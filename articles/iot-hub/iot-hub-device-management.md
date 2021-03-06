<properties
 pageTitle="IoT デバイス管理 | Microsoft Azure"
 description="IoT デバイスを管理するための IoT Hub と IoT Suite の使用の概要"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="sethm"/>

# Azure IoT Suite と Azure IoT Hub を使った IoT デバイス管理

Azure IoT Suite と Azure IoT Hub は、大規模な IoT ソリューションや、多様なデバイス、多様なデバイス トポロジを対象としたデバイス管理を実現する、基盤となる機能を備えています。この記事の中で触れている "デバイス管理" とは、厳密には "IoT デバイス管理" を指します。

## はじめに

サービス プロバイダーや大企業など、多数の IoT デバイスを運用している組織では、デバイス管理機能を使用して次のようなビジネス プロセスを遂行できます。

* IoT デバイスの登録と検出
* 接続性の確保
* デバイス上のソフトウェアのリモートからの構成と更新たとえば、製造工場や油田におけるデバイス管理には、リモートからデバイスの構成や更新を行う際に守るべきポリシーに加え、承認系統、法的監査要件、物理的な安全措置の存在などがあります。

IoT ソリューションに IoT デバイス管理を導入する際は、以下に列挙した機能を実際の事業目標と照らし合わせながら考慮し、それぞれの重要度を判断してください。

