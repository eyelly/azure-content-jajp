<properties 
	pageTitle="Azure API Management で相互証明書認証を使用してバックエンド サービスを保護する方法" 
	description="Azure API Management で相互証明書認証を使用してバックエンド サービスを保護する方法について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Azure API Management で相互証明書認証を使用してバックエンド サービスを保護する方法

API Management には、相互証明書を使用して API のバックエンド サービスへのアクセスを保護する機能が備わっています。このガイドは、API Management コンソールで証明書を管理する方法、および証明書を使用してバックエンド サービスにアクセスするように API を構成する方法を示しています。

> API Management REST API を使用して証明書を管理する方法について詳しくは、「[Azure API Management REST API 証明書エンティティ][Azure API Management REST API 証明書エンティティ]」を参照してください。

## このトピックの内容

-   [前提条件][前提条件]
-   [クライアント証明書のアップロード][クライアント証明書のアップロード]
-   [クライアント証明書の削除][クライアント証明書の削除]
-   [プロキシ認証に相互証明書を使用するように API を構成する][プロキシ認証に相互証明書を使用するように API を構成する]

## <a name="prerequisites"> </a>前提条件

このガイドは、相互証明書認証を使用して API のバックエンド サービスにアクセスするように、API Management サービス インスタンスを構成する方法を示しています。このトピックの手順を実行する前に、バックエンド サービスが相互証明書認証用に構成されていること、および API Management コンソールでのアップロードのために証明書へのアクセス権限と証明書のパスワードとを持っていることが必要です。

## <a name="step1"> </a>クライアント証明書のアップロード

最初に、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

![API Management console][api-management-management-console]

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

左側の **[API Management]** メニューで **[セキュリティ]** をクリックして、**[クライアント証明書]** をクリックします。

![クライアント証明書][api-management-security-client-certificates]

新しい証明書をアップロードするには、**[証明書のアップロード]** をクリックします。

![証明書のアップロード][api-management-upload-certificate]

使用する証明書を参照してから、証明書のパスワードを入力します。

> 証明書は、**.pfx** 形式でなければなりません。自己署名証明書も許可されます。

![証明書のアップロード][api-management-upload-certificate-form]

**[アップロード]** をクリックして、証明書をアップロードします。

> 証明書のパスワードは、このとき検証されます。パスワードが正しくない場合には、エラー メッセージが表示されます。

![アップロードされた証明書][api-management-certificate-uploaded]

証明書は、アップロードされた後に、**[クライアント 証明書]** タブに表示されます。複数の証明書がある場合は、以下の「[プロキシ認証に相互証明書を使用するように API を構成する][プロキシ認証に相互証明書を使用するように API を構成する]」セクションで説明されているように、証明書を使用するように API を構成するとき証明書の選択に使用される、件名または拇印の最後の 4 文字を参照します。

## <a name="step1a"> </a>クライアント証明書の削除

証明書を削除するには、対象の証明書の横にある **[削除]** をクリックします。

![証明書の削除][api-management-certificate-delete]

**[はい、削除します (Yes, delete it)]** をクリックして確定します。

![削除の確定][api-management-confirm-delete]

証明書が API によって使用されている場合、警告画面が表示されます。証明書を削除するには、最初に、証明書を使用するように構成されたすべての API からそれを削除する必要があります。

![削除の確定][api-management-confirm-delete-policy]

## <a name="step2"> </a>プロキシ認証に相互証明書を使用するように API を構成する

左側の **[API Management]** メニューで **[API]** をクリックして、対象となる API の名前をクリックしてから、**[セキュリティ]** タブをクリックします。

![API セキュリティ][api-management-api-security]

**[証明書を使用 (With credential)]** ドロップダウン リストで、**[相互証明書]** を選択します。

![相互証明書][api-management-mutual-certificates]

**[クライアント 証明書]** ドロップダウン リストから必要な証明書を選択します。複数の証明書がある場合は、以前のセクションに記載されているとおり、件名または拇印の最後の 4 文字に注目して対象となる証明書を判別できます。

![証明書の選択][api-management-select-certificate]

**[保存]** をクリックして API に対する構成の変更を保存します。

> この変更は即時に有効になり、その API の操作の呼び出しは、証明書を使用してバックエンド サーバーを認証するようになります。

![API 変更の保存][api-management-save-api]

> API のバックエンド サービスのプロキシ認証に証明書が指定されると、それはその API のプロキシの一部となり、ポリシー エディターで表示可能となります。

![証明書ポリシー][api-management-certificate-policy]

  [Azure API Management REST API 証明書エンティティ]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [前提条件]: #prerequisites
  [クライアント証明書のアップロード]: #step1
  [クライアント証明書の削除]: #step1a
  [プロキシ認証に相互証明書を使用するように API を構成する]: #step2
  [api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png

<!--HONumber=46--> 
 