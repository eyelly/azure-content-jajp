﻿<properties 
	pageTitle="REST で ContentKey を作成する" 
	description="アセットに安全にアクセスできる ContentKey を作成する方法について学習します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#REST で ContentKey を作成する

この記事は、[Media Services Video on Demand workflow (メディア サービス ビデオ オンデマンド ワークフロー)](../media-services-video-on-demand-workflow) や [Media Services Live Streaming workflow (メディア サービス ライブ ストリーミング ワークフロー)](../media-services-live-streaming-workflow) シリーズの一部です。  

メディア サービスでは、暗号化されたアセットを新しく作成して送信できます。**ContentKey** で**アセット**に安全にアクセスできます。 

新しいアセットを作成するときは (たとえば[ファイルをアップロードする](../media-services-rest-upload-files/)前)、**StorageEncrypted**、**CommonEncryptionProtected**、**EnvelopeEncryptionProtected** などの暗号化オプションを指定できます。 

アセットをクライアントに送信するときは、[アセットを動的に暗号化するように構成](../media-services-rest-configure-asset-delivery-policy)できます。その際、**DynamicEnvelopeEncryption** か **DynamicCommonEncryption** のいずれか 1 つを使用します。

暗号化されたアセットには、**ContentKey** を関連付ける必要があります。この記事では、コンテンツ キーを作成する方法について説明します。

暗号化するアセットに関連付けるコンテンツ キーを生成する一般的な手順を次に示します。 

1. 16 バイトの AES キー (CommonEncryption と EnvelopeEncryption 向け) か 32 バイトの AES キー (StorageEncryption 向け) をランダムに生成します。 

	これがアセットのコンテンツ キーになります。つまりこのアセットに関連するファイルは、暗号化の際に同じコンテンツ キーを使う必要があるということです。 
2.	[GetProtectionKeyId](https://msdn.microsoft.com/ja-jp/library/azure/jj683097.aspx#getprotectionkeyid) メソッドと [GetProtectionKey](https://msdn.microsoft.com/ja-jp/library/azure/jj683097.aspx#getprotectionkey) メソッドをコールして、コンテンツ キーを暗号化するために必要な適切な X.509 証明書を取得します。
3.	X.509 証明書の公開キーでコンテンツ キーを暗号化します。 

	Media Services .NET SDK では、暗号化の際に OAEP と RSA を使用します。具体例については、[EncryptSymmetricKeyData function](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Encryption/EncryptionUtils.cs) をご覧ください。
4.	そのキー識別子とコンテンツ キーを使って計算される、(PlayReady AES キー checksum アルゴリズムに基づく) checksum 値を作成します。詳細については、[こちら](http://www.microsoft.com/playready/documents/) で PlayReady Header Object ドキュメントの「PlayReady AES Key Checksum Algorithm (PlayReady AES キー checksum アルゴリズム)」セクションをご覧ください。

	次の .NET の例では、キー識別子の GUID 部とクリアなコンテンツ キーを使用して checksum を計算しています。 
	
		public static string CalculateChecksum(byte[] contentKey, Guid keyId)
		{
		    byte[] array = null;
		    using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
		    {
		        aesCryptoServiceProvider.Mode = CipherMode.ECB;
		        aesCryptoServiceProvider.Key = contentKey;
		        aesCryptoServiceProvider.Padding = PaddingMode.None;
		        ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
		        array = new byte[16];
		        cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
		    }
		    byte[] array2 = new byte[8];
		    Array.Copy(array, array2, 8);
		    return Convert.ToBase64String(array2);
		}

5. 前の手順で取得した **EncryptedContentKey** 値 (Base 64 エンコード形式の文字列に変換されます)、**ProtectionKeyId** 値、**ProtectionKeyType** 値、**ContentKeyType** 値、**Checksum** 値を使ってコンテンツ キーを作成します。
6. $links 操作で、**ContentKey** エンティティと **Asset** エンティティを関連付けます。

AES キーの生成、キーの暗号化、checksum 計算の例は、このトピックから削除されました。Media Servicesを操作する方法についての例のみご覧いただけます。


>[AZURE.NOTE] Media Services REST API を使用する場合は、次のことに考慮します。
>
>メディア サービスでエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。詳細については、[Setup for Media Services REST API Development (Media Services REST API 開発の設定)](../media-services-rest-how-to-use) をご覧ください。

>「https://media.windows.net」へ正常に接続すると、別のメディア サービス URI が指定された 301 リダイレクトが表示されます。[Media Services REST API を使用してメディア サービス アカウントに接続する](../media-services-rest-connect_programmatically/)で説明されているように、新しい URI に続けてコールを行う必要があります。 

##ProtectionKeyId の取得 
 

次の例では、コンテンツ キーを暗号化するときに必要な ProtectionKeyId と証明書のサムプリントを取得する方法を示します。この手順を実行してコンピューターに適切な証明書があることを確認します。


要求: 
	
	
	GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
	x-ms-version: 2.8
	Host: media.windows.net
	

応答: 
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 139
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
	x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 04 Feb 2015 02:42:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

##ProtectionKeyId の ProtectionKey の取得

次の例では、前の手順で受け取った ProtectionKeyId を使用して、X.509 証明書を取得する方法を示します。

要求: 
		
	GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
	Host: media.windows.net
	


応答: 
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1227
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
	request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
	x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 05 Feb 2015 07:52:30 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
	"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

##ContentKey を作成します。 

X.509 証明書を取得して、その公開キーを使ってコンテンツ キーを暗号化した後、**ContentKey** エンティティを作成してプロパティの値を適宜設定します。

コンテンツ キー作成時に設定する必要がある値の一つは type です。次のいずれかの値を選択します。

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }


この例では、**ContentKey** を **ContentKeyType** セットと作成する方法について示しています。StorageEncryption を ("1") に、**ProtectionKeyType** を "0" に設定し、保護キー ID が X.509 証明書のサムプリントであることを示します。  


要求

	POST https://media.windows.net/api/ContentKeys HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
	x-ms-version: 2.8
	Host: media.windows.net
	{
	"Name":"ContentKey",
	"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
	"ContentKeyType":"1", 
	"ProtectionKeyType":"0",
	"EncryptedContentKey":"your encrypted content key",
	"Checksum":"calculated checksum"
	}


応答: 
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 777
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
	Server: Microsoft-IIS/8.5
	request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
	x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 04 Feb 2015 02:37:46 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
	"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
	"LastModified":"2015-02-04T02:37:46.9684379Z",
	"ContentKeyType":1,
	"EncryptedContentKey":"your encrypted content key",
	"Name":"ContentKey",
	"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
	"ProtectionKeyType":0,
	"Checksum":"calculated checksum"}

##ContentKey をアセットに関連付ける

ContentKey を作成した後、次の例に示すように $links 演算子を使用して ContentKey をアセットに関連付けます。
	
要求: 
	
	POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
	x-ms-version: 2.8
	Host: media.windows.net

	
	{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

応答: 

	HTTP/1.1 204 No Content
<!--HONumber=45--> 