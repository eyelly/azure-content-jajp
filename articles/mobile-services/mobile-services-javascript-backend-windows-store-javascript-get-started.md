<properties 
	pageTitle="Windows ストア アプリ用 Mobile Services の使用 | モバイル デベロッパー センター" 
	description="このチュートリアルでは、C# または JavaScript で Windows ストア用の開発を行う場合に Azure Mobile Services を使用する方法を示します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure Mobile Services を使用してユニバーサル Windows アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 

このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリケーション データを保存する簡単な  *To do list* アプリケーションの両方を作成します。作成する Mobile Services は、サーバー側ビジネス ロジックのために JavaScript を使用します。Visual Studio を使用してサポートされる .NET 言語でサーバー側ビジネス ロジックを記述できる Mobile Services を作成する方法については、このトピックの「.NET バックエンド バージョン」をご覧ください。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)をご覧ください
* [Visual Studio 2013 Express for Windows] 

## 新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しいユニバーサル Windows アプリを作成する

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいユニバーサル Windows アプリを作成するか、既存の Windows ストアまたは Windows Phone アプリ プロジェクトを変更して Mobile Services に接続できます。 

ここでは、Mobile Services に接続された新しいユニバーサル Windows アプリを作成します。

1.  管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

   	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png)

   	これにより、Mobile Services に接続された Windows ストア アプリを作成するための簡単な 3 つの手順が表示されます。

  	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

3. まだ [Visual Studio 2013][Visual Studio 2013 Express for Windows] をダウンロードおよびインストールしていない場合は、これらをダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** で、アプリケーションの言語を選択し、**[ダウンロード]** をクリックします。 

  	これにより、Mobile Services に接続する、*To do list* サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Windows アプリケーションを実行する

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]Mobile Services にアクセスして default.js ファイルにあるデータを照会および挿入するコードを確認できます。

## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。 

* [アプリへの認証の追加][認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [アプリケーションにプッシュ通知を追加する][プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

ユニバーサル Windows アプリの詳細については、「[シングル Mobile Services から複数のデバイス プラットフォームをサポートする](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs)」をご覧ください。

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[データの使用]: ../mobile-services-javascript-backend-windows-universal-javascript-get-started-data.md
[認証の使用]: mobile-services-windows-store-javascript-get-started-users.md
[プッシュ通知の使用]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=52--> 