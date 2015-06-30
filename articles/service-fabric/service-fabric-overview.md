<properties 
   pageTitle="Service Fabric の概要" 
   description="アプリケーションがマイクロサービスで構成されている Service Fabric を紹介します。Service Fabric とは、スケーラブルで信頼性が高く管理しやすいクラウド向けアプリケーションの構築に使用される分散型システム プラットフォームです。" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Service Fabric の概要
Service Fabric とは、スケーラブルで信頼性が高く管理しやすいクラウド向けアプリケーションの構築に使用される分散型システム プラットフォームです。Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題に対処します。Service Fabric を使用することで、開発者と管理者は複雑なインフラストラクチャの問題を解決する必要がなくなり、代わりにミッション クリティカルで要求の厳しいワークロードの実装に重点を置くことができます。こうしたワークロードは、スケーラブルで信頼性が高く管理しやすいからです。Service Fabric は、これらのエンタープライズ クラスの Tier-1 クラウド スケール サービスを構築して管理するための次世代のミドルウェア プラットフォームを表します。

## マイクロサービスで構成されたアプリケーション ##
Service Fabric を使用すると、マシンの共有プール (通常、Service Fabric クラスターと呼ばれる) 上で超高密度で実行されるマイクロサービスで構成された、スケーラブルで信頼性の高いアプリケーションを構築して管理することができます。分散型のスケーラブルなステートレスおよびステートフル マイクロサービスを構築するための高度なランタイムを提供するとともに、デプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、および削除するための包括的なアプリケーション管理機能を提供します。

現在、Service Fabric は、Azure SQL Database、Azure DocumentDB、Cortana、Power BI、Microsoft Intune、Azure Event Hubs、さまざまなコア Azure サービス、Skype for Business など、多数の Microsoft サービスの機能を強化しています。

Service Fabric は、「クラウド由来」のサービスを作成するように調整されます。これらのサービスは、必要に応じて小規模で開始し、数百または数千ものマシンを含む非常に大きなスケールまで拡張して、リージョン内の可用性セット間またはリージョン間にわたる Service Fabric クラスターを作成することができます。

今日のインターネット スケール サービスは、マイクロサービスを使用して構築されます。マイクロサービスの例としては、プロトコル ゲートウェイ、ユーザー プロファイル、ショッピング カート、インベントリ処理、キュー、キャッシュなどが挙げられます。Service Fabric は、すべてのマイクロサービスにステートレスまたはステートフルな一意の名前を指定するマイクロサービス プラットフォームです。

Service Fabric は、これらのマイクロサービスで構成されたアプリケーションに対して、包括的なランタイムおよびライフサイクル管理機能を提供します。Service Fabric クラスター間にわたってデプロイされアクティブ化されるマイクロサービスを、コンテナー内にホストします。密度の 1 桁の増加が、Vm からコンテナーへの移行によって実現されるのと同様に、コンテナーからマイクロサービスに移行するときに密度が同様の 1 桁の増加が実現します。たとえば、Service Fabric 上に構築された単一の Azure SQL Database クラスターは、合計数十万のデータベース (各データベースは Service Fabric ステートフル マイクロサービスです) をホストする数万のコンテナーを実行する数百のマシンから成ります。Event Hubs および上記で説明した他のサービスの場合も同様です。Service Fabric の機能を表すのにハイパースケールという語が使用されるのはこのためです。コンテナーによって高密度が得られれば、マイクロサービスによってハイパースケールを実現できます。

![Service Fabric プラットフォーム][Image1]

## ステートレスおよびステートフル Service Fabric マイクロサービス

ステートレス マイクロサービス (プロトコル ゲートウェイや Web プロキシなど) では、要求およびそのサービスからの応答の外部で、変更可能な状態は維持されません。ステートレス サービスの例には Azure Cloud Services worker ロールがあります。ステートフル マイクロサービス (ユーザー アカウント、データベース、デバイス、ショッピング カート、キューなど) では、要求およびその応答の外部で、変更可能な認証状態が維持されます。今日のインターネット規模のアプリケーションは、ステートレス マイクロサービスとステートフル マイクロサービスの組み合わせで構成されています。
 
ステートフル マイクロサービスが重要な理由は何でしょうか。 単純にステートレス サービスのみを使用しないのはなぜはでしょうか。 これには次の 2 つの理由があります。