* **[デバイスのプロビジョニングと検出](#device-provisioning-and-discovery)**: デバイスをシステムに登録するプロセス。
* **[デバイス レジストリとデバイス モデル](#device-registry-and-device-models)**: デバイス モデルがデバイスの関係、システムにおける役割、検証手法へのメタデータの体系的活用法を表現する方法。
* **[デバイス アクセス管理](#device-access-management)**: デバイスのリソースに対するクラウド サービスからのアクセスを制御する方法。
* **[リモート制御](#remote-control)**: ユーザーが遠隔からデバイスにアクセスし、変更命令を与えるための方法。
* **[リモート管理](#remote-administration-and-monitoring)**: デバイス群の正常性を管理者が定義するプロセス。  
* **[リモート構成](#remote-configuration)**: デバイスの構成を管理者が変更する方法。
* **[ファームウェアとソフトウェアのリモート更新](#remote-firmware-and-software-update)**: デバイスのファームウェアとソフトウェアを管理者が更新するプロセス。

以降の各セクションでは、上に挙げた個々のデバイス管理機能をさらに踏み込んで説明すると共に、Azure IoT Hub でそれらの機能を導入するためのモデルを簡単に取り上げます。

## デバイスのプロビジョニングと検出

Azure IoT Hub を使用したデバイスのプロビジョニングは、レジストリ API を使って行います。対象デバイスを登録してキーを渡すか受け取ったら、そのキーを使ってデバイスを有効にして IoT Hub に接続することができます。Azure IoT Hub と通信できるのは、承認された資格情報を提示する登録済みのデバイスだけです。Azure IoT Hub に対するデバイスのアクセスは、管理者がデバイス管理ポータルから無効にできます。

IoT デバイスの製造、プロビジョニング、デプロイの方法によっては、ブートストラップ プロセスを使用することができます。貴社のソリューションの一環としてブートストラップ サービスを構築することで、シンプルな接続手段を確立したり、特定の IoT Hub にデバイスを割り当てる処理に時間差を設けたりすることができます。デバイスが実際にどこで運用されるかは通常、製造時点ではわかりません。しかし、ブートストラップ プロセスを使うと、たとえば、複雑になりがちな多数のワークフローで、特定のデバイスを Azure IoT Hub に認識させ、さらに既存のビジネス プロセスにそのデバイスを統合することができます。

ブートストラップ サービスが使用されている環境では、IoT デバイスが起動して要求を発行することにより、割り当て済みの Azure IoT Hub へのアクセスが実現します。デバイスのブートストラップ資格情報など必要なデータはすべて、要求に含めて送信する必要があります。デバイスが承認された場合、ブートストラップ サービスは、割り当て済みの Azure IoT Hub にそのデバイスを登録し、ブートストラップの要求元のデバイスに接続情報を返します。IoT Hub は、ブートストラップの要求元のデバイスに接続情報を返します。デバイスが承認された場合、ブートストラップ サービスは、割り当て済みの Azure IoT Hub にそのデバイスを登録し、ブートストラップの要求元のデバイスに接続情報を返します。

## デバイス レジストリとデバイス モデル

"*デバイス モデル*" とは、クラウド サービスによる読み取りまたは書き込みが可能なデバイス プロパティを含むモデルを指します。クラウド サービスからリモートで実行できるデバイス コマンドも含みます。次のセクションで説明するサービス駆動型モデルでは、そのモデルに関する知識をデバイス側は持ちませんが、クラウド サービスはそれでも、デバイスに関するメタデータを追跡し、利用することができます。

デバイスの情報とそれに関連するメタデータを保存することは、IoT システムにおいて、また、デバイス レジストリの役割においてきわめて重要な機能となります。これは特に、変更できないレガシ デバイスやデバイス モデルを使用しないデバイスに当てはまります。サービス駆動型モデルにより、IoT サービスはデバイス群と対話できます。定義されたモデルを使用してデバイスが動作するとき、デバイス レジストリは、整合性の原則に従ってデバイスのデータを反映します (デバイスからマスターにデータが供給される)。この場合サービスが、必要な変更をデバイスに伝え、デバイスが変更を確認した後でのみそれらが有効となります。

### サービス駆動型モデル

Azure IoT Hub に接続するデバイスがデバイス モデルを備えていない場合、登録デバイスとそれに関連したメタデータを追跡するデバイス レジストリを実装することが重要となります。この場合、デバイス レジストリがデバイス メタデータの唯一の保存場所となります。追跡対象となるデバイス メタデータの例として、論理的なトポロジ (フロア 4、ビルディング 109 など) やデバイスの機能 (入室センサーなど)、各種タグ情報を挙げることができます。

### デバイス駆動型モデル

IoT ソリューションで IoT デバイスの機能をフルに活用するためには、デバイスが、IoT Hub への接続後、クラウドに対して自分自身の情報を伝える必要があります。IoT ソリューションで利用できる 2 種類のデバイス モデルを以下で説明します。

#### 自己定義型デバイス モデル

デバイス エンジニア (またはデバイス シミュレーターを使用する開発者) は、デバイスを開発する過程で、試行錯誤を繰り返しながらその機能の実装に取り組む際に自己定義型のデバイス モデルを使用します。デバイス エンジニアは、最初はわずかなプロパティと対応コマンドのみを備えたデバイスを作成し、後からそれらを追加していくことができます。同様に、それぞれ一意の機能を備えた多数のデバイスに、自己定義型モデルを使用することもできます。この場合、デバイス エンジニアがデバイス モデルの構造を登録する必要はありません。自己定義型モデルでは、デバイス間の差異が著しく大きくなると、多数のデバイスへのスケーリングで無理が生じる場合があります。

#### 事前定義型デバイス モデル

ネットワークや電力、処理能力の限られた状況下で本番運用される IoT 環境の場合は、デバイスの処理能力と消費電力を最小限に抑えた事前定義型のデバイス モデルが大きな利便性を発揮します。同様に、ネットワーク トラフィックがごく小さく抑えられていれば、デバイスからのデータ伝送に、異機種ネットワーク (WiFi、2G/3G/4G、BLE、衛星など) を使うことができます。衛星通信のように、制限が厳しくコストのかかるインフラストラクチャが使われているときは、なおさらです。事前定義型のデバイス モデルを実装するデバイス エンジニアは、キーとしての役割を果たす 1 ～ 2 バイトのデータにデバイス情報をエンコードし、事前定義型のデバイス モデルに適用します。このシンプルなアプローチによって、自己定義型のデバイス モデルと比べ、効率が 1 ～ 2 桁アップします。

### リモート監視の事前構成済みソリューションとそのデバイス モデル

Azure IoT Suite Remote Monitoring の事前構成済みソリューションには、自己定義型のデバイス モデルが実装されています。デバイスの機能を定義し、進化させる過程で生じる反復作業を、このモデルによって短時間化することができます。

リモート監視の事前構成済みソリューションで実際にデバイス オブジェクトを作成し、それをサービスに送信するコードは、**Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs** で定義されています。自己記述式、つまり自分自身の情報を伝えるデバイス モデルを作成し、Azure IoT Hub に送信する手法が、`SendDeviceInfo` メソッドと `GetDeviceInfo` メソッドに使われています。

デバイス モデルに関連したイベントをクラウド サービスで処理するコードは、**EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs** にあります。事前構成済みソリューションのサービス側に送られたデバイス モデル関連メッセージに対して行われるアクションに含まれる処理の大部分は、`ProcessJToken` メソッドで行われます。

デバイス モデル関連メッセージを受信した後に、デバイス レジストリの更新をつかさどるのは、**DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** にある `UpdateDeviceFromSimulatedDeviceInfoPacketAsync` メソッドと `UpdateDeviceFromRealDeviceInfoPacketAsync` メソッドです。リモート監視の事前構成済みソリューションにおけるデバイス レジストリ API は、**DeviceManagement/Web/WebApiControllers/DeviceApiController.cs** で確認できます。

### フィールド ゲートウェイ デバイス モデル

フィールド ゲートウェイは、インターネットに直接接続できないデバイスや、直接接続することが望ましくないデバイスの接続とプロトコル変換によく使用されます。開発しているデバイスがフィールド ゲートウェイである場合、そのデバイスは、Azure IoT Hub とのすべてのやり取りにおいて、フィールド ゲートウェイを通じて接続されるデバイスを表します。デバイスのプロトコルと IoT サービスでサポートされるプロトコル間の変換は、フィールド ゲートウェイの製造元である貴社が実装することになります。フィールド ゲートウェイで BLE (Bluetooth Low Energy) デバイスの接続に対応するには、デバイスへの BLE インターフェイスと Azure IoT Hub へのインターフェイスを実装する必要があります。

## デバイス アクセス管理

デバイス プロパティに対する読み取り/書き込みアクセス権やデバイス コマンドの実行権限など、デバイスのさまざまな側面に対するアクセスは、IoT Suite の事前構成済みソリューションによって制御されます。事前構成済みソリューションでは、このアクセスが、デバイス管理ポータルから Azure Active Directory (AAD) を使用して制御されます。事前構成済みソリューションで AAD を応用し、ロールベースのアクセス セキュリティを有効にすることができます。

## リモート制御

Azure IoT Suite の事前構成済みソリューションのシナリオとして、リモート制御を詳しく取り上げることはしません。IoT ソリューションでリモート制御を取り入れるうえでの選択肢についてのみ簡単に取り上げます。

IT のシナリオでは、リモート ユーザーを支援したり、リモート サーバーの構成を遠隔から行ったりする目的でリモート制御が広く使われています。IoT のシナリオでは、デバイスが、特定のユーザーの使用下に置かれることはまずありません。必然的に遠隔からの構成や診断が、リモート制御の用途となります。リモート制御の実装方法には、次の 2 種類のモデルがあります。

* **直接接続** デバイスとの直接接続 (Linux での SSH、Windows でのリモート デスクトップ、リモート デバッグ ツールなど) によるリモート制御を行うには、対象デバイスとの接続を確立できることが必要です。オープンなインターネットにデバイスをさらすことにはセキュリティ面のリスクが伴うため、デバイスとの接続や、デバイス間で送受信されるトラフィックには、リレー サービス (Azure [Service Bus Relay サービス](service-bus-relay-overview.md)など) を使用することをお勧めします。リレー接続はデバイスからの送信接続であるため、デバイス上の開放 TCP ポートの攻撃対象領域を制限することができます。

* **デバイス コマンド** デバイスと Azure IoT Hub 間に確立された既存の接続と通信チャネルは、デバイス コマンドを介したリモート制御を行うことで有効活用できます。デバイス コマンド ベースのリモート制御を行うには、次の要件に従う必要があります。
  * デバイス上で動作するソフトウェアは、そのデバイスがデバイス コマンドに対応していることを IoT サービスに伝達する必要があります。これは通常、デバイス モデルの構成要素として定義されます。
  * デバイス上で動作するソフトウェアは、リモート制御コマンドを実装する必要があります。これらのデバイス コマンドは、(IoT サービスからデバイスへの) 要求と (デバイスから IoT サービスへの) 応答のパターンに従う必要があります。デバイス コマンドを実行することによって、デバイスの状態に変化を与え、その新しい状態をデバイス レジストリに反映する必要があります。

デバイスがマスター ストアとしてデバイスの構成や状態を保存するとき、デバイス レジストリは結果整合性モデルに従います。デバイスの状態に対する変更はデバイス レジストリにプッシュする必要があります。デバイス レジストリの状態は、IoT サービスからデバイスへの要求によって強制的に更新できるほか、デバイス側がシステム状態の変化を認識したときに自動的にサービスを更新することもできます。デバイス側からサービスを自動更新すると、大量のネットワーク トラフィックが生成され、デバイスのプロセッサ使用率と消費電力が上昇する可能性があります。

## リモート管理とリモート監視

ほとんどの IoT デバイスでは、運用ユーザー ロールと管理用ユーザー ロールが区別されています。リモート管理とは、管理者がそのデバイスの状態を監視し、デバイスからビジネス プロセスやアプリケーション (CRM、ERP、メンテナンス システムなど) へのデータの流れを構成したり、デバイスの状態や構成をデバイス コマンドを使ってリモートから更新したりする方法です。

Azure IoT Suite の事前構成済みソリューションには、デバイス管理作業の出発点となる Azure Web サイトが用意されており、バーティカルな IoT ソリューションのシナリオに応じてサイトを拡張することができます。

デバイスが正常であるかどうかの境界は、デバイスの運用データ (通常は高速に移動) とメタデータ (通常は低速に移動) に応じて管理者が定義します。ポーリング方式とは対照的に、ストリーム処理エンジン ([Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) など) を使って実装されるルールを使用して、正常性警告システムを実現することができます。

## リモート構成

デバイスの構成を遠隔から変更することは、プロビジョニング、診断、ビジネス プロセスとの統合など、デバイスのライフ サイクルのさまざまな局面で重要です。遠隔からの構成変更は、デバイス モデルと IoT サービスの機能とを組み合わせ、デバイス モデルのインスタンスの状態をリモートから更新することで可能となります。

リモート監視の事前構成済みソリューションでは、以下のコマンドを使ったリモート構成に対応しているという情報がデバイス側から伝えられます。

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

事前構成済みソリューションのデバイス管理ポータルに、利用可能なデバイス コマンドとして表示されることはありません。これらは、ポータルの特定の領域によってリモート実行されるデバイス コマンドです。デバイス コマンドを受信すると、デバイスは、受信確認応答をサービスに返します。デバイス コマンドを処理した後、デバイスは、コマンドの実行に成功したかどうかの情報をサービスに結果応答として返します (コマンドの実行に失敗した場合は、エラー コードも一緒に送信されます)。デバイスに対して要求された状態はこの時点で確定し、デバイス レジストリが更新されます。

## ファームウェアとソフトウェアのリモート更新

ファームウェアとソフトウェアのリモート更新は、IoT システムの重要な機能です。更新により、デバイスは最新かつ最も安全なソフトウェアを実行できるようになります。デバイス上のファームウェアとソフトウェアを遠隔から更新する作業は、通常ビジネス プロセスを伴って長時間実行される分散処理の一種です。たとえば、高性能燃料ポンプを制御するデバイスのファームウェアを更新するためには、更新処理の実行と検証が行われている間、燃料の供給経路を変更する措置が隣接するシステムに必要となります。

### ファームウェア更新の分析

以下の例は、ビジネス ニーズに合ったファームウェア更新機能を実装する場合に考えられる 1 つの方法です。この例の目的は、更新処理の考慮事項を大まかに説明することであって、特定の実装を強要することではありません。更新要件を設計する際は、業務上や技術上のさまざまな要因を考慮する必要があり、それらはこの記事で取り上げる範囲を超えています。

デバイスの更新処理は IoT サービス側から開始され、デバイスへの通知は、デバイス コマンドによって決まった時刻に行われます。ファームウェアやソフトウェアのリモート更新をデバイスが明示的にサポートしているときは、決められたビジネス プロセスとポリシーに従って、IoT サービスが更新コマンドを配信する必要があります。更新のためのデバイス コマンドを受け取ったら、デバイスはまず、更新パッケージをダウンロードしてデプロイする必要があります。次に、新しくデプロイされた構成 (ファームウェア更新の場合) をリブートするか、新しいソフトウェア パッケージを起動して、その新しいファームウェアまたはソフトウェアが適切に実行されていることを検証します。この複数のステップから成るプロセスの過程で随時、デバイスはその状態を IoT サービスに通知する必要があります。

ストレージ サービス (Azure Storage など) やコンテンツ配信ネットワーク (CDN) は、更新パッケージを配信できます。ファームウェアまたはソフトウェアの更新プログラムを適用する際は、ダウンロードしたパッケージの整合性を事前に検証し、パッケージが適切な配信元から提供されたことを確認することが大切です。

ファームウェア更新の完了後、デバイスは正常な状態を検証によって把握できることが必要となります。デバイスが正常な状態に移行しなかった場合、デバイス上のソフトウェアは既知の正常な状態にロールバックする処理を開始する必要があります。既知の正常な状態とは、たとえば、最後に確認されている正常な状態や、ストレージ パーティションに "*ひな形*" として保存されているデバイスのファームウェア イメージです。

## 次のステップ

Azure IoT Hub の詳細については、次のリンクを参照してください。

* [IoT Hub の概要][]
* [Azure IoT Hub とは][]
* [デバイスを接続する][]

[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT Hub とは]: iot-hub-what-is-iot-hub.md
[デバイスを接続する]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->