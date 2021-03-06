<properties 
    pageTitle="PowerShell を使用して Azure SQL Database のサービス階層とパフォーマンス レベルを変更する" 
    description="「Azure SQL Database のサービス階層とパフォーマンス レベルを変更する」では、PowerShell を使用した SQL Database のスケール アップとスケール ダウンの方法について説明しています。PowerShell を使用して Azure SQL Database の価格レベルを変更します。" 
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


# PowerShell で SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する

**1 つのデータベース**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


この記事では、PowerShell で SQL Database のサービス階層とパフォーマンス レベルを変更する方法について説明します。

[SQL Database の Web/Business データベースの新しいサービス階層へのアップグレード](sql-database-upgrade-new-service-tiers.md)に関するトピックと「[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)」の情報を使用して、Azure SQL Database に適したサービス階層とパフォーマンス レベルを決定します。

> [AZURE.IMPORTANT]SQL Database のサービス階層とパフォーマンス レベルの変更はオンライン操作です。そのため、操作中はダウンタイムがなく、データベースを使用できます。

- データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
- [標準の geo レプリケーション](https://msdn.microsoft.com/library/azure/dn758204.aspx)または[アクティブ geo レプリケーション](https://msdn.microsoft.com/library/azure/dn741339.aspx)を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス階層にアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
- Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。「[連続コピーの関係の終了](https://msdn.microsoft.com/library/azure/dn741323.aspx)」に説明されている手順に従って、プライマリ データベースとアクティブ セカンダリ データベース間のレプリケーション プロセスを停止してください。
- サービス階層によって、提供されている復元サービスは異なります。ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。詳細については、「[Azure SQL Database のバックアップと復元](https://msdn.microsoft.com/library/azure/jj650016.aspx)」を参照してください。
- 24 時間の期間内に 4 つまでの個別のデータベースの変更 (サービス階層またはパフォーマンス レベル) を行うことができます。
- データベースに対する新しいプロパティは、変更が完了するまで適用されません。



**この記事を完了するには、以下が必要です。**

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- Azure SQL Database。SQL Database がない場合は、「[最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。
- Azure PowerShell。

> [AZURE.IMPORTANT]Azure PowerShell 1.0 Preview のリリースから、Switch-AzureMode コマンドレットは利用できなくなりました。また、Azure ResourceManger モジュールで使用されていたコマンドレットは名前が変更されました。この記事の例では、新しい PowerShell 1.0 Preview の名付け規則が使用されています。詳細については、[Azure PowerShell での Switch-AzureMode の廃止](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell)に関するページを参照してください。


PowerShell コマンドレットを実行するには、Azure PowerShell をインストールして実行する必要があります。Switch-AzureMode が削除されたため、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行し、最新の Azure PowerShell をダウンロードしてインストールする必要があります。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」をご覧ください。



## 資格情報を構成してサブスクリプションを選択

まず、Azure アカウントへのアクセスを確立する必要があるため、PowerShell を起動してから以下のコマンドレットを実行します。ログイン画面で、Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを入力します。

	Add-AzureAccount

正常にサインインすると、サインインしている ID や使用中の Azure サブスクリプションを含む情報が画面に表示されます。


### Azure サブスクリプションを選択します。

サブスクリプションを選択するには、サブスクリプション ID またはサブスクリプション名 (**-SubscriptionName**) が必要になります。サブスクリプション ID は前の手順で示された情報からコピーできます。または、複数のサブスクリプションがあり、詳しい情報が必要な場合は、**Get-AzureSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。ご利用のサブスクリプションを取得したら次のコマンドレットを実行します。

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

**Select-AzureSubscription** を正常に実行すると、PowerShell プロンプトに戻ります。サブスクリプションが複数ある場合は、**Get-AzureSubscription** を実行して、使用するサブスクリプションが **IsCurrent: True** と表示されていることを確認できます。


 


## SQL Database のサービス階層とパフォーマンス レベルを変更する

**Set-AzureRMSqlDatabase** コマンドレットを実行し、**-RequestedServiceObjectiveName** を目的の価格レベルのパフォーマンス レベルに設定します (*S0*、*S1*、*S2*、*S3*、*P1*、*P2* など)。

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## SQL Database のサービス階層とパフォーマンス レベルを変更するサンプル PowerShell スクリプト

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRMSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## 次のステップ

- [スケールアウトとスケールイン](sql-database-elastic-scale-get-started.md)
- [SSMS での SQL Database の接続とクエリ](sql-database-connect-query-ssms.md)
- [Azure SQL Database のエクスポート](sql-database-export-powershell.md)

## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt163521.aspx)

<!---HONumber=Oct15_HO3-->