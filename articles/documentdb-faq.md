<properties title="Frequently asked questions about DocumentDB" pageTitle="Frequently asked questions about DocumentDB | Azure" description="Answers to frequently asked questions about Azure DocumentDB databases. Learn about capacity and request units, and understand how to scale to your application needs." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, faq"   services="documentdb" solutions="data-management"   authors="bradsev" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# DocumentDB に関してよく寄せられる質問

## この記事の内容

-   [Azure DocumentDB の基礎][Azure DocumentDB の基礎]
-   [Azure DocumentDB のセットアップ][Azure DocumentDB のセットアップ]
-   [Azure DocumentDB に対する開発][Azure DocumentDB に対する開発]

## <span id="fundamentals"></span></a>Microsoft Azure DocumentDB の基礎

### Microsoft Azure DocumentDB とは

Microsoft Azure DocumentDB は、拡張性の高い NoSQL ドキュメントのサービスとしてのデータベースです。Microsoft Azure の機能と能力に支えられた管理プラットフォームによって、スキーマフリーのデータに高度なクエリを実行することができ、構成可能な信頼性の高いパフォーマンスの提供、および迅速な開発が可能となります。DocumentDB は、予測可能なスループット、遅延時間の短縮、およびスキーマフリー データ モデルが重要となる、Web アプリケーションやモバイル アプリケーションに適したソリューションです。DocumentDB では、ネイティブ JSON データ モデルでスキーマの柔軟性と豊富なインデックス作成機能が提供され、統合 JavaScript でマルチドキュメント トランザクションがサポートされます。

このサービスのデプロイと使用の手順については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]を参照してください。

### DocumentDB はどのような種類のデータベースですか?

DocumentDB は、JSON 形式でデータを格納する NoSQL ドキュメント指向のデータベースです。DocumentDB では、高度な DocumentDB SQL クエリ文法で照会できる入れ子になった自立的データ構造がサポートされます。DocumentDB では、ストアド プロシージャ、トリガー、およびユーザー定義関数を使用して、サーバー側 JavaScript のトランザクション処理を高いパフォーマンスで実行できます。また、開発者によって調整可能な一貫性レベルを、関連付けられたパフォーマンス レベルと共にサポートします。

### DocumentDB データベースは RDBMS のようなテーブルを保持しますか?

いいえ、DocumentDB は JSON ドキュメントのコレクション内にデータを格納します。DocumentDB のリソースの詳細については、「DocumentDB のリソース モデルと概念」という記事を参照してください。

### DocumentDB データベースはスキーマフリー データをサポートしますか?

はい、DocumentDB ではスキーマ定義またはヒントなしで、アプリケーションが任意の JSON ドキュメントを格納できます。DocumentDB SQL クエリ インターフェイスを使用したクエリに、データをすぐに利用できます。

### DocumentDB は ACID トランザクションをサポートしますか?

はい、DocumentDB では、JavaScript ストアド プロシージャおよびトリガーとして表現されるクロスドキュメント トランザクションをサポートします。トランザクションは、単一のコレクションを対象とし、他の同時実行されるコードおよびユーザー要求から完全に分離された ACID セマンティクスにより実行されます。JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。

### DocumentDB の一般的な使用事例にはどのようなものがありますか?

DocumentDB は、拡張性、パフォーマンス、およびスキーマフリー データでのクエリが重要である新しい Web アプリケーションやモバイル アプリケーションに適しています。DocumentDB は、迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。ユーザーが生成したコンテンツとデータを管理するアプリケーションは、DocumentDB の一般的な使用事例です。

### どのような拡張性と容量の制限がありますか?

各 Azure DocumentDB アカウントでは、Azure ポータルで構成できる容量単位の最大数をサポートします。追加の容量単位が必要な場合は、サポートに連絡して、アカウント クォータの増加を依頼してください。リソース クォータの詳細については、「DocumentDB Service Limits (DocumentDB サービスの制限)」という記事を参照してください。

### Microsoft Azure DocumentDB の料金はいくらですか?

詳細については、「[DocumentDB Preview Pricing Details (DocumentDB プレビュー料金の詳細)][DocumentDB Preview Pricing Details (DocumentDB プレビュー料金の詳細)]」を参照してください。

## <span id="setup"></span></a>Microsoft Azure DocumentDB のセットアップ

### どのようにして Microsoft Azure DocumentDB にサインアップしますか?

Microsoft Azure DocumentDB (プレビュー) は、新しい Azure プレビュー ポータルで利用可能です。最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。Microsoft Azure サブスクリプションにサインアップした後で、DocumentDB アカウントをギャラリー経由で Azure サブスクリプションに追加できます。

### マスター キーとは何ですか?

マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。マスター キーを配布するときには十分な注意が必要となります。

### どのようにしてデータベースを作成しますか?

