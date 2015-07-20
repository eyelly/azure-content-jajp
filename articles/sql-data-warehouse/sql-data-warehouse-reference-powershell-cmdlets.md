<properties
   pageTitle="SQL Data Warehouse でのコマンドレットの概要"
   description="PowerShell コマンドレットを使用した SQL Data Warehouse の一時停止と再開"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/29/2015"
   ms.author="sidneyh;barbkess"/>

# Azure Data Warehouse コマンドレットと REST API の概要

SQL Data Warehouse は、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。

**Azure SQL Database** に対して定義されているコマンドは、**SQL Data Warehouse** に対しても使用されます。最新のリストについては、「[Azure SQL Database Cmdlets (Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/dn546726.aspx)」を参照してください。コマンドレット **Suspend-AzureSqlDatabase** と **Resume-AzureSqlDatabase** (以下参照) は、SQL Data Warehouse 用に追加作成されたものです。

同様に、**SQL Azure Database** 用の REST API も **SQL Data Warehouse** インスタンスに使用できます。最新のリストについては、「[Azure SQL データベースの操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)」を参照してください。

## Azure PowerShell コマンドレットの取得と実行

1. Azure PowerShell モジュールをダウンロードするには、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) を実行します。 
2. 2. モジュールを実行するには、スタート ウィンドウに「**Microsoft Azure PowerShell**」と入力します。
3. コンピューターにアカウントをまだ追加していない場合は、次のコマンドレットを実行します (詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure/)」を参照してください)。

		Add-AzureAccount
3. 次のコマンドレットを使用してモードを切り替えます。

		Switch-AzureMode AzureResourceManager

## Suspend-AzureSqlDatabase
### 例 1: サーバー上で名前によってデータベースを一時停止する

この例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが一時停止されます。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### 例 2: データベース オブジェクトを一時停止する

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 これは、取得したオブジェクトを **Suspend-AzureSqlDatabase** にパイプ処理します。その結果、データベースが一時停止されます。

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### 例 1: サーバー上で名前によってデータベースを再開する

この例では、"Server01" という名前のサーバーにホストされている "Database02" という名前のデータベースが再開されます。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

	Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### 例 2: データベース オブジェクトの再開

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 オブジェクトは **Resume-AzureSqlDatabase** にパイプ処理されます。

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

このコマンドレットは、Azure SQL Database のバックアップ復元ポイントを一覧表示します。復元ポイントは、データベースの復元に使用されます。返されるオブジェクトのプロパティは、次のとおりです。<table> <tr> <td>プロパティ</td> <td>説明</td> </tr> <tr> <td>RestorePointType</td> <td>不連続/連続。不連続復元ポイントは、Azure SQL Database を復元できる特定の時点を示します。連続復元ポイントは、Azure SQL Database を復元できる最も早い時点を示します。データベースは、最も早い時点以降の任意の時点に復元できます。</td> </tr> <tr> <td>EarliestRestoreDate</td> <td>最も早い復元時間 (restorePointType = CONTINUOUS である場合にデータを取り込む)</td> </tr> <tr> <td>RestorePointCreationDate </td> <td>バックアップ スナップショット時間 (restorePointType = DISCRETE である場合にデータを取り込む)</td> </tr> </table>
### 例 1: サーバー上で名前によってデータベースの復元ポイントを取得する
この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースの復元ポイントが取得されます。

	$restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"



### 例 2: データベース オブジェクトの再開

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースが取得されます。 データベース オブジェクトは **Get-AzureSqlDatabase** にパイプ処理され、データベースの復元ポイントが結果として得られます。

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$restorePoints = $database | Get-AzureSqlDatabaseRestorePoints


## 次のステップ
詳細な参照情報については、[SQL Data Warehouse のリファレンス概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse のリファレンス概要]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=July15_HO1-->