<properties linkid="develop-python-service-management" urlDisplayName="サービス管理" pageTitle="サービス管理 API の使用方法 (Python) - 機能ガイド" metaKeywords="" description="Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。" metaCanonical="" services="cloud-services" documentationCenter="Python" title="Python からサービス管理を使用する方法" authors="" solutions="" manager="" editor="" />




# Python からサービス管理を使用する方法

このガイドでは、Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。[Azure SDK for Python][download-SDK-Python] の **ServiceManagementService** クラスは、[管理ポータル][management-portal]で使用できるサービス管理関連の機能 (**クラウド サービス、展開、データ管理サービス、仮想マシン、およびアフィニティ グループの作成、更新、削除**など) へのプログラムによるアクセスをサポートしています。この機能は、サービス管理へのプログラムによるアクセスが必要なアプリケーションをビルドするために役立つ場合があります。

## 目次

* [サービス管理とは][]
* [概念][]
* [方法: サービス管理に接続する][]
* [方法: 利用可能な場所を列挙する][]
* [方法: クラウド サービスを作成する][]
* [方法: クラウド サービスを削除する][]
* [方法: 展開を作成する][]
* [方法: 展開を更新する][]
* [方法: ステージング環境と運用環境の間で展開を移動する][]
* [方法: 展開を削除する][]
* [方法: ストレージ サービスを作成する][]
* [方法: ストレージ サービスを削除する][]
* [方法: アフィニティ グループを作成する][]
* [方法: アフィニティ グループを削除する][]
* [方法: 利用可能なオペレーティング システムを列挙する][]
* [方法: オペレーティング システム イメージを作成する][]
* [方法: オペレーティング システム イメージを削除する][]
* [方法: 仮想マシンを作成する][]
* [方法: 仮想マシンを削除する][]
* [次のステップ][]

## <a name="WhatIs"> </a>サービス管理とは
サービス管理 API を使用すると、[管理ポータル][management-portal]を通じて使用可能なサービス管理機能の多くにプログラムでアクセスできます。Azure SDK for Python を使用すると、クラウド サービス、ストレージ アカウント、アフィニティ グループを管理できます。

サービス管理 API を使用するには、[Azure アカウントを作成する](http://www.windowsazure.com/ja-jp/pricing/free-trial/)必要があります。

## <a name="Concepts"> </a>概念
Azure SDK for Python は、REST API である [Azure サービス管理 API][svc-mgmt-rest-api] をラップします。すべての API 操作は SSL 上で実行され、X.509 v3 証明書を使用して相互認証されます。管理サービスへのアクセスは、Azure で実行されているサービス内から行うことも、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット上で直接行うこともできます。

## <a name="Connect"> </a>方法: サービス管理に接続する
サービス管理エンドポイントに接続するには、Azure サブスクリプション ID、および有効な管理証明書へのパスが必要です。サブスクリプション ID は[管理ポータル][management-portal]から入手できます。

### Windows での管理証明書

`makecert.exe` を使用して、マシン上で自己署名管理証明書を作成できます。**管理者**として **Visual Studio コマンド プロンプト**を開き、次のコマンドを使用して、*AzureCertificate* を、使用する証明書の名前に置き換えます。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

このコマンドにより、`.cer` ファイルが作成され、**個人用**証明書ストアにインストールされます。詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551722.aspx)」を参照してください。

