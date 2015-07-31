<properties
   pageTitle="技術概要"
   description="Service Fabric の技術概要。主要な概念とアーキテクチャの概要について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Service Fabric の技術概要

Service Fabric とは、スケーラブルで信頼性が高い、低待機時間の管理しやすいクラウド向けアプリケーションを簡単に構築できる分散型システム プラットフォームです。つまり、Service Fabric によってアプリケーションの利用可能性とスケーリング可能性が確保され、ユーザーはビジネス ニーズに集中することができます。

## 主要な概念

**クラスター** -ネットワークに接続した一連の仮想マシンや物理マシンにアプリケーション インスタンスをデプロイした環境のことです。クラスターは多数のマシンにスケールできます。

**ノード** - クラスター内のアドレス指定可能なユニットのことです。ノードには、配置プロパティや一意の ID などの特性があります。ノードはクラスターに参加することができ、Fabric.exe が実行されているオペレーティング システムのインスタンスに関連付けることができます。

**アプリケーション/アプリケーションの種類** - (マイクロ) サービスのコレクションのことです。アプリケーションの種類は、1 つまたは複数のサービスの種類のコンテナーと考えることができます。クラスター (それ自体が複数のノードで構成される) を複数の ApplicationTypes で構成する方法の詳細については、[アプリケーション モデル](service-fabric-application-model.md)に関する記事を参照してください。

**サービス/サービスの種類** - スタンドアロン (個別に開始および実行できる) の機能を実行するコードと構成のことです。例として、Queue サービスやデータベース サービスがあります。ApplicationType は 1 つ以上の ServiceTypes で構成されます。サービスの種類として次の 2 種類があります。

- ステートレス サービス: Azure データベースや Azure テーブル ストアなどの外部ストレージに保存される状態を持つサービスのことです。このサービスのインスタンスがアクティブになっているノードが停止した場合は、別のインスタンスが別のノードで自動的に開始されます。

- ステートフル サービス: クラスターの他のノードにあるレプリカ間のレプリケーションによって状態を保持し、信頼性を実現するサービスのことです。ステートフル サービスでは、1 つのプライマリ レプリカと複数のセカンダリ レプリカを使用します。このサービスのレプリカがアクティブになっているノードが停止した場合、新しいレプリカが別のノードで開始されます。停止したレプリカがプライマリ レプリカであった場合は、セカンダリ レプリカが自動的に昇格されて新しいプライマリになります。

**アプリケーション インスタンス** - クラスターでは、同じアプリケーションの種類を持つ多数のアプリケーション インスタンスを作成し、各インスタンスに特定の名前を付けることができます。各アプリケーション インスタンスを個別に管理できます。バージョン管理も個別に可能で、同じ種類または異なる種類の他のアプリケーション インスタンスと区別できます。さらに、リソースとセキュリティの分離が定義されます。

**サービス インスタンス** - あるサービスの種類としてインスタンス化されているコードのことです。各サービス インスタンスは、`fabric:/` で始まる一意の名前を持ち、特定の名前が付いたアプリケーション インスタンスと関連付けられています。

**アプリケーション パッケージ** - 特定のアプリケーション用として結合された、サービス コードのパッケージと構成ファイルのコレクションのことです。アプリケーション パッケージは、デプロイされた物理ファイルであり、簡単に言えばファイルとフォルダーの形式レイアウトです。たとえば、電子メール アプリケーションのアプリケーション パッケージには、Queue サービス パッケージ、フロントエンド サービス パッケージ、データベース サービス パッケージが含まれることがあります。

**プログラミング モデル** - Service Fabric には、アプリケーションを構築するためのプログラミング モデルが 2 種類あります。

- Reliable Services - .NET クラス `StatelessService` および `StatefulService` に基づくステートレス サービスとステートフル サービスを構築するための API です。状態を .NET の信頼性の高いコレクション (Dictionary と Queue) に格納し、Web API や WCF などのさまざまな通信スタックをプラグインとして使用できます。このプログラミング モデルは、状態の複数の単位でコンピューティングを実行する必要があるアプリケーションに適しています。

- Reliable Actors - 仮想アクター プログラミング モデルを使用してステートレス オブジェクトとステートフル オブジェクトを構築するための API です。このモデルは、複数の独立した状態の単位とコンピューティングの単位を持つアプリケーションに適しています。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
Service Fabric の詳細については、以下の情報を参照してください。

- [アプリケーション モデル](service-fabric-application-model.md)
- [アプリケーションのシナリオ](service-fabric-application-scenarios.md)
 

<!---HONumber=July15_HO2-->