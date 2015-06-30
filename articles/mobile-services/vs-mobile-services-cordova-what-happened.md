<properties 
	pageTitle="" 
	description="Cordova の Azure Mobile Service プロジェクトの変更点について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

### <span id="whathappened">プロジェクトの変更点</span>

##### リファレンスの追加

すべてのマルチデバイス ハイブリッド アプリケーションに含まれる Azure Mobile Service のクライアント プラグインが有効になりました。
  
##### Mobile Services 用の接続文字列の値

`services\mobileServices\settings` に新しい JavaScript (.js) ファイルが生成されました。このファイルには、選択したモバイル サービスのアプリケーション URL とアプリケーション キーを格納した **MobileServiceClient** が含まれています。このファイルには、次のようなモバイル サービス クライアント オブジェクトの初期化コードが含まれています。

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)
<!--HONumber=54--> 