証明書を作成した後、[管理ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と**個人用**証明書ストア内の証明書の場所を **ServiceManagementService** に渡すことで、Azure management のエンドポイントに接続できます (再度、*AzureCertificate* を証明書の名前に置き換えます)。

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

この例では、`sms` は **ServiceManagementService** オブジェクトです。**ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

### Mac または Linux での管理証明書
[OpenSSL](http://www.openssl.org/) を使用して管理証明書を作成できます。実際は 2 つの証明書を作成する必要があります。1 つはサーバー用 (`.cer` ファイル)、もう 1 つはクライアント用 (`.pem` ファイル) です。`.pem` ファイルを作成するには、次のコマンドを実行します。

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

`.cer` 証明書を作成するには、次をコマンドを実行します。

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Azure 証明書の詳細については、「[証明書の管理](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg981929.aspx)」を参照してください。OpenSSL のパラメーターの詳細については、[http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html) にあるドキュメントを参照してください。

これらのファイルを作成した後、[管理ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。`.pem` ファイルを保存した場所を書き留めておいてください。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と `.pem` ファイルへのパスを **ServiceManagementService** に渡すことで、Azure management のエンドポイントに接続できます。

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

この例では、`sms` は **ServiceManagementService** オブジェクトです。**ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

## <a name="ListAvailableLocations"> </a>方法: 利用可能な場所を列挙する

ホスティング サービスに利用できる場所を列挙するには、**list_locations** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

クラウド サービス、ストレージ サービス、またはアフィニティ グループを作成するときは、有効な場所を指定する必要があります。**list_locations** メソッドでは常に、現在利用可能な場所の最新のリストが返されます。この記事の執筆時点で利用可能な場所は次のとおりです。

- 西ヨーロッパ
- 東南アジア
- 東アジア
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 米国西部
- 米国東部

##<a name="CreateCloudService"></a>方法: クラウド サービスを作成する

アプリケーションを作成して、それを Azure で実行するときは、そのコードと構成をあわせて Azure [クラウド サービス]と呼びます (以前にリリースした Azure では*ホステッド サービス*と呼ばれていました)。**create\_hosted\_service** メソッドを使用して、新しいホステッド サービスを作成できます。そのためには、このメソッドに、ホステッド サービス名 (Azure 上で一意の名前)、ラベル (Base64 に自動的にエンコードされます)、説明、場所を渡します。サービスの場所の代わりにアフィニティ グループを指定できます。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

**list\_hosted\_services** メソッドを使用して、サブスクリプションのすべてのホステッド サービスを列挙できます。

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

特定のホステッド サービスに関する情報を取得する場合は、ホステッド サービス名を **get\_hosted\_service\_properties** メソッドに渡します。

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
クラウド サービスを作成した後、**create\_deployment** メソッドを使用してコードをサービスに展開できます。

## <a name="DeleteCloudService"> </a>方法: クラウド サービスを削除する

クラウド サービスを削除するには、そのサービス名を **delete\_hosted\_service** メソッドに渡します。

	sms.delete_hosted_service('myhostedservice')

サービスを削除する前に、そのサービスのすべての展開を最初に削除する必要があることに注意してください (詳細については「[方法: 展開を削除する](#DeleteDeployment)」を参照)。

## <a name="CreateDeployment"> </a>方法: 展開を作成する

**create\_deployment** メソッドでは、ステージング環境または運用環境に新しい[サービス パッケージ]をアップロードし、新しい展開を作成します。このメソッドのパラメーターは次のとおりです。

* **name**: ホステッド サービスの名前。
* **deployment\_name**: 展開の名前。
* **slot**: ステージング スロット (`staging`) または運用スロット (`production`) を示す文字列。
* **package_url**: 展開パッケージ (.cspgk ファイル) の URL。パッケージ ファイルは、パッケージのアップロード先のホステッド サービスと同じサブスクリプションの Azure BLOB ストレージ アカウントに保存する必要があります。展開パッケージを作成するには、[Azure PowerShell コマンドレット]または [cspack コマンド ライン ツール]を使用します。
* **configuration**: Base 64 にエンコードされたサービス構成ファイル (.cscfg file)。
* **label**: ホステッド サービス名のラベル (Base64 に自動的にエンコードされます)。

次の例では、`myhostedservice` という名前のホステッド サービスの新しい展開 `v1` を作成します。

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	slot = 'production'
	package_url = 'URL_for_.cspkg_file'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
	label = 'myhostedservice'

	result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

上の例では、**create\_deployment** 処理のステータスを取得するために、**create\_deployment** によって返された結果を **get\_operation\_status** メソッドに渡していることに注意してください。

展開のプロパティにアクセスするには、**get\_deployment\_by\_slot** メソッドまたは **get\_deployment\_by\_name** メソッドを使用します。次の例では、デプロイ スロットを指定してデプロイを取得します。この例では、展開のすべてのインスタンスの反復処理もしています。

	result = sms.get_deployment_by_slot('myhostedservice', 'production')

	print('Name: ' + result.name)
	print('Slot: ' + result.deployment_slot)
	print('Private ID: ' + result.private_id)
	print('Status: ' + result.status)
	print('Instances:')
	for instance in result.role_instance_list:
		print('Instance name: ' + instance.instance_name)
		print('Instance status: ' + instance.instance_status)
		print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>方法: 展開を更新する

展開は **change\_deployment\_configuration** メソッドまたは **update\_deployment\_status** メソッドを使用して更新できます。

**change\_deployment\_configuration** メソッドを使用して、新しいサービス構成 (`.cscfg`) ファイルをアップロードできます。これにより、複数のサービス設定のいずれか (展開内のインスタンスの数など) が変更されます。詳細については、「[Azure サービスの構成スキーマ (.cscfg ファイル)]」を参照してください。次の例では、新しいサービス構成ファイルをアップロードする方法を示しています。

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

	result = sms.change_deployment_configuration(name, deployment_name, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)


上の例では、**change\_deployment\_configuration** 処理のステータスを取得するために、**change\_deployment\_configuration** によって返された結果を **get\_operation\_status** メソッドに渡していることに注意してください。

**update\_deployment\_status** メソッドを使用して、展開のステータスを RUNNING または SUSPENDED に設定できます。次の例では、`myhostedservice` というホステッド サービスの `v1` という名前の展開について、そのステータスを RUNNING に設定する方法を示しています。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>方法: ステージング環境と運用環境の間で展開を移動する

Azure には、2 つの展開環境が用意されています。ステージングと運用です。通常は、サービスをステージング環境にデプロイしてテストし、その後で運用環境にデプロイします。ステージング環境のサービスを運用環境へ昇格する場合、サービスを再デプロイする必要はありません。デプロイをスワップすることで運用環境へ昇格できます。展開のスワップの詳細については、「[Azure での配置の管理]」を参照してください。

次の例では、**swap\_deployment** メソッドを使用して 2 つの展開 (展開名は `v1` と `v2`) をスワップする方法を示しています。この例の場合、**swap\_deployment** を呼び出す前は、展開 `v1` が運用スロットにあり、展開 `v2` がステージング スロットにあります。**swap\_deployment** の呼び出し後、`v2` は運用スロットに、`v1` はステージング スロットにあります。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>方法: 展開を削除する

展開を削除するには、**delete\_deployment** メソッドを使用します。次の例では、`v1` という名前の展開を削除する方法を示しています。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>方法: ストレージ サービスを作成する

[ストレージ サービス] を使用すると、Azure の [Blobs][azure-blobs]、[テーブル][azure-tables]、[キュー][azure-queues] キューにアクセスできます。ストレージ サービスを作成するには、サービスの名前 (Azure 内で一意の 3 〜 24 文字の小文字)、説明、ラベル (最大 100 文字、Base64 に自動的にエンコードされます)、場所 (またはアフィニティ グループ) が必要です。次の例では、場所を指定してストレージ サービスを作成する方法を示しています。アフィニティ グループを使用する場合は、まずアフィニティ グループを作成し (「[方法: アフィニティ グループの作成](#CreateAffinityGroup)」を参照)、**affinity\_group** パラメーターでアフィニティ グループを設定する必要があります。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

上の例では、**create\_storage\_account** 処理のステータスを取得するために、**create\_storage\_account** によって返された結果を **get\_operation\_status** メソッドに渡していることに注意してください。

ストレージ アカウントとそのプロパティを列挙するには、**list\_storage\_accounts** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>方法: ストレージ サービスを削除する

ストレージ サービスを削除するには、そのサービス名を **delete\_storage\_account** メソッドに渡します。ストレージ サービスを削除すると、サービスに格納されているすべてのデータ (BLOB、テーブル、キュー) が削除されます。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>方法: アフィニティ グループを作成する

アフィニティ グループは Azure サービスの論理グループであり、Azure で最適なパフォーマンスを得られるようにサービスを配置するために使用されます。たとえば、アフィニティ グループを "米国西部" という場所に作成し、そのアフィニティ グループ内に[クラウド サービス](#CreateCloudService)を作成できます。その後、同じアフィニティ グループ内にストレージ サービスを作成した場合、Azure では、そのサービスは "米国西部" 場所に配置され、同じアフィニティ グループ内のクラウド サービスと連携して最高レベルのパフォーマンスが得られるように、データ センター内で最適化されます。

アフィニティ グループを作成するには、グループの名前、ラベル (Base64 に自動的にエンコードされます)、場所が必要です。必要に応じて説明を指定できます。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

アフィニティ グループの作成後、[ストレージ サービスを作成](#CreateStorageService)するときは、場所ではなくグループを指定できます。

アフィニティ グループを列挙し、そのプロパティを調べるには、**list\_affinity\_groups** メソッドを呼び出します。

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>方法: アフィニティ グループを削除する
	
アフィニティ グループを削除するには、そのグループ名を **delete\_affinity\_group** メソッドに渡します。アフィニティ グループを削除する前に、アフィニティ グループとサービス (削除するアフィニティ グループを使用するサービス) との関連付けを解除する必要があることに注意してください。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>方法: 利用可能なオペレーティング システムを列挙する

ホスティング サービスに利用できるオペレーティング システムを列挙するには、**list\_operating\_systems** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

または、**list\_operating\_system\_families** メソッドを使用することもできます。このメソッドでは、オペレーティング システムがファミリにグループ化されます。

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>方法: オペレーティング システム イメージを作成する

オペレーティング システム イメージをイメージ リポジトリに追加するには、**add\_os\_image** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

利用できるオペレーティング システム イメージを列挙するには、**list\_os\_images** メソッドを使用します。このメソッドでは、すべてのプラットフォーム イメージとユーザー イメージが対象となります。

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Affinity group: ' + image.affinity_group)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>方法: オペレーティング システム イメージを削除する

ユーザー イメージを削除するには、**delete\_os\_image** メソッドを使用します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>方法: 仮想マシンを作成する

仮想マシンを作成するには、最初に[クラウド サービス](#CreateCloudService)を作成する必要があります。その後で、**create\_virtual\_machine\_deployment** メソッドを使用して、仮想マシンの展開を作成します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>方法: 仮想マシンを削除する

仮想マシンを削除するには、最初に **delete\_deployment** メソッドを使用して展開を削除します。

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

その後で、**delete\_hosted\_service**メソッドを使用して、クラウド サービスを削除することができます。

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>次のステップ

これで、サービス管理の基本を学習できました。さらに複雑なタスクを実行するには、次のリンク先を参照してください。

-   MSDN リファレンス: [クラウド サービス][]
-   MSDN リファレンス: [仮想マシン][]

[サービス管理とは]: #WhatIs
[概念]: #Concepts
[方法: サービス管理に接続する]: #Connect
[方法: 利用可能な場所を列挙する]: #ListAvailableLocations
[方法: クラウド サービスを作成する]: #CreateCloudService
[方法: クラウド サービスを削除する]: #DeleteCloudService
[方法: 展開を作成する]: #CreateDeployment
[方法: 展開を更新する]: #UpdateDeployment
[方法: ステージング環境と運用環境の間で展開を移動する]: #MoveDeployments
[方法: 展開を削除する]: #DeleteDeployment
[方法: ストレージ サービスを作成する]: #CreateStorageService
[方法: ストレージ サービスを削除する]: #DeleteStorageService
[方法: アフィニティ グループを作成する]: #CreateAffinityGroup
[方法: アフィニティ グループを削除する]: #DeleteAffinityGroup
[方法: 利用可能なオペレーティング システムを列挙する]: #ListOperatingSystems
[方法: オペレーティング システム イメージを作成する]: #CreateVMImage
[方法: オペレーティング システム イメージを削除する]: #DeleteVMImage
[方法: 仮想マシンを作成する]: #CreateVM
[方法: 仮想マシンを削除する]: #DeleteVM
[次のステップ]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/ja-jp/develop/python/common-tasks/install-python/
[クラウド サービス]:http://windowsazure.com/ja-jp/documentation/articles/cloud-services-what-is
[クラウド サービス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj155995.aspx
[Azure PowerShell cmdlets (Azure PowerShell コマンドレット)]: https://www.windowsazure.com/ja-jp/develop/php/how-to-guides/powershell-cmdlets/
[CSPack コマンド ライン ツール]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg432988.aspx
[Azure での配置の管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433027.aspx
[ストレージ アカウントとは]: https://www.windowsazure.com/ja-jp/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/ja-jp/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/ja-jp/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/ja-jp/develop/python/how-to-guides/queue-service/
[Azure サービスの構成スキーマ (.cscfg ファイル)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758710.aspx
[クラウド サービス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj155995.aspx
[仮想マシン]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156003.aspx
