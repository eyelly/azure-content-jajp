<properties 
    pageTitle="チュートリアル: Azure Active Directory と OverDrive Books の統合 | Microsoft Azure" 
    description="Azure Active Directory で Overdrive Books を使用して、シングル サインオンを有効にする方法、プロビジョニングを自動化する方法などについて説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#チュートリアル: Azure Active Directory と OverDrive Books の統合
  
このチュートリアルの目的は、Azure と OverDrive の統合を紹介することです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   OverDrive でのシングル サインオンが有効なサブスクリプション
  
このチュートリアルを終了すると、OverDrive に割り当てた Azure AD ユーザーは、OverDrive 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。
  
このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  OverDrive のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "シナリオ")
##OverDrive のアプリケーション統合の有効化
  
このセクションでは、OverDrive のアプリケーション統合を有効にする方法について説明します。

###OverDrive のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "アプリケーション")

4.  ページの下部にある **[追加]** をクリックします。

    ![Add application](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")

6.  **検索ボックス** に、「**OverDrive**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "アプリケーション ギャラリー")

7.  結果ウィンドウで **[OverDrive]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##シングル サインオンの構成
  
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで OverDrive に対する認証を行えるようにする方法を説明します。

###シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **OverDrive** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "シングル サインオンの有効化")

2.  **[ユーザーの OverDrive へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選び、**[次へ]** をクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "シングル サインオンの構成")

3.  **[アプリ URL の構成]** ページの **[OverDrive サインイン URL]** テキストボックスに、"**http://mslibrarytest.libraryreserve.com*" というパターンの URL を入力して、**[次へ]** をクリックします。

    ![アプリケーション URL の構成](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "アプリケーション URL の構成")

4.  **[OverDrive でのシングル サインオン構成]** ページで、メタデータ ファイルをダウンロードし、OverDrive サポート チームに送信します。

    ![シングル サインオンの構成](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "シングル サインオンの構成")

    >[AZURE.NOTE]OverDrive サポート チームはシングル サインオンを構成し、構成の完了時に通知を送信します。

5.  Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。

    ![シングル サインオンの構成](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "シングル サインオンの構成")
##ユーザー プロビジョニングの構成
  
OverDrive へのユーザー プロビジョニングの構成にあたって必要な操作はありません。割り当てられているユーザーが OverDrive にログインしようとすると、必要に応じて OverDrive アカウントが自動的に作成されます。

>[AZURE.NOTE]OverDrive から提供されている他の OverDrive ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

##ユーザーを割り当てる
  
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

###ユーザーを OverDrive に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **OverDrive** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

    ![ユーザーの割り当て](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "ユーザーの割り当て")

3.  テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

    ![あり](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "あり")
  
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=Nov15_HO1-->