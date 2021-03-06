<properties 
	pageTitle="Azure SQL エラスティック データベース プールのリファレンス" 
	description="このリファレンスでは、エラスティック データベース プールの記事やプログラミング情報のリンクと詳細が提供されています。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/08/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# SQL Database エラスティック データベース プールのリファレンス

数十台、数百台、何千ものデータベースを持つ SaaS 開発者にとっては、エラスティック データベース プールを使用することで、プロセスの作成、保守、データベースのグループ全体にわたるパフォーマンスとコストの両方の管理が簡素化されます。

このリファレンスでは、エラスティック データベース プールの記事やプログラミング情報のリンクと詳細が提供されています。

## 概要

エラスティック データベース プールは、エラスティック データベース スループット単位 (eDTU) と、複数のデータベースで共有されるストレージ (GB) のコレクションです。エラスティック データベースは、いつでも、プールに追加したり、プールから削除したりできます。プール内のエラスティック データベースでは、使用可能なリソースを解放するプールから必要なリソースのみが、それを必要とするアクティブなデータベース用にのみ使用されます。お使いのデータベースで、エラスティック データベース プールが役立つかどうかを判断する際の参考として、「[Price and performance considerations for an elastic database pool (エラスティック データベース プールの価格とパフォーマンスの考慮事項)](sql-database-elastic-pool-guidance.md)」をご覧ください。



## エラスティック データベース プールを作成および管理するための前提条件


