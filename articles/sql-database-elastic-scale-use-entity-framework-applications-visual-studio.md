﻿<properties title="Using Elastic Scale with Entity Framework" pageTitle="Entity Framework での Elastic Scale の使用" description="Elastic Scale makes it easy to scale, Entity Framework is easy to use for coding databases " metaKeywords="Using Elastic Scale with Entity Framework, Azure SQL Database sharding, elastic scale, Entity Framework and Elastic Scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Entity Framework での Elastic Scale の使用 
 
Azure SQL Database Elastic Scale を Microsoft の Entity Framework (EF) で使用してアプリケーションを構築できます。Elastic Scale は、アプリケーションのデータ層のシャーディングとスケールアウトを通じて容量の拡張と縮小を実現します。このドキュメントでは、Elastic Scale の機能と統合するために Entity Framework アプリケーションに加える必要がある変更について説明します。ここでは、Entity Framework の **Code First** アプローチを使用して [Elastic Scale シャード管理](http://go.microsoft.com/?linkid=9862595)と[データ依存ルーティング](./sql-database-elastic-scale-data-dependent-routing.md)を構成する方法を重点的に説明します。このドキュメント全体の実際の例として、EF 向けの[新しいデータベースを対象とした Code First](http://msdn.microsoft.com/ja-jp/data/jj193542.aspx) のチュートリアルを参照してください。このドキュメントに付属するサンプル コードは、Visual Studio のコード サンプルの Elastic Scale のサンプルの一部です。
  
## サンプル コードのダウンロードと実行
この記事のコードをダウンロードするには:

* Visual Studio 2012 以降が必要です。 
* Visual Studio を起動します。 
* Visual Studio で、[ファイル] の [新しいプロジェクト] を選択します。 
* [新しいプロジェクト] ダイアログ ボックスで、**オンライン サンプル**の **[Visual C#]** に移動し、右上の検索ボックスに「elastic scale」と入力します。
    
    ![Entity Framework and elastic scale sample app][1] 

"**Elastic Scale with Azure SQL Database - Integrating with Entity Framework**" という名前のサンプルを選択します。ライセンスに同意すると、サンプルが読み込まれます。 

このサンプルを実行するには、Azure SQL Database で次の 3 つの空のデータベースを作成する必要があります。

* シャード マップ マネージャー データベース
* シャード 1 データベース
* シャード 2 データベース

これらのデータベースを作成した後、Azure SQL DB サーバー名、データベース名、データベースに接続するための資格情報を **Program.cs** 内のプレースホルダーに入力します。Visual Studio でソリューションをビルドします。Visual Studio によるビルド プロセスの一部として、Elastic Scale、Entity Framework、および一時的エラーの処理のための必要な NuGet パッケージがダウンロードされます。ソリューションで NuGet パッケージの復元が有効になっていることを確認します。この設定は、Visual Studio ソリューション エクスプローラーでソリューション ファイルを右クリックして有効にすることができます。 

##Entity Framework のワークフロー 

Entity Framework 開発者は、アプリケーションをビルドし、アプリケーション オブジェクトの持続性を確認する場合に、次の 4 つのワークフローのいずれかに依存します。 

* **Code First (新しいデータベース)**: EF 開発者がアプリケーション コードでモデルを作成した後、EF によってこのモデルからデータベースが生成されます。 
* **Code First (既存のデータベース)**: 開発者は、EF を使用して既存のデータベースからモデルのアプリケーション コードを生成します。
* **Model First**: 開発者が EF デザイナーでモデルを作成した後、EF によってこのモデルからデータベースが作成されます。
* **Database First**: 開発者は、EF ツールを使用して、既存のデータベースからモデルを推論します。 

これらの手法はすべて DbContext クラスに依存して、アプリケーションのデータベース接続とデータベース スキーマを透過的に管理します。後で詳しく説明しますが、DbContext 基本クラスのさまざまなコンストラクターにより、接続の作成、データベースのブートストラップ、スキーマの作成をさまざまなレベルで制御できます。このとき、主に、EF によって提供されるデータベース接続の管理機能が、Azure Database Elastic Scale によって提供されるデータ依存ルーティング インターフェイスの接続の管理機能と交差するという事実から、課題が生まれます。 

##Elastic Scale の前提条件 

用語の定義については、「[Elastic Scale 用語集](./sql-database-elastic-scale-glossary.md)」を参照してください。

Azure SQL Database Elastic Scale では、"シャードレット" と呼ばれる、アプリケーション データのパーティションを定義します。シャードレットはシャーディング キーによって識別され、特定のデータベースにマップされます。アプリケーションは、任意の数のデータベースを持つことができ、シャードレットを分散して現在のビジネス要件に対して十分な容量またはパフォーマンスを提供します。シャーディング キー値とデータベースとの間のマッピングは、Elastic Scale API によって提供されるシャード マップによって格納されます。この機能は、"シャードのマップの管理" (SMM) と呼ばれます。シャード マップは、シャーディング キーを格納する要求のデータベース接続用のためのブローカーとしても機能します。この機能は、"データ依存ルーティング" と呼ばれます。 
 
Elastic Scale のシャード マップ マネージャーは、同時シャードレット管理操作 (たとえば、データのシャード間の移動) の実行中に発生する可能性があるシャードレット データの一貫性のないビューからユーザーを保護します。これを実現するために、Elastic Scale のシャード マップは、Elastic Scale アプリケーションのデータベース接続を仲介します。これにより、接続が作成されているシャードレットがシャード管理操作の影響を与える可能性のあるときにシャード マップ機能によってデータベース接続を自動的に強制終了することができます。この方法は、EF の機能の一部 (たとえば、既存の接続から新しい接続を作成してデータベースの存在を確認する機能) と統合する必要があります。一般に、標準的な DbContext コンストラクターは EF の処理用に安全に複製できる閉じたデータベース接続に対してのみ確実に動作する、というのが従来の考え方でした。これに対し、Elastic Scale の設計上の原則は、開かれている接続のみを仲介することにあります。Elastic Scale によって仲介される接続を EF DbContext に渡す前に閉じればこの問題を解決できると考える読者もいることでしょう。ただし、接続を閉じた後、再び開く操作を EF に依存することで、Elastic Scale によって実行される検証と一貫性チェックが失われてしまいます。ただし、EF の移行機能は、これらの接続を使用して、アプリケーションに対して透過的な形で基になるデータベース スキーマを管理します。同じアプリケーション内でこれらの Elastic Scale の機能と EF の機能をそのまま組み合わせることができれば理想的です。次のセクションでは、これらのプロパティと要件について詳しく説明します。 


##要件 

Elastic Scale と Entity Framework API の両方を使用する場合は、次のプロパティを維持することを想定します。 

* **スケールアウト**: アプリケーションの容量要求に合わせて必要に応じてシャード化されたアプリケーションのデータ層のデータベースを追加または削除すること。これには、データベースの作成と削除を制御することに加え、Elastic Scale シャード マップ マネージャー API を使用してデータベースを管理したりシャードレットのマッピングを管理したりすることが含まれます。 

* **一貫性**: アプリケーションは、シャーディングを使用し、Elastic Scale のデータ依存ルーティング機能を使用します。破損や誤ったクエリ結果を回避するために、Elastic Scale シャード マップ マネージャーを通じて接続が仲介されます。これにより、検証と一貫性も維持されます。
 
* **Code First**: EF の Code First パラダイムの利便性を保つこと。Code First では、アプリケーションのクラスは基になるデータベース構造に透過的にマップされます。アプリケーション コードは、基になるデータベースの処理に関連するほとんどの側面をマスクする Dbset と対話します。
 
* **スキーマ**: Entity Framework は、初期のデータベース スキーマの作成とそれ以降のスキーマの展開を移行を通じて処理します。これらの機能を維持することで、データの進化に対して簡単にアプリを適合させることができます。 

Azure SQL Database Elastic Scale を使用してこれらの Code First アプリケーションの要件を満たす方法を次のガイダンスに示します。 

## EF DbContext を使用したデータ依存ルーティング 

一般に、Entity Framework でのデータベース接続は、**DbContext** のサブクラスを使用して管理します。これらのサブクラスは、**DbContext** から派生させて作成します。ここでは、データベースによってバックアップされる、アプリケーションの CLR オブジェクトのコレクションを実装する **DbSets** を定義します。データ依存ルーティングのコンテキストでは、他の Code First アプリケーション シナリオには必ずしも該当しない、次のようないくつかの有用なプロパティを識別できます。 

* データベースが既に存在し、Elastic Scale シャード マップに登録されている。 
* アプリケーションのスキーマが既にデータベースにデプロイされている (後で説明します)。 
* データベースへのデータ依存ルーティング接続は Elastic Scale シャード マップによって仲介される。 

スケールアウトのために **DbContexts** をデータ依存ルーティングと統合するには: 

1. シャード マップ マネージャーの Elastic Scale インターフェイスを介して物理的なデータベース接続を作成します。 
2. **DbContext** サブクラスを使用して接続をラップします。
3. EF の側でもすべての処理が発生することを確認するために、**DbContext** 基本クラスに接続を渡します。 

この方法を次のコード例に示します (このコードは付属の Visual Studio プロジェクトにも含まれています)。

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    ...

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

#### 要点
* DbContext サブクラスの既定のコンストラクターが新しいコンストラクターで置き換えられます。 
* 新しいコンストラクターは、データ依存ルーティングに必要な次の引数を Elastic Scale 経由で受け取ります。 
    * データ依存ルーティング インターフェイスにアクセスするためのシャード マップ 
    * シャードレットを識別するためのシャーディング キー 
    * シャードへのデータ依存ルーティング接続に使用する資格情報を含む接続文字列 
* 基本クラス コンストラクターの呼び出しは、データ依存ルーティングに必要なすべての手順を実行する静的メソッドに迂回します。 
    * コンストラクターが、シャード マップ上で Elastic Scale インターフェイスの **OpenConnectionForKey** 呼び出しを使用して、開いた接続を確立します。 
    * シャード マップにより、特定のシャーディング キーのシャードレットを保持するシャードへの開いた接続が作成されます。
    * この開いた接続が再び **DbContext** の基本クラス コンストラクターに渡され、EF が新しい接続を自動的に作成する代わりにこの接続を使用することが示されます。このように Elastic Scale によって接続がタグ付けされ、シャード マップ管理操作での一貫性が保証されます。  
  
コードの既定のコンストラクターに代えて、新しいコンストラクターを DbContext サブクラスに使用します。たとえば次のようになります。 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     ... 
    }

新しいコンストラクターは、**tenantid1** の値によって識別されるシャードレットのデータを保持するシャードへのデータベース接続を開きます。**using** ブロック内のコードは変更されておらず、**tenantid1** のシャード上で EF を使用してブログの **DbSet** にアクセスします。これにより、using ブロック内のコードのセマンティクスが変更され、**tenantid1** が保持されている 1 つのシャードにすべてのデータベース操作のスコープが設定されます。たとえば、ブログ **DbSet** に対する LINQ クエリを実行した場合、現在のシャードに格納されているブログのみが返され、他のシャードに格納されているブログは返されません。  

####一時的エラーの処理
Microsoft Patterns & Practices チームでは、[The Transient Fault Handling Application Block](http://msdn.microsoft.com/ja-jp/library/dn440719(v=pandp.60).aspx) を公開しています。このライブラリは、EF と組み合わせて使用する Elastic Scale で使用します。ただし、ここで調整したコンストラクターを使用してすべての新しい接続試行が行われるようにするには、一時的エラーの後に新しいコンストラクターが使用されることが保証される位置にすべての一時的な例外から制御が返されるようにします。そうでないと、正しいシャードへの接続が保証されず、シャード マップに対する変更が発生したときに接続が保持される保証がなくなります。 

次のコード サンプルは、新しい **DbContext** サブクラスのコンストラクターの周囲でどのように SQL 再試行ポリシーを使用できるかを示しています。 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            ... 
            } 
        }); 

