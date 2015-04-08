﻿<properties 
	pageTitle="Azure Government 開発者向けガイド" 
	description="Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="1/21/2014" 
	ms.author="jharve"/>


# Microsoft Azure Government 開発者向けガイド 

<p> Microsoft Azure Government は、Microsoft Azure の物理的、ネットワーク的に独立したインスタンスです。この開発者向けガイドには、アプリケーション開発者と管理者が、Azure のこれらの異なるリージョンとやり取りし、操作する必要がある場合の相違点についての詳しい説明が記載されています。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## このトピックの内容


+ [概要](#Overview)
+ [開発者向けのガイダンス](#Guidance)
+ [Microsoft Azure Government で現在使用できる機能](#Features)
+ [エンドポイント マッピング](#Endpoint)
+ [次のステップ](#next)


## <a name="Overview"></a>概要

Microsoft Azure Government は、米国連邦政府機関、州、地方自治体、そのソリューション プロバイダーのセキュリティとコンプライアンスのニーズを満たす Microsoft Azure サービスの個別のインスタンスです。Azure Government は、米国政府機関以外のデプロイから物理的、ネットワーク的に独立しており、選別された米国のスタッフによって運営されます。 

Microsoft では、クラウド アプリケーションの作成、Microsoft のグローバルな Microsoft Azure サービス ("グローバル サービス") と Microsoft Azure Government サービスにデプロイするための多数のツールを提供します。

アプリケーションを作成し、グローバル サービスではなく Azure Government サービスにデプロイするとき、開発者はその 2 つのサービスの主な違いを把握する必要があります(特に、プログラム環境の設定と構成、エンドポイントの構成、アプリケーションの記述、それらをサービスとした Azure Government へのデプロイについて)。

このドキュメント内の情報は、これらの相違点をまとめ、[Azure Government](http://www.azure.com/gov "Azure Government") サイトと MSDN の [Microsoft Azure のテクニカル ライブラリ](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")に記載されている情報を補足するものです。公式の情報は、[Microsoft Azure のトラスト センター](http://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center")、[Azure ドキュメント センター](http://azure.microsoft.com/documentation/)、[Azure ブログ](http://azure.microsoft.com/blog/ "Azure Blogs")など、他のさまざまな場所にも記載されています。 

このコンテンツは、Microsoft Azure Government にデプロイしているパートナーと開発者に適しています。



## <a name="Guidance"></a>開発者向けのガイダンス
現在利用可能な技術的なコンテンツのほとんどは、Microsoft Azure Government ではなく、グローバル サービス向けにアプリケーションを開発中であることを前提としているため、開発者は、Azure Government でホストされるように開発されたアプリケーションの主な相違点を確実に意識することが重要です。

- まず、サービスと機能に相違点があります。これは、グローバル サービスの特定のリージョンにある特定の機能は、Azure Government では使用できないことを意味します。

- 第 2 に、Azure Government で提供されている機能の場合、グローバル サービスとは構成上の相違点があります。そのため、サンプル コード、構成、手順を確認し、Azure Government クラウド サービス環境内で確実に構築と実行するようにしてください。


## <a name="Features"></a> Microsoft Azure Government で現在使用できる機能
現在、Azure Government では、米国政府アイオワ州、米国政府バージニア州の両方のリージョンで、次のサービスを使用できます。

- 仮想マシン
- クラウド サービス
- ストレージ
- Active Directory
- Scheduler
- 仮想ネットワーク
- SQL Database

その他のサービスを利用でき、より多くのサービスが継続的に追加される予定です。最新のサービスのリストについては、使用できる各リージョンとそのサービスについて取り上げる[リージョン ページ](http://azure.microsoft.com/regions/#services)をご覧ください。  

現時点では、Azure Government をサポートするデータ センターは、米国政府アイオワ州と米国政府バージニア州です。現在のデータ センターと利用可能なサービスについては、上記リージョン ページをご覧ください。

## <a name="Endpoint"></a>エンドポイント マッピング

次の表を使用すると、Microsoft Azure と SQL データベースのパブリック エンドポイントを Azure Government 特定のエンドポイントにマッピングするときに役立ちます。

<table>
<tr style='font-weight:bold'><td>
サービスの種類</td><td>	Azure Public</td><td>	Azure Government
</td></tr><tr><td>
Azure Government Home</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
管理ポータル</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
全般</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
コア	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
コンピューティング	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
BLOB ストレージ</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
キュー ストレージ	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
テーブル ストレージ</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
サービス管理</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>SQL Database</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table> 

## <a name="next"></a>次のステップ
Azure Government の詳細と、企業からのアクセス権については、「<A href="http://azure.com/gov">http://www.azure.com/gov</a>」をご覧ください。

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[別の azure.microsoft.com ドキュメントのトピックへのリンク 1]: ../virtual-machines-windows-tutorial/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 2]: ../web-sites-custom-domain-name/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 3]: ../storage-whatis-account/

<!--HONumber=47-->