﻿<properties 
	pageTitle="Azure 接続文字列の構成" 
	description="Azure のストレージ アカウントへの接続文字列の設定方法を説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Azure Storage の接続文字列を構成する

## 概要

接続文字列には、Azure のストレージ アカウントにプログラムでアクセスするのに必要な情報が含まれています。次の方法で Azure Storage に接続するための接続文字列を構成できます。

- サービスやアプリケーションをローカルでテストしているときに、Azure ストレージ エミュレーターに接続します。

- ストレージ サービスの既定のエンドポイントを使用して、Azure でストレージ アカウントに接続します。

- ストレージ サービスの明示的なエンドポイントを使用して、Azure でストレージ アカウントに接続します。

アプリケーションが Azure で実行しているクラウド サービスの場合、接続文字列は通常 [Azure サービスの構成スキーマ (.cscfg) ファイル](https://msdn.microsoft.com/library/ee758710.aspx)に保存します。アプリケーションが別の環境 (たとえば、デスクトップで) で実行されている場合、通常、接続文字列は app.config ファイルまたは別の構成ファイルに保存します。Azure `CloudConfigurationManager` クラスを使用すれば、実行場所に関係なく、接続文字列にアクセスできます。

## ストレージ エミュレーターへの接続文字列を作成する

ストレージ エミュレーターは、既知の名前とキーを持つローカル アカウントです。アカウント名とキーはすべてのユーザーで同じであるため、ショートカット文字列の形式を使用して、接続文字列内のストレージ エミュレーターを参照できます。接続文字列の値は、 `UseDevelopmentStorage=true` に設定します。

また、サービスをストレージ エミュレーターでテストする際に使用する HTTP プロキシを指定することもできます。これは、ストレージ サービスに対する操作をデバッグするとき、HTTP の要求と応答を調べる場合に便利です。プロキシを指定するには、 `DevelopmentStorageProxyUri` オプションを接続文字列に追加し、値としてプロキシ URI を設定します。たとえば、次に示す接続文字列は、ストレージ エミュレーターを指しており、HTTP プロキシを構成しています。

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Azure ストレージ アカウントへの接続文字列を作成する

Azure ストレージ アカウントへの接続文字列を作成するには、次の接続文字列の形式を使用します。HTTP と HTTPS (推奨) のどちらを使用してストレージ アカウントに接続するかを指定し、 `myAccountName` をストレージ アカウントの名前に、 `myAccountKey` をアカウント アクセス キーに置き換えます。

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

たとえば、接続文字列は次のサンプルの接続文字列のようになります。

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] Azure Storage では、HTTP と HTTPS の両方の接続文字列をサポートします。ただし、HTTPS の使用を強くお勧めします。
    
## 明示的なストレージ エンドポイントへの接続文字列を作成する

次の場合は、接続文字列でサービス エンドポイントを明示的に指定することができます。

- ストレージ アカウントのカスタム ドメイン名を BLOB サービスとマップしている場合。
- ストレージ アカウント内のストレージ リソースにアクセスするための共有アクセス署名がある場合。

明示的な BLOB エンドポイントを指定する接続文字列を作成するには、次に示す形式を使用して、プロトコル仕様 (HTTP または HTTPS) を含む完全なサービス エンドポイントをサービスごとに指定します。

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

サービス エンドポイントは 1 つ以上指定する必要がありますが、すべてを指定する必要はありません。たとえば、カスタムの BLOB エンドポイントで使用する接続文字列を作成する場合は、キューとテーブルのエンドポイントの指定は省略できます。接続文字列からキューとテーブルのエンドポイントを除外することを選択した場合、その接続文字列を使用してコードからキュー サービスやテーブル サービスにアクセスすることはできないことにご注意ください。

接続文字列でサービス エンドポイントを明示的に指定する場合、上記の文字列に `credentials` を指定するには、次の 2 つのオプションがあります。

- アカウント名とキーを指定する: `AccountName=myAccountName;AccountKey=myAccountKey` 
- 共有アクセス署名を指定する: `SharedAccessSignature=base64Signature`

### カスタム ドメイン名を使用して BLOB エンドポイントを指定する 

BLOB サービスで使用するためにカスタム ドメイン名が登録されている場合は、接続文字列で BLOB エンドポイントを明示的に構成する必要があります。接続文字列に示されているエンドポイント値を使用することで、BLOB サービスへの要求 URI が作成され、コードに返される URI の形式も決定されます。 

たとえば、カスタム ドメイン上の BLOB エンドポイントへの接続文字列は、次のようになります。

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### 共有アクセス署名で BLOB エンドポイントを指定する 

明示的なエンドポイントを持つ接続文字列を作成し、共有アクセス署名を介してストレージ リソースにアクセスできます。この場合、アカウント名とアカウント キーの資格情報ではなく、共有アクセス署名を接続文字列に含めて指定できます。共有アクセス署名トークンは、アクセス先のリソース、アクセス可能な期間、付与される権限に関する情報をカプセル化します。共有アクセス署名の詳細については、「[共有アクセス署名を使用したアクセスの委任](https://msdn.microsoft.com/library/ee395415.aspx)」を参照してください。

共有アクセス署名を含む接続文字列を作成するには、文字列を次の形式で指定します。

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

エンドポイントには、既定のサービス エンドポイントとカスタム エンドポイントのいずれかを指定できます。 `base64Signature` は、共有アクセス署名の署名部分に対応します。署名は、有効な署名対象文字列とキーを SHA256 アルゴリズムを使用して計算処理した後、その値を Base64 エンコードした HMAC 値です。

### エンドポイント サフィックスを含む接続文字列を作成する

Azure China、Azure Governance など、別のエンドポイント サフィックスを持つリージョンまたはインスタンスのストレージ サービスの接続文字列を作成するには、次の接続文字列の形式を使用します。HTTP または HTTPS のどちらでストレージ アカウントに接続するかを示し、 `myAccountName` をストレージ アカウントの名前、 `myAccountKey` をアカウント アクセス キー、 `mySuffix` を URI サフィックスに置き換えます。


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


たとえば、接続文字列は次のサンプルの接続文字列のように表示されます。

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;


<!--HONumber=52-->