上記のコードの **SqlDatabaseUtils.SqlRetryPolicy** は **SqlDatabaseTransientErrorDetectionStrategy** として定義され、再試行回数が 10 回、再試行間の待機時間が 5 秒に設定されています。この方法は、EF とユーザーによって開始されたトランザクションのガイダンスに一致します (「[Limitations with Retrying Execution Strategies (EF6 onwards) (再試行実行戦略の制限 (EF6 以降))](http://msdn.microsoft.com/ja-jp/data/dn307226)」を参照してください)。トランザクションを開き直す場合、または (示されているように) Elastic Scale ライブラリを使用する適切なコンストラクターからコンテキストを再作成する場合のどちらの状況でも、一時的な例外から制御が返されるスコープをアプリケーション プログラムが制御する必要があります。  

上のコード例は、アプリケーションが Elastic Scale のデータ依存ルーティングを Entity Framework で使用するために既定のコンストラクターを書き直す必要があることを示しています。次の表では、この方法を他のコンストラクター向けに一般化しています。 


現在のコンストラクター | データ用に書き直されたコンストラクター | 基本コンストラクター | メモ
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) | 接続は、シャード マップとデータ依存ルーティング キーの機能である必要があります。EF による自動的な接続の作成を回避し、代わりにシャード マップを使用して接続を仲介する必要があります。 
MyContext(string)|ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) | 接続は、シャード マップとデータ依存ルーティング キーの機能です。シャード マップによる検証が回避されるため、固定されたデータベース名または接続文字列は機能しません。 
MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) | 接続は、モデルが提供された特定のシャード マップとシャーディング キーに対して作成されます。コンパイルされたモデルは、基本コンストラクターに渡されます。
MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) | 接続をシャード マップとキーから推論する必要があります。接続を入力として渡すことはできません (入力で既にシャード マップとキーが使用されていた場合を除きます)。ブール値が渡されます。 
MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) | 接続をシャード マップとキーから推論する必要があります。接続を入力として渡すことはできません (入力でシャード マップとキーが使用されていた場合を除きます)。コンパイルされたモデルが渡されます。 
MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) | 新しいコンストラクターは、入力として渡された ObjectContext 内のすべての接続が、Elastic Scale によって管理される接続に再ルーティングされることを保証する必要があります。ObjectContexts の詳しい説明は、このドキュメントの範囲を超えているため省略します。
MyContext(DbConnection, DbCompiledModel,bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool)| DbContext(DbConnection, DbCompiledModel, bool); | 接続をシャード マップとキーから推論する必要があります。接続を入力として渡すことはできません (入力で既にシャード マップとキーが使用されていた場合を除きます)。モデルとブール値が基本クラス コンストラクターに渡されます。 

