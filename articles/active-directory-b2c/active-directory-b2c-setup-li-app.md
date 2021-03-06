<properties
	pageTitle="Azure Active Directory B2C プレビュー: LinkedIn の構成 | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、LinkedIn アカウントを使用するコンシューマーにサインアップとサインインを提供します"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Azure Active Directory (AD) B2C プレビュー: LinkedIn アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## LinkedIn アプリケーションを作成する

Azure Active Directory (AD) B2C で ID プロバイダーとして LinkedIn を使用するには、最初に LinkedIn アプリケーションを作成し、適切なパラメーターを提供する必要があります。そのためには LinkedIn アカウントが必要です。ない場合は、[https://www.linkedin.com/](https://www.linkedin.com/) で取得できます。

1. [LinkedIn Developers](https://www.developer.linkedin.com/) の Web サイトに移動し、LinkedIn アカウントの資格情報でサインインします。
2. 上部メニュー バーの **[My Apps]** をクリックし、**[Create Application]** をクリックします。

    ![LinkedIn - 新しいアプリ](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. **[Create a New Application]** フォームで、関連する情報 (**[Company Name]**、**[Name]**、**[Description]**、**[Application Logo URL]**、**[Application Use]**、**[Website URL]**、**[Business Email]**、**[Business Phone]**) を入力します。
4. LinkedIn API の使用条件に同意し、**[Submit]** をクリックします。

    ![LinkedIn - アプリの登録](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. **[Client ID]** および **[Client Secret]** の値をコピーします (**[Authentication Keys]** セクションの下にあります)。ディレクトリで ID プロバイダーとして LinkedIn を構成するには、両方の値が必要です。

    > [AZURE.NOTE]**[Client Secret]** は、重要なセキュリティ資格情報です。

6. **[OAuth 2.0]** セクションの **[Authorized Redirect URLs]** フィールドに「[https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)」と入力します。**{directory}** は実際のディレクトリの名前に置き換えます (例: contoso.onmicrosoft.com)。**[Add]** をクリックします。**[Update]** をクリックします。

    ![LinkedIn - アプリの設定](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## ディレクトリで ID プロバイダーとして LinkedIn を構成する

1. [Azure プレビュー ポータルで B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。たとえば、「LI」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[LinkedIn]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した LinkedIn アプリケーションの **[クライアント ID]** と **[クライアント シークレット]** を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして LinkedIn の構成を保存します。

<!---HONumber=Oct15_HO3-->