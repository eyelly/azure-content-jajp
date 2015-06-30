<properties
	pageTitle="iOS アプリ用 Azure Mobile Services の使用"
	description="次のチュートリアルに従って、iOS 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="2/11/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure Mobile Services を使用して iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。

このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリ データを保存する簡単な _To do list_ アプリの両方を作成します。作成する Mobile Services は、サーバー側ビジネス ロジックのために JavaScript を使用します。.NET でサーバー側ビジネス ロジックを記述できる Mobile Services を作成する方法については、このトピックの[.NET バックエンド バージョン]をご覧ください。

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、[無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます](http://azure.microsoft.com/pricing/details/mobile-services/)。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank")をご覧ください。

## <a name="create-new-service"> </a>新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

<h2>新しい iOS アプリを作成する</h2>

管理ポータルで簡単なクイック スタートに従い、Mobile Services に接続された新しいアプリを作成できます。

1. 管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[iOS]** を選択し、**[新しい iOS アプリを作成する]** を展開します。これにより、Mobile Services に接続された iOS アプリを作成するための手順が表示されます。

3. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。これにより、Mobile Services に接続されている _To do list_ サンプル アプリケーションのプロジェクトが、Mobile Services iOS SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい iOS アプリを実行する

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>管理ポータルに戻り、<strong>[データ]</strong> タブ、<strong>TodoItems</strong> テーブルの順にクリックします。これで、アプリケーションによってテーブルに挿入されたデータを参照できます。<p></li></ol></p>

## <a name="next-steps"></a>次のステップ
Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。

* [既存のアプリに Mobile Services を追加する]
	<br/>Mobile Services を使用してデータの格納とクエリを実行する方法について説明します。

* [オフライン データ同期の使用]
	<br/>オフライン データの同期を使用してアプリケーションの反応と信頼性を高める方法について説明します。

* [既存の Azure Mobile Services アプリへの認証の追加]
	<br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [Mobile Services アプリへのプッシュ通知の追加]
	<br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。


<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[既存のアプリに Mobile Services を追加する]: mobile-services-dotnet-backend-ios-get-started-data.md
[オフライン データ同期の使用]: mobile-services-ios-get-started-offline-data.md
[既存の Azure Mobile Services アプリへの認証の追加]: mobile-services-dotnet-backend-ios-get-started-users.md
[Mobile Services アプリへのプッシュ通知の追加]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[管理ポータル]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET バックエンド バージョン]: mobile-services-dotnet-backend-ios-get-started.md


<!--HONumber=52--> 