###EF の移行を通じたシャード スキーマのデプロイ 

自動スキーマ管理は、Entity Framework によって提供される便利な機能です。Elastic Scale アプリケーションのコンテキストでは、この機能を保持して、データベースがシャード化されたアプリケーションに追加されたときにスキーマを新しく作成されたシャードに自動的にプロビジョニングできるようにします。基本的なユース ケースは、EF を使用するシャード化されたアプリケーションのためにデータ層の容量を増やすことです。EF のスキーマ管理機能に依存することで、EF 上に構築されたシャード化されたアプリケーションでのデータベースの管理に伴う労力を減らすことができます。 

EF の移行を通じたスキーマのデプロイは、**開かれていない接続**に最適です。これは、Elastic Scale によって提供される、開かれた接続に依存するデータ依存ルーティングのシナリオとは対照的です。もう 1 つの違いは、一貫性の要件にあります。シャード マップの同時操作から保護するためにすべてのデータ依存ルーティング接続の一貫性を保証するのは望ましいことですが、まだシャード マップに登録されておらず、シャードレットを保持するために割り当てられていない新しいデータベースへの初期スキーマ デプロイメントでは、これは問題になりません。そこで、このシナリオでは、データ依存ルーティングではなく、通常のデータベース接続に依存することができます。  

