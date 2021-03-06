<properties
	pageTitle="チュートリアル: Azure Active Directory と Halogen Software の統合"
	description="Azure Active Directory と Halogen Software の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と Halogen Software の統合

このチュートリアルの目的は、Halogen Software と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Halogen Software と Azure AD の統合には、次の利点があります。

- Halogen Software にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Halogen Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Halogen Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の Halogen Software サブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Halogen Software の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Halogen Software の追加
Azure AD への Halogen Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Halogen Software を追加する必要があります。

**ギャラリーから Halogen Software を追加するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br> ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]
6. [検索] ボックスに、「**halogen software**」と入力します。<br> ![アプリケーション][5]
7. 結果ウィンドウで **[Halogen Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br>



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Halogen Software で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Halogen Software ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Halogen Software の関連ユーザーの間で、リンクの関係が確立されている必要があります。<br> このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Halogen Software の **[Username]** の値として割り当てます。
 
Halogen Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[単一の Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Halogen Software のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Halogen Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### 単一の Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、Halogen Software アプリケーションでシングル サインオンを構成するにです。<br>

**Halogen Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **[Halogen Software]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![シングル サインオンの構成][8]

2. **[ユーザーの Halogen Software へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][9]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行します。<br><br>![アプリケーションの設定の構成][10]
 
     3\.1. **[サインオン URL]** ボックスに、次のパターンを使用して、ユーザーが Halogen Software アプリケーションへのサインオンに使用する URL を入力します: **https://global.hgncloud.com/fabrikam/welcome.jsp*。

     3\.2.**[次へ]** をクリックします。
 
4. **[Halogen Software でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。<br><br>![What is Azure AD Connect][11]

5. 別のブラウザー ウィンドウで、管理者として **Halogen Software** アプリケーションにサインオンします。
6. **[Options]** タブをクリックします。<br><br>![What is Azure AD Connect][12]
7. 左のナビゲーション ウィンドウで、**[SAML Configuration]** をクリックします。<br><br>![What is Azure AD Connect][13]
8. **[SAML Configuration]** ページで、次の手順を実行します。<br><br>![What is Azure AD Connect][14]

     8\.1.**[Unique Identifier]** として **[NameID]** を選択します。

     8\.2.**[Unique Identifier Maps To]** として **[Username]** を選択します。

     8\.3.ダウンロードしたメタデータ ファイルをアップロードするには、**[Browse]** をクリックしてファイルを選択し、**[Upload File]** をクリックします。

     8\.4.構成をテストするには、**[Run Test]** をクリックします。 > [AZURE.NOTE] "*The SAML test is complete.Please close this window*" というメッセージが表示されるまで待機する必要があります。次に、開いているブラウザー ウィンドウを閉じます。<br>**[Enable SAML]** チェック ボックスは、テストが完了した場合にのみ有効にします。

     8\.5.**[Enable SAML]** を選択します。
    
     8\.6**[変更を保存]** をクリックします。


9. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。<br><br>![What is Azure AD Connect][15]
10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。<br><br>![What is Azure AD Connect][16]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![What is Azure AD Connect][100] 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. ユーザーの一覧を表示するには、上部のメニューで **[ユーザー]** をクリックします。<br><br>![What is Azure AD Connect][101] 
4. **[ユーザーの追加]** ダイアログを開くには、下部にあるツール バーで **[ユーザーの追加]** をクリックします。<br><br>![What is Azure AD Connect][102] 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br>![What is Azure AD Connect][103] 
  1. **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
  2. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。
6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![What is Azure AD Connect][104] 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]** をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br>![What is Azure AD Connect][105]  
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![What is Azure AD Connect][106]   
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   
  
 
### Halogen Software のテスト ユーザーの作成

このセクションの目的は、Halogen Software で Britta Simon というユーザーを作成することです。

**Halogen Software で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 管理者として **Halogen Software** アプリケーションにサインオンします。
2. **[User Center]** タブをクリックし、**[Create User]** をクリックします。<br><br>![What is Azure AD Connect][300]  
3. **[New User]** ダイアログ ページで、次の手順に従います。<br><br>![What is Azure AD Connect][301]
  1. **[First Name]** ボックスに「**Britta**」と入力します。 
  2. **[Last Name]** ボックスに「**Simon**」と入力します。
  3. **[Username]** ボックスに **Azure AD ポータルでの Brita Simon のユーザー名**を入力します。
  4. **[Password]** ボックスに Britta のパスワードを入力します。
  5. **[保存]** をクリックします。


### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Halogen Software へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![What is Azure AD Connect][200]

**Halogen Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br> <br><br>![What is Azure AD Connect][201]
2. アプリケーションの一覧で **[Halogen Software]** を選択します。<br><br>![What is Azure AD Connect][202]
1. 上部のメニューで **[ユーザー]** をクリックします。<br> <br><br>![What is Azure AD Connect][203]
1. ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![What is Azure AD Connect][204]
2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![What is Azure AD Connect][205]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br>
アクセス パネルで Halogen Software のタイルをクリックすると、自動的に Halogen Software アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=Oct15_HO3-->