DocumentDB SDK または REST API を使用して、データベースを作成できます。アプリケーションの開発方法の詳細については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]の開発のセクションを参照してください。

### コレクションとは何ですか?

コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。クエリおよびトランザクションの範囲としてコレクションが指定されます。多種多様な JSON ドキュメントのセットを 1 つのコレクション内に格納し、自動的にインデックスを作成することができます。

### データベースとコレクションに制限はありますか?

購入した容量単位ごとに、データベース ストレージとプロビジョニング済みスループットが割り当てられます。サービスで管理される、各リソースに対するクォータも用意されています。詳細については、「DocumentDB Service Limits (DocumentDB サービスの制限)」という記事を参照してください。

### どのようにしてユーザーおよびアクセス許可を設定しますか?

DocumentDB SDK または REST API を使用して、ユーザーおよびアクセス許可を作成できます。アプリケーションの開発方法の詳細については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]の開発のセクションを参照してください。 .

## <span id="develop"></span></a>Microsoft Azure DocumentDB に対する開発

### どのようにして DocumentDB に対する開発を開始しますか?

プレビューでは、.NET、Python、Node.js、および JavaScript に対する SDK が利用可能です。開発者は、RESTful HTTP API を利用して、さまざまなプラットフォームおよび言語から DocumentDB リソースとやり取りすることもできます。これらの SDK の使用方法の詳細については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]の開発のセクションを参照してください。

### DocumentDB は SQL をサポートしますか?

DocumentDB の SQL クエリ言語は、JavaScript ベースのユーザー定義関数 (UDF) により高度な階層型の関係演算子と機能拡張を提供します。JSON 文法では、JSON ドキュメントをツリー ノードとしてラベルが付けられたツリーとしてモデル化することができます。この事実は、DocumentDB の自動的なインデックス作成手法と DocumentDB の SQL クエリ方言の両方で利用されます。SQL 文法の使用方法の詳細については、「[Query DocumentDB (DocumentDB のクエリ)][Query DocumentDB (DocumentDB のクエリ)]」という記事を参照してください。

### DocumentDB でサポートされるデータ型は何ですか?

DocumentDB でサポートされるプリミティブ データ型は JSON と同じです。JSON には、文字列、数値 (IEEE754 倍精度)、ブール値 (true と false)、および Null で構成されるシンプルな型システムがあります。{ } 演算子で入れ子になったオブジェクトを作成し、[ ] 演算子で配列を作成することにより、さらに複雑なデータ型 (DateTime、Guid、Int64、Geometry など) を JSON と DocumentDB の両方で表現できます。

### DocumentDB はどのようにして同時実行を提供しますか?

DocumentDB では、HTTP エンティティ タグ (または ETag) でオプティミスティック同時実行制御 (OCC) をサポートします。すべての DocumentDB リソースには ETag があり、DocumentDB クライアントでは最新の読み取りバージョンが書き込み要求内に含まれます。ETag が最新である場合は、変更がコミットされます。値が外部で変更されている場合、サーバーは "HTTP 412 Precondition failure" 応答コードにより書き込みを拒否します。クライアントは最新バージョンのリソースを読み取り、要求を再試行する必要があります。

### どのようにして DocumentDB のトランザクションを実行しますか?

DocumentDB では、JavaScript ストアド プロシージャおよびトリガーを介して、統合された言語のトランザクションがサポートされます。スクリプト内のすべてのデータベース操作は、コレクションを対象としたスナップショット分離により実行されます。ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。JavaScript がエラーをスローした場合、トランザクションはロールバックされます。

### どのようにして DocumentDB にドキュメントを一括挿入しますか?

DocumentDB によるストアド プロシージャのサポートにより、一括挿入の効率的な手法を利用できます。ドキュメントの受け入れと挿入のためのシンプルな JavaScript ストアド プロシージャを開発することにより、一括挿入を実行できます。これには、一貫性のあるコレクションの状態を保持したまま、一括挿入をトランザクションとして実行できるという利点もあります。プログラミング モデルの詳細については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]の開発のセクションを参照してください。

### DocumentDB はリソース リンク キャッシュをサポートしますか?

はい、DocumentDB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。DocumentDB クライアントは、ドキュメントやコレクションなどリソースの読み取りに対して "If-None-Match" ヘッダーを指定でき、サーバー バージョンが変更された場合のみローカル コピーを更新できます。

  [Azure DocumentDB の基礎]: #fundamentals
  [Azure DocumentDB のセットアップ]: #setup
  [Azure DocumentDB に対する開発]: #develop
  [DocumentDB ドキュメント ページ]: http://go.microsoft.com/fwlink/p/?LinkID=402319
  [DocumentDB Preview Pricing Details (DocumentDB プレビュー料金の詳細)]: http://go.microsoft.com/fwlink/p/?LinkID=402317
  [Query DocumentDB (DocumentDB のクエリ)]: ../documentdb-sql-query/