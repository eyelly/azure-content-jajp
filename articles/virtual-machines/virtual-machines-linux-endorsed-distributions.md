<properties
	pageTitle="動作保証済み Linux ディストリビューション | Microsoft Azure"
	description="Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、OpenLogic、Oracle、および SUSE に関する指針も含めて、説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"
	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2015"
	ms.author="szark"/>



#Azure での動作保証済み Linux ディストリビューション

Azure ギャラリーにある Linux イメージは多くのパートナーから提供されており、Microsoft はさまざまな Linux コミュニティと協力して、動作保証済みディストリビューションの一覧をよりいっそう充実させようと努めています。ギャラリーから利用できないディストリビューションの場合も、[このページ](virtual-machines-linux-create-upload-vhd.md)の指針に従うことにより、いつでも独自の Linux を展開することができます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## サポートされているディストリビューションとバージョン ##

次の表に、Azure でサポートされている Linux ディストリビューションとバージョンを示します。

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、Microsoft からアップストリームの Linux カーネルに直接提供されるカーネル モジュールです。LIS ドライバーは、ディストリビューションのカーネルに既定で組み込まれているか、以前の RHEL/CentOS ベースのディストリビューションの場合は[こちらで](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)個別にダウンロードする形で入手できます。LIS ドライバーの詳細については、[こちらの記事](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements)を参照してください。

Azure Linux エージェントは、Azure ギャラリーのイメージにあらかじめインストールされており、通常はディストリビューションのパッケージのリポジトリで入手できます。ソース コードは [GitHub](https://github.com/azure/walinuxagent) にあります。

ディストリビューション|バージョン|ドライバー|エージェント
---|---|---|---
Canonical Ubuntu|Ubuntu 12.04、14.04、15.04、および 15.10|カーネル内|パッケージ: リポジトリ内の "WALinuxAgent"<p><p> ソース: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
CentOS by OpenLogic |CentOS 6.3+、7.0+| CentOS 6.3:[LIS ダウンロード](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 以上、カーネル内。|パッケージ: <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">OpenLogic リポジトリ内の "WALinuxAgent"<p><p> ソース: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)|494\.4.0+ |カーネル内|ソース: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Oracle Linux| 6\.4+、7.0+|カーネル内|パッケージ: リポジトリ内の "WALinuxAgent" <p><p>ソース: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
SUSE Linux Enterprise |SLES 11 SP3 +、SLES 12 + と <p><p>SAP 11.3 + 向け SLES |カーネル内|パッケージ: [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の "WALinuxAgent"<p><p> ソース: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE |openSUSE 13.1+|カーネル内|パッケージ: [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の "WALinuxAgent"<p><p> ソース コード: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)

## パートナー

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。Canonical は、Ubuntu に関して、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイスを備え、携帯電話からクラウドまで統一されたフリー プラットフォームにするというビジョンを持っているため、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまで多様な企業が最初に選択するディストリビューションとなり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC からサーバーや携帯機器まで、さまざまな Ubuntu ソリューションを市場に送り出しています。


### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS Web サイトから入手:

*CoreOS はセキュリティ、一貫性、信頼性を実現するように設計されています。yum や apt でパッケージをインストールする代わりに、Linux コンテナーを使用して、より抽象的なレベルでサービスを管理します。コンテナーには 1 つのサービス コードとすべての依存関係がパッケージ化されており、1 つまたは複数の CoreOS マシン上で実行できます。*


### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic は、クラウドおよびデータ センター向けの企業オープン ソース ソリューションの一流プロバイダーです。OpenLogic は、多種多様な業界の一流企業数百社に対して、オープン ソース ソフトウェアを安全に入手し、サポートし、コントロールできるよう支援しています。OpenLogic は、600 種類のオープン ソース パッケージについて、OpenLogic Expert Community に支えられた商用品質のテクニカル サポートと保証を提供しています。たとえば、CentOS を企業レベルでサポートし、Azure に CentOS ベースのイメージを提供する開設時パートナー様でもあります。


### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle の戦略は、パブリック クラウドとプライベート クラウドに対して広範なソリューションを用意し、顧客が Oracle のクラウドや他社のクラウドにおいて Oracle のソフトウェアを柔軟にデプロイできるようにすることです。マイクロソフトと Oracle との提携により、お客様はマイクロソフトのパブリック クラウドおよびプライベート クラウドにおいて Oracle の保証とサポートの下で安心して Oracle のソフトウェアをデプロイできます。Oracle のパブリックおよびプライベート クラウド ソリューションに対する取り組みと投資が変わることはありません。


### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に安心して自信を持ってデプロイできることを保証しています。

<!---HONumber=Nov15_HO1-->