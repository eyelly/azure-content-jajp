<properties 
   pageTitle="Virtual Network 内の内部ロード バランサー (ILB) を使用した アプリケーション ゲートウェイの作成と構成 | Microsoft Azure"
   description="このページでは、内部ロード バランサーのエンドポイントを使用して Azure Application Gateway を構成する手順について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/29/2015"
   ms.author="joaoma"/>

# 内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)


アプリケーション ゲートウェイは、インターネットに接続する VIP のほか、内部ロード バランサー (ILB) エンドポイントとも呼ばれるインターネットに接続されていない内部エンドポイントを使用して構成できます。ILB を使用したゲートウェイの構成は、インターネットに接続されていない社内用ビジネス アプリケーションで便利です。また、セキュリティの境界でインターネットに接続されていない多階層アプリケーション内のサービスや階層でも便利ですが、ラウンド ロビンの負荷分散、セッションの持続性、または SSL 終了が必要です。この記事では、ILB を使用してアプリケーション ゲートウェイを構成する手順について説明します。

## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。最新バージョンは、[ダウンロード ページ](http://azure.microsoft.com/downloads/)の **Windows PowerShell** のセクションからダウンロードしてインストールできます。
2. 有効なサブネットが存在する作業用の仮想ネットワークがあることを確認します。
3. 仮想ネットワーク内、またはパブリック IP/VIP が割り当てられたバックエンド サーバーがあることを確認します。


新しいアプリケーション ゲートウェイを作成するには、次の手順を順番に実行します。

1. [新しいアプリケーション ゲートウェイの作成](#create-a-new-application-gateway)
2. [ゲートウェイの構成](#configure-the-gateway)
3. [ゲートウェイ構成の設定](#set-the-gateway-configuration)
4. [ゲートウェイの起動](#start-the-gateway)
4. [ゲートウェイの確認](#verify-the-gateway-status)



## 新しいアプリケーション ゲートウェイの作成:

**ゲートウェイを作成する**には、`New-AzureApplicationGateway` コマンドレットを独自の値に置き換えて使用します。この時点ではゲートウェイの課金は開始されません。課金は後の手順でゲートウェイが正しく起動されたときに開始します。

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

ゲートウェイが作成されたことを**確認する**には、`Get-AzureApplicationGateway` コマンドレットを使用します。

サンプルでは、*Description*、*InstanceCount*、および*GatewaySize* は省略可能なパラメーターです。*InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。その他の値は Small および Large です。ゲートウェイがまだ起動していないため、*Vip* と *DnsName* は空白のまま表示されます。これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## ゲートウェイの構成

アプリケーション ゲートウェイの構成は、複数の値で構成されます。値を相互に関連付けて構成を作成します。
 
値は次のとおりです。

- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧です。一覧の IP アドレスは、VNet サブネットに属しているか、パブリック IP/VIP である必要があります。 
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、アプリケーション ゲートウェイに開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。 
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。現在、*basic* ルールのみサポートされます。*basic* ルールは、ラウンド ロビンの負荷分散です。

構成は、構成オブジェクトを作成するか、構成 XML ファイルを使用して構築できます。構成 XML ファイルを使用して構成を構築するには、次のサンプルを使用します。



以下の点に注意してください。


- *FrontendIPConfigurations* 要素には、ILB を使用したアプリケーション ゲートウェイの構成に関連した ILB の詳細が記述されています。 

- フロントエンド IP の *Type* は、[Private] に設定する必要があります。

- *StaticIPAddress* は、ゲートウェイがトラフィックを受信する、目的の内部 IP アドレスに設定する必要があります。なお、*StaticIPAddress* 要素は省略可能です。設定しない場合は、デプロイ済みのサブネットから有効な内部 IP が選択されます。

- *FrontendIPConfiguration* で指定した *Name* 要素の値は、FrontendIPConfiguration を参照するように、HTTPListener の *FrontendIP* 要素に指定する必要があります。

 **構成 XML のサンプル**

 

		<?xml version="1.0" encoding="utf-8"?>
		<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
			<FrontendIPConfigurations>
				<FrontendIPConfiguration>
					<Name>fip1</Name> 
					<Type>Private</Type> 
					<StaticIPAddress>10.0.0.10</StaticIPAddress> 
				</FrontendIPConfiguration>
			</FrontendIPConfigurations>
		    <FrontendPorts>
		        <FrontendPort>
		            <Name>FrontendPort1</Name>
		            <Port>80</Port>
		        </FrontendPort>
		    </FrontendPorts>
		    <BackendAddressPools>
		        <BackendAddressPool>
		            <Name>BackendPool1</Name>
		            <IPAddresses>
		                <IPAddress>10.0.0.1</IPAddress>
		                <IPAddress>10.0.0.2</IPAddress>
		            </IPAddresses>
		        </BackendAddressPool>
		    </BackendAddressPools>
		    <BackendHttpSettingsList>
		        <BackendHttpSettings>
		            <Name>BackendSetting1</Name>
		            <Port>80</Port>
		            <Protocol>Http</Protocol>
		            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
		        </BackendHttpSettings>
		    </BackendHttpSettingsList>
		    <HttpListeners>
		        <HttpListener>
		            <Name>HTTPListener1</Name>
					<FrontendIP>fip1</FrontendIP>
		            <FrontendPort>FrontendPort1</FrontendPort>
		            <Protocol>Http</Protocol>
		        </HttpListener>
		    </HttpListeners>
		    <HttpLoadBalancingRules>
		        <HttpLoadBalancingRule>
		            <Name>HttpLBRule1</Name>
		            <Type>basic</Type>
		            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
		            <Listener>HTTPListener1</Listener>
		            <BackendAddressPool>BackendPool1</BackendAddressPool>
		        </HttpLoadBalancingRule>
		    </HttpLoadBalancingRules>
		</ApplicationGatewayConfiguration>
	


## ゲートウェイ構成の設定

次に、アプリケーション ゲートウェイを設定します。構成オブジェクトまたは構成 XML ファイルで `Set-AzureApplicationGatewayConfig` コマンドレットを使用できます。

	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## ゲートウェイの起動

ゲートウェイを構成したら、`Start-AzureApplicationGateway` コマンドレットを使用してゲートウェイを起動します。アプリケーション ゲートウェイの課金は、ゲートウェイが正常に起動された後に開始します。


**注:** `Start-AzureApplicationGateway` コマンドレットの実行には最大で 15 ～ 20 分かかる場合があります。
   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## ゲートウェイの状態の確認

`Get-AzureApplicationGateway` コマンドレットを使用してゲートウェイの状態を確認します。前の手順で *Start-AzureApplicationGateway* が成功した場合、State は *Running* になり、Vip と DnsName に有効な値が入力されます。このサンプルの最初の行はコマンドレットを示し、その後に出力が続きます。このサンプルでは、ゲートウェイが実行中で、トラフィックを受け取る準備ができています。

**注:** この例のアプリケーション ゲートウェイは、構成済みの ILB エンドポイント 10.0.0.10 のトラフィックを受け入れるように構成されています。

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {10.0.0.10}
	DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## 次のステップ


負荷分散のオプション全般の詳細については、次を参照してください。

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO3-->