- エラスティック データベース プールは、Azure SQL Database V12 サーバーでのみ使用できます。   
- エラスティック データベース プールの作成と管理は、[プレビュー ポータル](https://portal.azure.com)、PowerShell、Azure リソース マネージャーの .NET クライアント ライブラリ (REST API のラッパー) のみでの使用に対応しています。[ポータル](https://manage.windowsazure.com/)とサービス管理コマンドはサポートされません。
- さらに、新しいエラスティック データベースの作成と、既存のデータベースのプールの内外への移動は、Transact-SQL を使用してサポートされています。



## 現行のプレビューの考慮事項


- プールごとにデータベースとプール eDTUs の最大数があります。

    | サービス階層 | プールあたりの最大データベース数* | プールあたりの最大 eDTU* |
    | :-- | :-- | :-- |
    | 基本 | 200 | 1200 | 
    | Standard | 200 | 1200 |
    | Premium | 50 | 1500 |

    ****プールあたりのデータベースと数とプール eDTU の数の現在の上限は増えることが予想されます。***




## 記事の一覧

次の記事は、エラスティック データベースとエラスティック ジョブを使い始める時に役立ちます。

| 記事 | 説明 |
| :-- | :-- |
| [SQL Database エラスティック データベース プール](sql-database-elastic-pool.md) | エラスティック データベース プールの概要 |
| [価格とパフォーマンスの考慮事項](sql-database-elastic-pool-guidance.md) | エラスティック データベース プールの利用のコスト効率を評価する方法 |
| [Azure ポータルを使用した SQL Database エラスティック データベース プールの作成と管理](sql-database-elastic-pool-portal.md) | Azure ポータルを使ったエラスティック データベース プールの作成と管理方法 |
| [PowerShell を使用した SQL Database エラスティック データベース プールの作成と管理](sql-database-elastic-pool-powershell.md) | PowerShell コマンドレットを使ったエラスティック データベース プールの作成と管理方法 |
| [Azure SQL Database Library for .NET を使用した SQL Database の作成と管理](sql-database-elastic-pool-powershell.md) | C# を使ったエラスティック データベース プールの作成と管理方法 |
| [エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md) | プール内のすべてのエラスティック データベース間で T-SQL スクリプトを実行できるようにする、エラスティック ジョブ サービスの概要 |
| [エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md) | エラスティック データベース ジョブ サービスのインストール方法 |
| [エラスティック ジョブ サービスに必要なユーザーの作成](sql-database-elastic-jobs-add-logins-to-dbs.md) | エラスティック データベース ジョブのスクリプトを実行するには、プール内のすべてのデータベースに適切なアクセス許可を持つユーザーを追加する必要があります。 |
| [エラスティック データベース ジョブ コンポーネントのアンインストール方法](sql-database-elastic-jobs-uninstall.md) | エラスティック データベース ジョブ サービスをインストールしようとするときに発生するエラーからの回復 |



## 名前空間とエンドポイントの詳細
エラスティック データベース プールは、Microsoft Azure SQL Database の “ElasticPool” タイプの Azure リソース マネージャー リソースです。

- **名前空間**: Microsoft.Sql/ElasticPool
- REST API 呼び出しの**管理エンドポイント** (リソース マネージャー): https://management.azure.com



## エラスティック データベース プールのプロパティ

| プロパティ | 説明 |
| :-- | :-- |
| creationDate | プールが作成された日付。 |
| databaseDtuMax | プール内の 1 つのデータベースによって使用可能な eDTU の最大数。データベースの eDTU の最大値はリソース保証ではありません。eDTU の最大値はプール内のすべてのデータベースに適用されます。 |
| databaseDtuMin | プール内の 1 つのデータベースで保証される eDTU の最小数。データベースの eDTU の最小値 は 0 に設定されることがあります。eDTU の最小値はプール内のすべてのデータベースに適用されます。プール内のデータベース数の積とデータベースの eDTU の最小値はプール自体の eDTU を超えることはできません。 |
| DTU | プール内のすべてのデータベースで共有されている eDTU 数。 |
| edition | プールのサービス階層。プール内のすべてのデータベースが、このエディションです。 |
| elasticPoolId | プールのインスタンスの GUID。 |
| elasticPoolName | プールの名前。名前は、その親サーバーを基準にして一意です。 |
| location | プールが作成されたデータ センターの場所。 |
| state | サブスクリプションの請求書の支払いが延滞すると、状態は「無効」になり、それ以外の場合は「準備完了」になります。 |
| storageMB | プールの記憶域上限 (MB) です。プール内の 1 つのデータベースは、Standard Edition 記憶域の上限 (250 GB) まで使用できますが、プール内のすべてのデータベースによって使用されるストレージの合計がこのプールの制限を超えることはできません。 |


## エラスティック プールとエラスティック データベースの eDTU と記憶域の上限

プールの記憶域の上限はプールの eDTU の量によって決まります。

| プロパティ | 基本 | 標準 | Premium |
| :-- | :-- | :-- | :-- |
| dtu | **100**、200、400、800、1200 | **100**、200、400、800、1200 | **125**、250、500、1000、1500 |
| databaseDtuMax | **5** | 10、20、50、**100** | **125**、250、500、1000 |
| databaseDtuMin | **0**、5 | **0**、10、20、50、100 | **0**、125、250、500、1000 |
| storageMB* | **10000 MB**、20000 MB、40000 MB、 80000 MB、120000 MB | **100 GB**、200 GB、400 GB、800 GB、1200 GB | **62.5 GB**、125 GB、250 GB、500 GB、750 GB |
| DTU あたりのストレージ | 100 MB | 1 GB | 0\.5 GB |
| プールあたりの最大データベース数 | 200 | 200 | 50 |

既定値は**太字**です。

**API の単位は GB ではなく MB です。





## ワーカーとセッションの制限

同時実行ワーカーとエラスティック プール内のすべてのデータベースに対してサポートされている同時実行セッションの最大数は、プールの eDTU 設定に依存します。

| eDTU | 最大同時実行ワーカー数 | 最大同時セッション数 |
| :-- | :-- | :-- |
| 100 (Basic/Standard)、125 (Premium) | 200 | 2,400 |
| 200 (Basic/Standard)、250 (Premium) | 400 | 4,800 |
| 400 (Basic/Standard)、500 (Premium) | 800 | 9,600 |
| 800 (Basic/Standard)、1,000 (Premium) | 1,600 | 19,200 |
| 1,200 (Basic/Standard)、1,500 (Premium) | 2,400 | 28,800 |


## Azure リソース マネージャー の制限事項

Azure SQL Database V12 サーバーは、リソース グループにあります。

- 各リソース グループには、最大で 800 台のサーバーを含めることができます。
- 各サーバーには、最大で 800 個のエラスティック プールを含めることができます。


## エラスティック プール操作の待機時間

- 通常、データベースあたりに保証される eDTU (databaseDtuMin) またはデータベースあたりの最大 eDTU (databaseDtuMax) の変更は、5 分以内で完了します。 
- プールの eDTU や記憶域の上限 (storageMB) の変更は、プール内のすべてのデータベースで使用される領域の合計に依存します。変更の平均時間は、100 GB あたり 90 分以下です。たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールの eDTU や記憶域上限の変更にかかる想定待機時間は、3 時間以下になります。 



## PowerShell、REST API、.NET クライアント ライブラリ

エラスティック プールの作成と管理には、いくつかの PowerShell コマンドレットと REST API コマンドを使用できます。詳細とコード例については、「[PowerShell を使用したSQL Database のエラスティック データベース プールの作成と管理](sql-database-elastic-pool-powershell.md)」と「[C# で SQL Database を作成し、管理する](sql-database-client-library.md)」をご覧ください。

> [AZURE.IMPORTANT]Azure PowerShell 1.0 Preview のリリースから、Switch-AzureMode コマンドレットは利用できなくなりました。また、Azure ResourceManger モジュールで使用されていたコマンドレットは名前が変更されました。詳細については、[Azure PowerShell での Switch-AzureMode の廃止](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)に関するページを参照してください。

| [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt163521.aspx) | [REST API のコマンド](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [エラスティック データベース プールを作成します](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [エラスティック データベース プールのパフォーマンス設定を設定します](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [エラスティック データベース プールを削除します](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [エラスティック データベース プールとそのプロパティ値を取得します](https://msdn.microsoft.com/JA-JP/library/mt163646.aspx) |
| [Get-AzureRMSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [エラスティック データベース プールの操作の状態を取得します](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRMSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [エラスティック データベース プールのデータベースを取得します](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRMSqlElasticPoolDatabaseActivity]() | [プールへ、またはプールからのデータベースの移動の状態を取得します](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

TRANSACT-SQL を使用して、次のエラスティック データベース管理タスクを実行できます。

| タスク | 詳細 |
| :-- | :-- |
| 新しいエラスティック データベースを作成する (プール内で直接) | [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) |
| 既存のデータベースをプールの内外に移動する | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| プールのリソース使用状況の統計を取得する | [sys.elastic\_pool\_resource\_stats (Azure SQL Database)](https://msdn.microsoft.com/library/mt280062.aspx) |


## 課金と価格情報

エラスティック データベース プールは、次の特性ごとに課金されます。

- エラスティック プールは、プールにデータベースがない場合でも、その作成時に課金されます。 
- エラスティック プールは 1 時間ごとに課金されます。これは、シングル データベースのパフォーマンス レベルと同じ使用状況測定の頻度です。 
- エラスティック プールが新しい eDTU 量にサイズ変更されると、サイズ変更操作が完了するまでは新しい eDTU 量に応じた課金はされません。これは、スタンドアロン データベースのパフォーマンス レベルを変更する場合と同様のパターンに従っています。 


- エラスティック プールの価格は、プールの eDTU 数に基づきます。エラスティック プールの価格は、内部のエラスティック データベースの使用率とは関係ありません。
- 価格は、(プールの eDTU 数) x (eDTU あたりの単価) で計算されます。

エラスティック プールの eDTU 単価は、同じサービス階層のスタンドアロン データベースの DTU 単価よりも高くなります。詳細については、「[SQL Database の価格](http://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

## エラスティック データベース プールのエラー

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | エラスティック プールが、その記憶域の上限に達しました。エラスティック プールの記憶域の使用率が (%d) MB を超えることはできません。 | エラスティック プール領域の上限 (MB) です。 | エラスティック プールの記憶域が上限に達したときに、データベースにデータを記述しようとしています。 | 可能であれば、エラスティック プールの DTU を増やして、その記憶域の上限を上げることを検討する、エラスティック プール内の個々のデータベースで使用される記憶域を減らす、エラスティック プールからデータベースを削除するのいずれかを行ってください。 |
| 10929 | EX_USER | %S の最低限保証は %d、最大値は %d 、データベースの現在の使用状況は %d です。ただし、サーバーは現在ビジー状態であり、このデータベースの %d を超える要求をサポートできません。詳細については、[http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) をご覧ください。それ以外の場合は、後でもう一度やり直してください。 | データベースあたりの DTU の最小値、データベースあたりの DTU の最大値 | エラスティック プール内のすべてのデータベース間での同時実行ワーカー (要求) の合計数が、プールの制限を超えようとしました。 | 可能であれば、エラスティック プールの DTU を増やしてワーカーの上限を上げることを検討するか、エラスティック プールからデータベースを削除してください。 |
| 40844 | EX_USER | サーバー '%ls' のデータベース '%ls' は、エラスティック プールの '%ls' エディションのデータベースであり、連続コピー リレーションシップを持つことはできません。 | データベース名、データベースのエディション、サーバー名 | エラスティック プール内の Premium 以外のデータベースには、StartDatabaseCopy コマンドが発行されます。 | 近日対応予定 |
| 40857 | EX_USER | サーバー '%ls ' のエラスティック プールが見つかりませんでした。エラスティック プール名は ' %ls ' です。 | サーバー名、エラスティック プール名 | 指定されたエラスティック プールが、指定されたサーバー内にありません。 | 有効なエラスティック プール名を指定してください。 |
| 40858 | EX_USER | エラスティック プール '%ls' は サーバー '%ls' に既に存在します。 | エラスティック プール名、サーバー名 | 指定されたエラスティック プールは、指定された論理サーバー内に既に存在します。 | 新しいエラスティック プール名を指定してください。 |
| 40859 | EX_USER | エラスティック プールでは、サービス階層 '%ls' はサポートされていません。 | エラスティック プールのサービス階層 | 指定されたサービス階層は、エラスティック プールのプロビジョニングにはサポートされていません。 | 正しいエディションを指定するか、既定のサービス階層を使用する場合はサービス階層を空のままにしてください。 |
| 40860 | EX_USER | エラスティック プール '%ls' とサービス目標 '%ls' の組み合わせが正しくありません。 | エラスティック プール名、サービス レベル目標名 | エラスティック プールとサービス目標は、サービス目標が 'ElasticPool' に指定されている場合にのみ同時に指定できます。 | エラスティック プールとサービス目標の適切な組み合わせを指定してください。 |
| 40861 | EX_USER | データベースのエディション '%.*ls' は、エラスティック プールのサービス階層 ('%.*ls') と同じである必要があります。 | データベースのエディション、エラスティック プールのサービス階層 | データベースのエディションの大文字と小文字が、エラスティック プールのサービス階層と異なります。 | エラスティック プールのサービス階層と異なるデータベース エディションを指定しないでください。データベースのエディションを指定する必要がないことにご注意ください。 |

<!---HONumber=Oct15_HO3-->