<properties 
   pageTitle="Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発 | Azure" 
   description="Data Lake Tools for Visual Studio のインストール方法と、U-SQL スクリプトの開発およびテスト方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Data Lake Tools for Visual Studio をインストールし、それを使用して U-SQL スクリプトを作成およびテストする方法を説明します。

U-SQL は、Data Lake 内のすべてのデータを準備、変換、分析するための拡張性に優れた言語です。詳細については、[U-SQL リファレンス](http://go.microsoft.com/fwlink/p/?LinkId=691348)に関するページを参照してください。


**前提条件**

- **Visual Studio 2015、Visual Studio 2013 Update 4、または Visual Studio 2012 (Visual C++ インストール済み)** 
- **Microsoft Azure SDK for .NET バージョン 2.5 以上**。[Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用してインストールします。
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**。 

    Data Lake Tools for Visual Studio がインストールされると、Visual Studio に [Data Lake] メニューが表示されます。
    
    ![U-SQL Visual Studio のメニュー](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **「[Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」の次の 2 つのセクションを実行します**。

	- [Create an Azure Data Lake Analytics account (Azure Data Lake Analytics アカウントの作成)](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)
	- [Upload SearchLog.tsv to the default Data Lake Storage account (既定の Data Lake ストレージ アカウントへの SearchLog.tsv のアップロード)](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)

	Data Lake Tools では、Data Lake Analytics アカウントの作成はサポートされません。そのため、Azure プレビュー ポータル、Azure PowerShell、.NET SDK、または Azure CLI を使用して作成する必要があります。Data Lake Analytics ジョブを実行するには、いくつかのデータが必要です。Data Lake Tools でデータのアップロードがサポートされていても、このチュートリアルに従いやすくするため、サンプル データのアップロードにはポータルを使用します。

## Azure への接続

**Data Lake Analytics に接続するには**

1. Visual Studio を開きます。
2. **[U-SQL]** メニューの **[オプションと設定]** をクリックします。
4. **[サインイン]** をクリックします。他のユーザーがサインインしている場合は、**[ユーザーの変更]** をクリックし、指示に従ってサインインします。
5. **[OK]** をクリックして、[オプションと設定] ダイアログ ボックスを閉じます。

**Data Lake Analytics アカウントを参照するには**

1. Visual Studio で、**Ctrl + Alt + S** キーを押して、**サーバー エクスプローラー**を開きます。
2. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]** の順に展開します。Data Lake Analytics アカウントが複数ある場合は、そのリストが表示されます。Visual Studio で Data Lake Analytics アカウントを作成することはできません。アカウントを作成する場合は、「[Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」または「[Azure PowerShell で Azure Data Lake Analytics の使用を開始する](knoa-get-started-powershell.md)」を参照してください。

## ソース データ ファイルのアップロード

「**前提条件**」セクションでデータをアップロードしてあります。

独自のデータを使用したい場合は、以下の手順を使用して Data Lake Tools からデータをアップロードします。

**使用する Azure Data Lake アカウントにファイルをアップロードするには**

1. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]**、使用する Data Lake Analytics アカウント、**[ストレージ アカウント]** の順に展開します。既定の Data Lake ストレージ アカウント、リンクされている Data Lake ストレージ アカウント、リンクされている Azure ストレージ アカウントが表示されます。既定の Data Lake アカウントには、「既定のストレージ アカウント」と表示されます。
2. 既定の Data Lake ストレージ アカウントを右クリックし、**[エクスプローラー]** をクリックします。Data Lake Tools for Visual Studio Explorer ウィンドウが開きます。左側にツリー ビューが、右側にコンテンツ ビューが表示されます。
3. ファイルをアップロードするフォルダーを参照します。 
4. 空白部分を右クリックし、**[アップロード]** をクリックします。 

	![U-SQL Visual Studio プロジェクト U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Azure BLOB ストレージ アカウントにファイルをアップロードするには**

1. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]**、使用する Data Lake Analytics アカウント、**[ストレージ アカウント]** の順に展開します。既定の Data Lake ストレージ アカウント、リンクされている Data Lake ストレージ アカウント、リンクされている Azure ストレージ アカウントが表示されます。 
2. Azure ストレージ アカウントを展開します。
3. ファイルをアップロードするコンテナーを右クリックして、**[エクスプローラー]** をクリックします。
4. ファイルをアップロードするフォルダーを参照します。 
5. 空白部分を右クリックし、**[アップロード]** をクリックします。 

## U-SQL スクリプトの作成とテスト 

Data Lake Analtyics ジョブは U-SQL 言語で記述されます。U-SQL の詳細については、「[Get started with U-SQL language (U-SQL 言語の概要)](data-lake-analytics-u-sql-get-started.md)」および「[U-SQL language reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

**Data Lake Analytics ジョブを作成して送信するには**

1. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
2. **[U-SQL プロジェクト]** タイプを選択します。

	![新しい U-SQL Visual Studio プロジェクト](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. **[OK]** をクリックします。Visual Studio で、**Script.usql** ファイルを含むソリューションが作成されます。
4. 次のスクリプトを **Script.usql** に入力します。

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。
    
    ソース ファイルを別の場所にコピーしたのでない限り、2 つのパスを変更しないでください。出力フォルダーが存在しない場合、Data Lake Analytics によって作成されます。
	
	既定の Data Lake アカウントに格納されたファイルの相対パスを使用する方が簡単です。絶対パスを使用することもできます。たとえば、次のように入力します。
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。リンクされた Azure ストレージ アカウントに格納されているファイルの構文は以下のとおりです。
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB コンテナーは、現在サポートされていません。

	次の機能に注意してください。

	- **IntelliSense**
	 
		自動的に作成された名前とメンバーが、行セット、クラス、データベース、スキーマ、ユーザー定義オブジェクト (UDO) に対して表示されます。
		 
		カタログ エンティティ (データベース、スキーマ、テーブル、UDO など) に対する IntelliSense は、コンピューティング アカウントと関連付けられています。現在のアクティブなコンピューティング アカウント、データベース、スキーマを上部のツールバーで確認でき、ドロップダウン リストで切り替えることができます。
 
	- **オート フォーマット**
	
		[編集] -> [詳細設定] でコードの構造に基づいて Scope スクリプトのインデントを変更できます。
  
		- ドキュメントのフォーマット (Ctrl + E、D): ドキュメント全体をフォーマットします   
		- 選択範囲のフォーマット (Ctrl + K、Ctrl + F): 選択範囲をフォーマットします。範囲が選択されていない場合、このショートカットはカーソルがある行をフォーマットします。  
		
		すべてのフォーマット ルールは、[ツール] -> [オプション] -> [テキスト エディター] -> [SIP] -> [フォーマット] で構成できます。  
	- **スマート インデント**
	 
		スクリプトの作成中に、Data Lake Tools for Visual Studio は式を自動的にインデントできます。この機能は既定では無効になっているので、ユーザーは [U-SQL] -> [オプションと設定] -> [スイッチ] -> [スマート インデントを有効にする] をオンにして有効にする必要があります。

	- **定義に移動してすべての参照を検索**
	
		行セット/パラメーター/列/UDO などの名前を右クリックし、[定義へ移動] (F12) をクリックすると、その定義に移動できます。[すべての参照の検索] (Shift + F12) をクリックすると、すべての参照が表示されます。

	- **Azure パスの挿入**
		
		Azure のファイル パスを憶えていてスクリプトを作成するときに手動で入力しなくても、Data Lake Tools for Visual Studio では、エディターを右クリックして [Azure パスの挿入] をクリックするだけで簡単に挿入できます。[Azure BLOB ブラウザー] ダイアログでファイルに移動します。[OK] ボタンをクリックすると、ファイル パスがコードに挿入されます。

5. Data Lake Analytics アカウント、データベース、スキーマを指定します。

	![U-SQL Visual Studio プロジェクトの送信](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    詳細については、「[U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。

5. **ソリューション エクスプローラー**で、**Script.usql** を右クリックしてから **[スクリプトのビルド]** をクリックします。[出力] ペインで結果を確認します。
6. **ソリューション エクスプローラー**で、**Script.usql** を右クリックしてから **[スクリプトの送信]** をクリックします。または、Script.usql のウィンドウから **[送信]** をクリックすることもできます。先のスクリーンショットをご覧ください。詳細オプションを使用して送信するには、[送信] ボタンの横にある下矢印をクリックします。
7. **[ジョブ名]** を指定し、**[Analytics アカウント]** を確認してから、**[送信]** をクリックします。送信が完了すると、Data Lake Tools for Visual Studio の [結果] ウィンドウに送信結果とジョブのリンクが示されます。

	![U-SQL Visual Studio プロジェクトの送信](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. 最新のジョブの状態を表示して、画面を更新するには、[更新] をクリックする必要があります。ジョブが成功すると、**[ジョブ グラフ]**、**[メタ データ操作]**、**[状態の履歴]**、**[診断]** が表示されます。

	![U-SQL Visual Studio Data Lake Analytics ジョブ パフォーマンス グラフ](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

	* [ジョブの概要]。現在のジョブの概要情報が表示されます (状態、進行状況、実行時間、ランタイムの名前、送信者など)。   
	* [ジョブの詳細]。このジョブに関する詳細情報が表示されます (スクリプト、リソース、頂点実行ビューなど)。
	* [ジョブ グラフ]。ジョブの情報が 4 つのグラフで視覚的に提供されます (進行状況、データの読み取り、データの書き込み、実行時間、ノードごとの平均実行時間、入力スループット、出力スループット)。
	* [メタデータ操作]。すべてのメタデータ操作が表示されます。
	* [状態の履歴]。 
	* [診断]。Data Lake Tools for Visual Studio がジョブの実行を自動的に診断します。エラーやパフォーマンスの問題がジョブにある場合、アラートが表示されます。詳細についてはジョブの診断 (リンクは TBD) を参照してください。 
	
**ジョブの状態を確認するには**

1. サーバー エクスプローラーで、**[Azure]**、**[Data Lake Analytics]**、Data Lake Analytics アカウント名の順に展開します。
2. **[ジョブ]** をダブルクリックしてジョブを一覧表示します。
2. 状態を確認するにはジョブをクリックします。

**ジョブの出力を表示するには**

1. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]**、ご使用の Data Lake Analytics アカウント、**[ストレージ アカウント]** の順に展開し、既定の Data Lake ストレージ アカウントを右クリックしてから **[エクスプローラー]** をクリックします。 
2.  **[出力]** をダブルクリックしてフォルダーを開きます。
3.  **SearchLog-From-adltools.csv** をダブルクリックします。


###ジョブの再生 

ジョブの再生を使用すると、ジョブ実行の進行状況を監視し、視覚的にパフォーマンス上の問題やボトルネックを検出できます。この機能は、ジョブの実行が完了する前 (つまり、ジョブの実行中) だけでなく実行完了後にも使用できます。ジョブの実行中に再生を行うと、現時点までの進行状況を再生できます。

**ジョブの実行の進行状況を表示するには**

1. 右上隅にある **[プロファイルの読み込み]** をクリックします。前のスクリーンショットを参照してください。
2. 左下隅の [再生] ボタンをクリックすると、ジョブの実行の進行状況が表示されます。 
3. 再生中に **[一時停止]** をクリックすると停止し、進行状況バーを特定の位置にドラッグすると直接そこに移動します。 


###ヒート マップ 

Data Lake Tools for Visual Studio にはユーザーが選択できるカラー オーバーレイがあり、各ステージの進行状況、データ I/O、実行時間、I/O スループットがジョブ ビューに表示されます。これにより、ジョブのプロパティの潜在的な問題と分布が直接かつ直観的にわかります。ドロップダウン リストから表示するデータ ソースを選択できます。



##関連項目

さまざまなツールを使用して Data Lake Analytics の使用を開始する方法については、以下を参照してください。

- [Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
- [.NET SDK で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-net-sdk.md)

以下の開発トピックも参照してください。

- [Analyze weblogs using Data Lake Analytics (Data Lake Analytics を使用したウェブログの分析)](data-lake-analytics-analyze-weblogs.md)
- [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
- [Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->