<properties
   pageTitle="リソース マネージャーのテンプレートを使用してリソースをデプロイする |Microsoft Azure"
   services="azure-resource-manager"
   description="Azure リソース マネージャーを使用してリソースを Azure にデプロイします。テンプレートは JSON ファイルであり、ポータル、PowerShell、Azure コマンドライン インターフェイス (Mac、Linux、Windows 用)、または REST で使用できます。"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ

このトピックでは、Azure リソース マネージャーのテンプレートを使用して Azure にアプリケーションをデプロイする方法について説明します。また、Azure PowerShell、Azure CLI、REST API、または Microsoft Azure プレビュー ポータルを使用して、アプリケーションをデプロイする方法も示します。

リソース マネージャーの概要については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。テンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

テンプレートを使用してアプリケーションをデプロイする場合は、パラメーター値を指定することで、リソースの作成方法をカスタマイズすることができます。これらのパラメーターの値は、インラインか、パラメーター ファイルのどちらかで指定します。


## PowerShell でデプロイする

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


1. Azure アカウントにログインします。資格情報を提供すると、コマンドがアカウントの情報を返します。

    Azure PowerShell 1.0 Preview 未満:

        PS C:\> Switch-AzureMode AzureResourceManager
        ...
        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

    Azure PowerShell 1.0 Preview:

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. 複数のサブスクリプションがある場合、**Select-AzureRmSubscription** コマンドでデプロイに使用するサブスクリプション ID を提供します。

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. 既存のリソース グループがない場合は、**New-AzureRmResourceGroup** コマンドで新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. リソース グループに新しいデプロイを作成するには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
     次のオプションを使用してパラメーターの値を提供できます。
   
     - インライン パラメーターを使用する

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - パラメーター オブジェクトを使用する

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - パラメーター ファイルの使用テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     リソース グループをデプロイすると、デプロイの概要が表示されます。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. デプロイ エラーに関する情報を取得するには

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment

        
### ビデオ

このビデオでは、PowerShell を使用したリソース マネージャーのテンプレートの利用方法を説明しています。

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Mac、Linux、および Windows 用の Azure CLI でデプロイする

リソース マネージャーで Azure CLI を使用したことがない場合は、「[Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」に関するページを参照してください。

1. Azure アカウントにログインします。資格情報を提供すると、コマンドがログインの結果を返します。

        azure login
  
        ...
        info:    login command OK

2. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。

        azure account set <YourSubscriptionNameOrId>

3. Azure リソース マネージャー モジュールに切り替えます。新しいモードの確認が表示されます。

        azure config mode arm
   
        info:     New mode is arm

4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. リソース グループに新しいデプロイを作成するには、次のコマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
     次のオプションを使用してパラメーターの値を提供できます。

     - インライン パラメーターとローカルのテンプレートを使用します。各パラメーターは、`"ParameterName": { "value": "ParameterValue" }` の形式になります。次の例では、エスケープ文字を含むパラメーターを示します。

             azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - インライン パラメーターとローカルのテンプレートへのリンクを使用します。

             azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - パラメーター ファイルを使用します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     リソース グループをデプロイすると、デプロイの概要が表示されます。
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 最新のデプロイに関する情報を取得するには

         azure group log show -l ExampleResourceGroup

7. デプロイ エラーに関する詳細情報を取得するには
      
         azure group log show -l -v ExampleResourceGroup

## REST API でデプロイする
1. [一般的なパラメーターおよびヘッダー](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) (認証トークンを含む) を設定します。
2. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、および場所を指定します。詳細については、「[リソース グループの作成](https://msdn.microsoft.com/library/azure/dn790525.aspx)」を参照してください。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. リソース グループの新しいデプロイを作成します。サブスクリプション ID、デプロイするリソース グループの名前、デプロイの名前、およびテンプレートの場所を指定します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。リソース グループを作成する REST API の詳細については、「[テンプレートのデプロイを作成する](https://msdn.microsoft.com/library/azure/dn790564.aspx)」を参照してください。
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. テンプレートのデプロイの状態を取得します。詳細については、「[テンプレートのデプロイに関する情報を取得](https://msdn.microsoft.com/library/azure/dn790565.aspx)」を参照してください。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## プレビュー ポータルでデプロイする

ご存知でしょうか。 [プレビュー ポータル](https://portal.azure.com/)を使用して作成するすべてのアプリケーションは、Azure リソース マネージャーのテンプレートに基づいています。 ポータルを使用して Virtual Machine、Virtual Network、ストレージ アカウント、App Service、またはデータベースを作成するだけで、追加の操作を行わなくても、Azure リソース マネージャーの恩恵を得られます。**[新規]** アイコンを選択するだけで、Azure リソース マネージャーを使用してアプリケーションのデプロイを進めることができます。

![新規](./media/resource-group-template-deploy/new.png)

プレビュー ポータルと Azure リソース マネージャーの使用方法の詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](azure-portal/resource-group-portal.md)」を参照してください。


## パラメーター ファイル

デプロイ中にパラメーター ファイルを使用してパラメーター値をテンプレートに渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

パラメーター ファイルのサイズは、64 KB 以下である必要があります。

## 次のステップ
- .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](arm-template-deployment.md)」を参照してください。
- アプリケーションのデプロイの詳細な例については、「[Azure でマイクロサービスを予測どおりにデプロイする](app-service-web/app-service-deploy-complex-application-predictably.md)」を参照してください。
- ソリューションを別の環境にデプロイする方法については、「[Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments-preview-portal.md)」を参照してください。
- Azure リソース マネージャーのテンプレートのセクションについては、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- Azure リソース マネージャーのテンプレートで使用できる関数の一覧については、「[Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。

 

<!---HONumber=Nov15_HO2-->