これは、EF の移行を通じたスキーマのデプロイメントと、アプリケーションのシャード マップにおけるシャードとしての新しいデータベースの登録とが密接に結び付いたアプローチになります。これは、次の前提条件に依存します。 

* データベースが既に作成されている。 
* データベースが空で、ユーザー スキーマやユーザー データが含まれない。
* データ依存ルーティングに使用される Elastic Scale API を介してデータベースにまだアクセスできない。 

これらの前提条件の下で、通常のまだ開かれていない **SqlConnection** を作成して、スキーマ デプロイメントのための EF の移行を開始できます。次のコード サンプルに、この方法を示します。 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

この例は、**RegisterNewShard** メソッドを示しています。このメソッドは、シャードを Elastic Scale シャード マップに登録し、EF の移行を通じてスキーマをデプロイし、さらにシャーディング キーとシャードのマッピングを格納します。このメソッドは、SQL 接続文字列を入力として受け取る、**DbContext** サブクラスのコンストラクター (サンプルの **ElasticScaleContext**) に依存しています。次の例に示すように、このコンストラクターのコードは簡単です。 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
基本クラスから派生したコンストラクターのバージョンを使用したことのある読者もいることでしょう。ただし、このコードでは、接続するときに EF の既定の初期化子が使用されるようにする必要があります。そこで、接続文字列を使用して基本クラスのコンストラクターを呼び出す前に、静的メソッドに迂回します。EF の初期化子の設定が競合しないように、シャードの登録を異なるアプリ ドメインまたはプロセスで実行する必要があることに注目してください。 


