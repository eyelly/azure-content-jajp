<properties 
	pageTitle="Axinom を使用して Azure Media Services に Widevine ライセンスを配信する" 
	description="この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。PlayReady ライセンスは Media Services PlayReady サーバーから取得し、Widevine ライセンスは Axinom ライセンス サーバーから取得します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Axinom を使用して Azure Media Services に Widevine ライセンスを配信する  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##概要

Azure Media Services (AMS) で Google Widevine の動的な保護がサポートされるようになりました (詳細については、[Mingfei のブログ](https://azure.microsoft.com/ja-JP/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)を参照してください)。さらに、Azure Media Player (AMP) では、Widevine がサポートされるようになりました (詳細については、[AMP のドキュメント](http://amp.azure.net/libs/amp/latest/docs/)を参照してください)。これは、MSE と EME を搭載する最新のブラウザー上でマルチネイティブ DRM (PlayReady と Widevine) による CENC で保護された DASH コンテンツのストリーミングにおける大きな成果です。

>[AZURE.NOTE]現在、Media Services では、Widevine ライセンス サーバーは提供されていません。Widevine ライセンスは、AMS パートナーの [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/) を通して配信できます。

この記事では、Axinom が管理する Widevine ライセンス サーバーを統合し、テストする方法について説明します。具体的には、次の項目について説明します。

- マルチ DRM (PlayReady と Widevine) とそれに対応するライセンス購入 URL を使用して動的共通暗号化を構成する。
- ライセンス サーバーの要件を満たすために JWT トークンを生成する。
- JWT トークン認証によるライセンスの取得を処理する Azure Media Player アプリを開発する。

次の図では、システム全体と、コンテンツ キー、キー ID、キー シード、JTW トークン、およびその要求のフローをうまく示しています。

![DASH and CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##コンテンツ保護

動的な保護とキー配信ポリシーの構成については、Mingfei のブログの [Azure Media Services で Widevine パッケージ化を構成する方法](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)に関する記事を参照してください。

次の両方の保護を備えた DASH ストリーミングに対して、マルチ DRM による動的な CENC 保護を構成できます。

1. トークンの承認制限を設定できる MS Edge および IE11 用の PlayReady 保護。トークン制限ポリシーには、Azure Active Directory などの Secure Token Service (STS) によって発行されたトークンを含める必要があります。
1. Chrome 用 Widevine 保護。別の STS によって発行されたトークンを使用したトークンの認証を要求する場合があります。 

Axinom の Widevine ライセンス サーバーの STS として Azure Active Directory を使用できない理由については、「[JWT トークンの生成](media-services-axinom-integration.md#jwt-token-generation)」セクションを参照してください。

###考慮事項

1. キー配信サービスを構成するためのコンテンツ キーを生成するには、Axinom によって指定されたキー シード (8888000000000000000000000000000000000000) と、自分で生成または選択したキー ID を使用する必要があります。Axinom ライセンス サーバーは、テストと運用の両方に有効な同じキー シードに基づくコンテンツ キーを含むライセンスをすべて発行します。
1. テスト用の Widevine ライセンス取得 URL: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)。HTTP と HTTS の両方が許可されています。

##Azure Media Player の準備

AMP v1.4.0 では、PlayReady DRM と Widevine DRM の両方で動的にパッケージ化されている AMS コンテンツの再生がサポートされています。Widevine ライセンス サーバーでトークン認証が必要ない場合は、Widevine で保護されている DASH コンテンツをテストするために追加で必要となる操作はありません。たとえば、AMP チームから提供されている簡単なサンプル [http://amp.azure.net/libs/amp/latest/samples/dynamic\_multiDRM\_PlayReadyWidevine\_notoken.html](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html) は、Edge と IE11 では PlayReady によって、Chrome では Widevine によって動作していることがわかります。Axinom が提供する Widevine ライセンス サーバーでは、JWT トークン認証が必要です。JWT トークンは、HTTP ヘッダー "X-AxDRM-Message" を使用してライセンス要求と共に送信する必要があります。そのため、ソースを設定する前に、AMP をホストする Web ページに次の JavaScript を追加する必要があります。

	<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

AMP コードの残りの部分は、[こちら](http://amp.azure.net/libs/amp/latest/docs/)の AMP ドキュメントで示されるように標準の AMP API です。

カスタム承認ヘッダーを設定するための上記の JavaScript は、AMP の公式な長期的なアプローチがリリースされるまでの短期的なアプローチであることに注意してください。

##JWT トークンの生成

テスト用の Widevine ライセンス サーバーでは、JWT トークン認証が必要です。さらに、JWT トークンの要求の 1 つは、プリミティブ データ型ではなく複合オブジェクト型です。

残念ながら、Azure AD が発行できるのは、プリミティブ型を持つ JWT トークンのみです。同様に、.NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler と JwtPayload) では、複合オブジェクト型のみを要求として入力できます。ただし、要求は、現在も文字列としてシリアル化されます。したがって、Widevine ライセンス要求用の JWT トークンを生成するためにこの 2 つのどちらも使用できません。


ここでは、このニーズを満たしている John Sheehan の [JWT Nuget パッケージ](https://www.nuget.org/packages/JWT)を使用します。

テスト用の Axinom Widevine ライセンス サーバーで求められる必要な要求を含む JWT トークンを生成するためのコードを次に示します。

	
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.IdentityModel.Tokens;
	using System.IdentityModel.Protocols.WSTrust;
	using System.Security.Claims;
	
	namespace OpenIdConnectWeb.Utils
	{
	    public class JwtUtils
	    {
	        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
	        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
	        {
	            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
	
	            var payload = new Dictionary<string, object>()
	                         {
	                             { "version", 1 },
	                             { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
	                             { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
	                         };
	
	            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
	
	            return token;
	        }
	
	        //convert hex string to byte[]
	        public static byte[] ConvertHexStringToByteArray(string hexString)
	        {
	            if (hexString.Length % 2 != 0)
	            {
	                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
	            }
	
	            byte[] HexAsBytes = new byte[hexString.Length / 2];
	            for (int index = 0; index < HexAsBytes.Length; index++)
	            {
	                string byteValue = hexString.Substring(index * 2, 2);
	                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
	            }
	
	            return HexAsBytes;
	        }
	
	    }  
	
	}  

Axinom Widevine ライセンス サーバー

	<add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
	<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
	<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
	<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###考慮事項

1.	AMS PlayReady ライセンス配信サービスでは認証トークンの前に "Bearer=" が必要になりますが、Axinom Widevine ライセンス サーバーではこれを使用しません。
2.	Axinom 通信キーは、署名キーとして使用されます。キーは 16 進文字列ですが、エンコードする際に文字列ではなく一連のバイトとして処理する必要があることに注意してください。これは、ConvertHexStringToByteArray メソッドによって実現されます。

##キー ID の取得

JWT トークンを生成するためのコードでは、キー ID が必要です。JWT トークンは、AMP プレーヤーを読み込む前に準備が整っている必要があるため、JWT トークンを生成するためにキー ID を取得する必要があります。

キー ID を取得する方法はいくつかあります。たとえば、キー ID をコンテンツ メタデータと一緒にデータベースに格納できます。また、DASH MPD (Media Presentation Description) ファイルからキー ID を取得することもできます。後者の方法を次のコードに示します。

	//get key_id from DASH MPD
	public static string GetKeyID(string dashUrl)
	{
	    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
	    {
	        dashUrl += "(format=mpd-time-csf)";
	    }
	
	    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
	    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
	    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
	    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
	    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
	    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
	    objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
	    objXmlNamespaceManager.PushScope();
	
	    XPathNodeIterator objXPathNodeIterator;
	    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
	
	    string key_id = string.Empty;
	    if (objXPathNodeIterator.MoveNext())
	    {
	        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
	    }
	
	    return key_id;
	}

##概要

Azure Media Services Content Protection と Azure Media Player の両方で Widevine のサポートが新しく追加されました。これにより、次の最新のブラウザーで、AMS の PlayReady ライセンス サービスと Axinom の Widevine ライセンス サーバーの両方を使用して DASH とマルチネイティブ DRM (PlayReady + Widevine) のストリーミングを実装できます。

- Chrome
- Windows 10 の Microsoft Edge
- Windows 8.1 と Windows 10 の IE 11
- Mac (iOS ではありません) の Firefox (デスクトップ) と Safari の両方も、Silverlight、および Azure Media Player と同じ URL を使用してサポートされています。

Axinom Widevine ライセンス サーバーを利用する解決策では、次のパラメーターが必要です。キー ID 以外の残りのパラメーターは、Widevine サーバーの設定に基づいて Axinom によって提供されます。


![パラメーター](./media/media-services-axinom-integration/media-services-axinom2.png)


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Oct15_HO2-->