1) 同じコンピューター上でコードとデータを密接に保持することで、対話型のストア フロント、検索、モノのインターネット (IoT) システム、取引システム、クレジット カード処理、不正検出システム、個人記録管理など、高スループット、低待機時間、エラー トレラントな OLTP サービスを構築できます。

2) 完全にステートレスなマイクロサービスの可用性と待機時間の要件に対応するために従来必要だった追加のキューおよびキャッシュが、ステートフル マイクロサービスでは不要になるため、アプリケーション設計が単純化されます。ステートフル サービスは高可用性と低待機時間を特徴とするため、アプリケーション全体では管理すべき変動要素が少なくなります。

Service Fabric を使用したアプリケーションのパターンと設計の詳細については、[アプリケーション シナリオ](../service-fabric-application-scenarios)に関するページを参照してください。

## アプリケーション ライフサイクル管理
Service Fabric は、開発からデプロイ、日常的な管理、保守、および最終的な使用停止に至るまで、クラウド アプリケーションの完全なアプリケーション ライフサイクル管理 (ALM) に対して高度なサポートを提供します。

Service Fabric ALM 機能を使用すると、アプリケーション管理者または IT オペレーターは単純なロータッチ ワークフローを使用して、アプリケーションをプロビジョニング、デプロイ、修正、および監視することができます。これらの組み込みワークフローにより、アプリケーションを継続的に使用可能に保つために IT オペレーターにかかる負担が大幅に軽減されます。

ほとんどのアプリケーションは、ステートレス マイクロサービスとステートフル マイクロサービスの組み合わせ、および一緒にデプロイされた他の EXE/ランタイムで構成されています。Service Fabric は、アプリケーションの厳密な型およびパッケージ化されたマイクロサービスを備えており、個別に管理およびアップグレードできる複数のアプリケーション インスタンスのデプロイが可能です。重要な点として、Service Fabric は*任意の*実行可能ファイルまたはランタイムをデプロイし、これらを信頼性の高いものにすることができます。たとえば、ASP.NET 5、node.js、スクリプト、またはアプリケーションを構成する任意の要素をデプロイすることができます。
  
アプリケーション ライフサイクル管理の詳細については、[アプリケーション ライフサイクル](../service-fabric-application-lifecycle)に関するページを参照してください。

## 主な機能
Service Fabric を使用すると、次の操作を実行できます。

- 自己復旧機能を備えた非常にスケーラブルなアプリケーションを開発します。

- 「マシン上のデータ センター」アプローチで開発を行います。ローカル開発環境は、Azure データ センターで実行されるのと同じコードです。
 
- マイクロサービス、実行可能ファイル、およびユーザーが選択したその他のアプリケーション フレームワーク (ASP.NET や nodejs など) から成るアプリケーションを開発します。

- ステートレスおよびステートフル (マイクロ) サービスを開発し、これらを非常に信頼性の高いものにします。

- キャッシュとキューの代わりにステートフル (マイクロ) サービスを使用して、アプリケーションの設計を簡略化します。
 
- アプリケーションを数秒間でデプロイします。

- コードを変更せずに、Azure にデプロイするか、または Windows Server を実行するオンプレミス クラウドにデプロイします。一度記述してから、任意の Service Fabric クラスターにデプロイします。

- マシンごとに数百または数千のアプリケーションをデプロイすることで、仮想マシンよりも高密度なアプリケーションのデプロイを実現します。

- それぞれ個別にアップグレード可能な、同じアプリケーションの異なるバージョンをサイドバイサイドでデプロイします。
 
- 重大なアップグレードと重大でないアップグレードを含め、ステートフル アプリケーションのライフサイクルをダウンタイムなく管理します。

- .NET Api、Powershell、または REST インターフェイスを使用してアプリケーションを管理します。
 
- アプリケーション内で個別にマイクロサービスをアップグレードおよび修正します。

- アプリケーションの状態を監視および診断し、自動修復を実行するようにポリシーを設定します。

- 使用可能なリソースに応じてアプリケーションがスケーリングされるため、Service Fabric クラスターを容易にスケール アップまたはスケール ダウンすることができます。

- 自己修復リソース バランサーが、障害から回復し、利用可能なリソースに基づいて負荷分散を最適化するために、Service Fabric クラスター全体でアプリケーションの再配布を統制するのを監視します。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

詳細については、[技術的概要](../service-fabric-technical-overview)に関するページを参照してください。

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=58_postMigration-->