##制限事項 

このドキュメントで説明した方法には、いくつかの制限事項があります。 

* **LocalDb** を使用する EF アプリケーションは、Elastic Scale を使用する前に通常の SQL Server データベースに移行する必要があります。Elastic Scale によるシャーディングを使用したアプリケーションのスケーリングは、**LocalDb** では実行できません。開発では引き続き **LocalDb** を使用できます。 

* データベース スキーマの変更を暗示するすべてのアプリケーションに対する変更は、すべてのシャードで EF の移行を介する必要があります。このドキュメントのサンプル コードには、この方法を示していません。Update-Database を ConnectionString パラメーターと共に使用してすべてのシャードに対して反復処理を行うことを検討してください。または、Update-Database を -Script オプションと共に使用して保留中の移行の T-SQL スクリプトを抽出し、その T-SQL スクリプトをシャードに適用します。  

* 要求を受け取った場合、要求によって提供されたシャーディング キーで識別される 1 つのシャード内にそのすべてのデータベース処理が含まれると見なされます。ただし、この仮定が常に正しいとは限りません。たとえば、シャーディング キーを使用可能にできないことがあります。これに対処するために、複数のシャードに対してクエリを実行するための接続の抽象化を実装する **MultiShardQuery** クラスが Elastic Scale ライブラリに含まれています。EF と組み合わせた **MultiShardQuery** の使用方法については、このドキュメントの範囲を超えているため省略します。



##まとめ 

Entity Framework アプリケーションは、Azure SQL Database Elastic Scale のメリットを簡単に活用できます。このドキュメントで説明した手順に従って EF アプリケーションで使用されている **DbContext** サブクラスのコンストラクターをリファクタリングすることにより、EF アプリケーションで Elastic Scale の機能をデータ依存ルーティングに利用できるようになります。これにより、必要な変更が、**DbContext** クラスが既に存在している場所に制限されます。さらに、EF アプリケーションは、新しいシャードと、必要な EF の移行を伴う手順と、新しいシャードとマッピングの Elastic Scale シャード マップへの登録とを組み合わせることによって、自動スキーマ デプロイメントのメリットを引き続き得ることができます。 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-using-entity-framework/sample.png