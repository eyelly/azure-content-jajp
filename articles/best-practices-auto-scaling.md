<properties
   pageTitle="自動スケール ガイダンス | Microsoft Azure"
   description="自動スケールによってアプリケーションに必要なリソースを動的に割り当てる方法に関するガイダンス。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# 自動スケール ガイダンス

![](media/best-practices-auto-scaling/pnp-logo.png)

## 概要
自動スケールとは、パフォーマンス要件を満たし、サービス レベル アグリーメント (SLA) に準拠しつつ、ランタイム コストを最小限に抑えられるように、アプリケーションに必要なリソースを動的に割り当てるプロセスです。作業の量が多くなると、アプリケーションで効率よく作業を実行できるようにするために、追加リソースが必要になる場合があります。需要が低くなれば、リソースの割り当てを解除してコストを最小限に抑えながら、十分なパフォーマンスを維持し、SLA に準拠できます。自動スケールは、クラウドでホストされている環境の弾力性を活用しながら、オペレーターがシステムのパフォーマンスを継続的に監視し、リソースの追加または削除に関して判断を行う必要性を軽減することで、管理オーバーヘッドを削減します。
> 自動スケールは、コンピューティング リソースだけでなく、アプリケーションによって使用されるすべてのリソースに適用されます。たとえば、システムでメッセージ キューを使用して情報の送受信が行われている場合、そのスケール状況に合わせて追加のキューを作成できます。

## スケールのタイプ
通常、スケーリングは垂直方向または水平方向という 2 種類の形式のいずれかで行われます。

- **垂直方向のスケーリング** (スケールアップとスケールダウンとも呼ばれる) では、ハードウェアを変更したり (その容量やパフォーマンスを拡大または縮小する)、適切な容量やパフォーマンスが備わった代替ハードウェアを使用するソリューションを再デプロイしたりする必要があります。通常、クラウド環境ではハードウェア プラットフォームは仮想化環境です。結果として生じる事前の金銭的支出を加味して元のハードウェアが余裕を持って準備されていなければ、この環境で垂直方向のスケールアップを行うと、より強力なリソースをプロビジョニングしたり、新しいリソースにシステムを移動したりすることになります。垂直方向のスケーリングは、システムの再デプロイ中にシステムを一時的に使用不可にしなくてはならないため、中断を伴うプロセスになることがよくあります。新しいハードウェアがプロビジョニングされ、オンラインに設定される間、元のハードウェアを稼働し続けることは可能な場合もありますが、古い環境から新しい環境に移行している間に何らかの中断が生じる可能性はあります。自動スケールを使用して、垂直方向のスケーリング戦略を実装することはあまりありません。
- **水平方向のスケーリング** (スケールアウトおよびスケールインとも呼ばれる) では、リソースを追加または削除して、それに基づいたソリューションを再デプロイする必要があります。多くの場合、リソースは高性能システムではなく、コモディティ リソースです。これらのリソースをプロビジョニングしている間に、このソリューションは中断することなく継続的に実行できます。プロビジョニング プロセスが完了したら、ソリューションを構成している要素のコピーを追加リソースにデプロイして、使用できるようにすることができます。需要が下がれば、追加したリソースを使用している要素を正常にシャットダウンしてから、それらのリソースを再利用できます。Microsoft Azure を含む、多くのクラウド ベース システムでは、この形式の自動スケールがサポートされています。

## 自動スケール戦略の実装
通常、自動スケール戦略を実装するには、次のコンポーネントやプロセスが必要になります。

- アプリケーション、サービス、およびインフラストラクチャのレベルで、応答時間、キューの長さ、CPU 使用率、およびメモリ使用量などの主要メトリックをキャプチャするインストルメンテーションおよび監視システム。
- 監視対象のスケーリング因子を、事前設定されたシステムしきい値やスケジュールに照らして評価し、スケールを実行するかどうかを決定できる意思決定ロジック。
- リソースのプロビジョニングまたはプロビジョニング解除など、システムのスケールに関連するタスクを実行する役割を担うコンポーネント。
- 自動スケール戦略を期待どおりに機能させるための、戦略のテスト、監視、および調整。

Microsoft Azure など、大部分のクラウド ベース環境では、一般的な状況に対処するための組み込みの自動スケール メカニズムが用意されています。使用する環境またはサービスで必要な自動スケール機能が提供されていない場合、または提供されている機能を上回る、非常に厳密な自動スケール要件に対処する必要がある場合は、カスタム実装を行ってオペレーションおよびシステム メトリックを収集し、それらを分析して関連データを特定し、それに応じてリソースをスケールする必要が生じる可能性があります。

## 自動スケール実装に関する考慮事項
自動スケールは単純なソリューションではありません。単にリソースをシステムに追加したり、実行するプロセスのインスタンスを増やしたりするだけで、システムのパフォーマンスを確実に向上させることができるわけではありません。自動スケール戦略を作成するときには、次の点を考慮してください。

- システムは、水平方向にスケールできるように設計されている必要があります。インスタンスのアフィニティに関して仮説を立てることは避けます。あるコードが常にプロセスの特定のインスタンスで実行されている必要があるようなソリューションを作成しないでください。クラウド サービスまたは Web サイトを水平方向にスケーリングする場合、同じソースからの一連の要求が常に同じインスタンスにルーティングされるようには想定しないでください。同様の理由で、アプリケーションからの一連の要求が常にサービスの同じインスタンスにルーティングされなくても済むように、サービスはステートレスになるように設計します。キューからメッセージを読み取り、それらを処理するサービスを設計する場合、サービスのどのインスタンスが特定のメッセージを処理するかについて、仮説を立てないでください。キューの長さが長くなると、自動スケールによってサービスの追加インスタンスが開始される可能性があるからです。「[Competing Consumers pattern (競合コンシューマー パターン)](http://msdn.microsoft.com/library/dn568101.aspx)」には、この状況に対処する方法が記載されています。
- ソリューションで長期タスクを実装する場合、このタスクを作成して、スケールアウトとスケールインの両方がサポートされるようにします。しかるべき注意を払わなければ、そのようなタスクによってシステムをスケールインするときにプロセスのインスタンスが正常にシャットダウンされなかったり、プロセスが強制的に終了された場合にデータが失われたりする可能性があります。可能であれば、長期タスクをリファクタリングし、プロセスを分割して、より小さい、個別のまとまりで実行されるようにします。「[Pipes and Filters pattern (パイプとフィルター パターン)](http://msdn.microsoft.com/library/dn568100.aspx)」には、これを行う方法の例が記載されています。あるいは、一定の間隔でタスクの状態に関する情報を記録するチェックポイント メカニズムを使用し、タスクを実行しているプロセスのすべてのインスタンスがアクセスできる永続的ストレージにその状態を保存することもできます。このようにすることで、プロセスがシャットダウンされても、別のインスタンスを使用して、そのプロセスによって実行されていた作業を最後のチェックポイントから再開できます。
- Cloud Services でホストされているアプリケーションの worker ロールなど、別個のコンピューティング インスタンスでバックグラウンド タスクが実行されている場合、別のスケーリング ポリシーを使用してアプリケーションのさまざまな部分をスケールする必要が生じることがあります。たとえば、バックグラウンド コンピューティング インスタンスの数を増やさずに、UI コンピューティング インスタンスをさらにデプロイしたり、その反対の処理を行ったりしなくてはならない場合があります。異なるレベルのサービス (Basic、および Premium サービス パッケージなど) を提供する場合、SLA に準拠するために、Basic サービス パッケージのコンピューティング リソースよりも、Premium サービス パッケージのコンピューティング リソースの方をより積極的にスケールアウトする必要が生じる可能性があります。
- 自動スケール戦略を促進する要因として、UI とバックグラウンド コンピューティング インスタンスが通信に使用するキューの長さを使用することを検討してください。これは、バックグラウンド タスクの現在の負荷と処理能力の間の不均衡または差異を最も明確に示す指標です。
- 1 時間当たりに行われた命令数や複雑なトランザクションの平均実行時間など、ビジネス プロセスを測定するカウンターに基づいて自動スケール戦略を作成する場合は、そのようなタイプのカウンターから得られる結果と実際のコンピューティング能力の要件の関係を完全に理解してください。ビジネス プロセス カウンターの変更に応じて、複数のコンポーネントまたはコンピューティング ユニットをスケールする必要が生じる場合があります。  
- システムによって過度なスケールアウトが試行されたり、多数のインスタンス実行に関連するコストが生じたりしないようにするために、自動的に追加できるインスタンスの最大数を制限することを検討してください。大部分の自動スケール メカニズムでは、任意のルールに基づいてインスタンスの最小数と最大数を指定できます。また、インスタンスの最大数を設定しても、システムが依然としてオーバーロードの状態である場合には、システムによって提供される機能を適度に低下させることを検討してください。
- 自動スケールは、ワークロードの急激な増加を処理する最も適切なメカニズムではない場合もあることに留意してください。サービスの新しいインスタンスをプロビジョニングして開始したり、リソースをシステムに追加したりする作業は時間を要します。また、追加したそれらのリソースが使用できるようになるまでに、ピーク時が過ぎてしまう可能性もあります。このようなときは、サービスを調整したほうがよい場合があります。詳細については、「[Throttling pattern (調整パターン)](http://msdn.microsoft.com/library/dn589798.aspx)」を参照してください。
- 逆に、ボリュームが急激に変動する場合にすべての要求を処理する容量が必要であり、コストが主な検討要因ではない場合は、追加インスタンスをより迅速に開始する積極的な自動スケール戦略を使用したり、負荷が予見される前に、十分な数のインスタンスを開始して最大負荷に対応するようにスケジュール設定されたポリシーを使用したりすることを検討してください。
- 自動スケール メカニズムでは、自動スケール プロセスを監視し、各自動スケール イベントの詳細 (イベントがトリガーされた理由、追加または削除されたリソース、およびそれらの日時) を記録する必要があります。カスタムの自動スケール メカニズムを作成する場合は、この機能が組み込まれていることを確認してください。情報を分析して、自動スケール戦略の効果を測定し、必要に応じて、使用パターンがより明確になる場合は短期間、ビジネスが拡大したり、アプリケーションの要件が増加したりする場合は長期間という両方の期間設定でその戦略を調整するのに役立てることができます。アプリケーションが自動スケールに対して定義された上限に到達すると、このメカニズムによって、状況的に正当な理由がある場合には追加リソースを手動で開始できる操作者にアラートが出される可能性があります。このような状況では、ワークロードが軽減された後、操作者はそれらのリソースを手動で削除しなくてはならなくなる場合もあることに留意してください。

## Azure ソリューションでの自動スケール
Azure ソリューションでは自動スケールを構成するためのオプションがいくつかあります。

- **Azure 自動スケール**。この機能は、スケジュールに基づいた最も一般的なスケーリング シナリオをサポートします。またオプションで、ランタイム メトリクス (プロセッサ使用率、キューの長さ、または組み込みのカスタム カウンターなど) に基づいてトリガーされたスケーリング操作をサポートします。Microsoft Azure 管理ポータルを使用して、ソリューションの簡単な自動スケール ポリシーをすばやく簡単に構成できます。また、Azure Monitoring Services 管理ライブラリを使用してより詳細な制御レベルで自動スケール ルールを構成できます。詳細については、「[Azure Monitoring Services 管理ライブラリ](#the-azure-monitoring-services-management-library)」のセクションを参照してください。
- Asure の診断、監視、およびサービス管理機能に基づく**カスタム ソリューション**。たとえば、Azure の診断、カスタム コード、または [System Center Management Pack for Azure](http://www.microsoft.com/download/details.aspx?id=38414) を使用して、アプリケーションのパフォーマンスを継続的に監視できます。また、[Azure サービス管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)、[Microsoft Azure Management Libraries](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Libraries)、または [オートスケーリング アプリケーション ブロック](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx)を使用して、スケールアウトおよびスケールインを行うこともできます。スケーリング操作をトリガーするためのメトリックには、組み込みまたはカスタムのカウンター、あるいはアプリケーション内に実装するその他のツールを使用できます。ただし、カスタム ソリューションの実装は容易ではないため、前述の手法では要件を完全に満たすことができない場合にのみ検討してください。オートスケーリング アプリケーション ブロックは、オープン ソースのフレームワークであり、Microsoft が直接サポートしているものではありません。
- [Paraleap AzureWatch](http://www.paraleap.com/AzureWatch) などの**サード パーティ サービス**を使用して、スケジュール、サービス負荷とシステム パフォーマンス インジケーター、カスタム ルール、およびさまざまなタイプのルールの組み合わせに基づいてソリューションをスケールできます。

採用する自動スケール ソリューションを選択する場合は、以下の点を考慮してください。

- プラットフォームに組み込まれた自動スケール機能で要件を満たせる場合には、それを使用します。満たせない場合は、より複雑なスケーリング機能が本当に必要かどうかを慎重に検討してください。組み込まれた自動スケール機能では満たせない要件の例としては、より詳細な制御、スケーリングのトリガー イベントを検出するさまざまな方法、サブスクリプション全体にわたるスケーリング、他のタイプのリソースのスケーリングなどを行わなければならない場合などあります。
- スケジュールされた自動スケールしか使用しなくても (インスタンスを追加および削除して、予測される需要のピーク時に対応し) 十分な精度でアプリケーションの負荷を予測できるかどうかを検討します。これが不可能な場合、実行時に収集されたメトリックに基づいたリアクティブ自動スケールを使用して、予測不可能な需要の変化にアプリケーションが対応できるようにします。ただし、多くの場合、これらの手法を組み合わせることで適切に対処できます。たとえば、アプリケーションが最もビジー状態になることが分かっている時間のスケジュールに基づいて、コンピューティング、ストレージ、およびキューなどのリソースを追加する戦略を作成します。これは、新しいインスタンスの開始時に遅延を生じることなく、必要なときに容量を確実に使用できるようにするのに役立ちます。また、スケジュールされたルールごとに、その期間内にリアクティブ自動スケールを有効にし、持続的だが予測不可能な需要のピークをアプリケーションが処理できるようにするメトリックを定義します。
- 特にアプリケーションが初めてデプロイされたときは、メトリックと容量要件の関係を理解するのが困難なことがよくあります。最初は容量を少量追加し、その後自動スケール ルールを監視および調整して、実際の負荷に容量を近づけることをお勧めします。

### Azure 自動スケールの使用
Azure 自動スケールを使用して、ソリューションのスケールアウト オプションやスケールイン オプションを構成できます。Azure 自動スケールでは、Azure Cloud Services の Web および worker ロール、Azure Mobile Services、および Azure Web Sites アプリケーションのインスタンスが自動的に追加および削除されます。また、Azure Virtual Machines のインスタンスを開始および停止することで、自動スケールを有効にすることもできます。Azure 自動スケール戦略は、次の 2 つの要素のセットで構成されています。

- スケジュール ベースの自動スケール。これにより、予想される使用量ピークに合わせて追加されたインスタンスを使用できるようになり、ピーク時間が過ぎるとスケールインできます。この機能によって、システムが負荷に対応するのを待たずに、十分なインスタンスを事前に実行させておくことができます。
- メトリック ベースの自動スケール。これは、過去 1 時間の平均 CPU 使用率、または Azure Storage や Service Bus のキューでソリューションが処理しているメッセージのバックログなどの要因に反応します。この機能によって、アプリケーションはスケジュールされた自動スケール ルールとは別個に反応し、計画されていない、または予期しない需要の変化に対処できます。

Azure 自動スケールを使用するときは、次の点を考慮してください。

- 自動スケール戦略では、スケジュールされたスケーリングとメトリックベース スケーリングの両方が組み合わされます。サービスに対して両方のタイプのルールを指定し、アプリケーションがスケジュールと、負荷の変化に対する反応という 2 つの側面でスケールするように設定できます。
- Azure 自動スケール ルールを構成し、長期間、アプリケーションのパフォーマンスを監視する必要があります。この監視結果を使用して、必要に応じてシステムをスケールする方法を調整します。ただし、自動スケールは即時に結果を得られるプロセスではありません。指定したしきい値を超過 (または下回る) 平均 CPU 使用率など、何らかのメトリックに反応するには一定の時間を要します。
- 測定されたトリガー属性 (CPU 使用率やキューの長さなど) に基づく検出メカニズムを使用する自動スケール ルールでは、瞬間的な値ではなく、長期にわたって集計された値を使用して自動スケール アクションがトリガーされます。既定では、集計は値の平均になります。これにより、システムの反応が早すぎたり、急激な変動が生じたりすることが回避されます。また、自動的に開始された新しいインスタンスが実行モードになるまでの時間を確保し、新しいインスタンスの開始中に追加の自動スケール アクションが実行されないようにすることもできます。Cloud Services や Virtual Machines については、集計の規定期間は 45 分間であるため、需要の急増に反応して自動スケールをトリガーするメトリックに、この長さまでの時間を設定できます。SDK を使用してこの集計期間を変更できますが、25 分未満に設定すると予期しない結果を招く可能性があります (詳細については、「[Auto Scaling Cloud Services on CPU Percentage with the Azure Monitoring Services Management Library (Azure Monitoring Services 管理ライブラリを使用した CPU 使用率に基づく Cloud Services の自動スケール)](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/)」を参照)。Azure Web サイトでは、平均の期間はかなり短く、平均のトリガー測定への変更後、約 5 分間で新しいインスタンスを使用できるようになります。
- この Web ポータルではなく、SDK を使用して自動スケールを構成する場合は、ルールが有効になる条件を定めた、より詳細なスケジュールを指定できます。独自のメトリックを作成し、自動スケール ルールで既存のメトリックと共に使用したり、単独で使用したりすることもできます。たとえば、1 秒間当たりの要求数や使用可能なメモリ平均量などの代替カウンターを使用することも、特定のビジネス プロセスを測定するカスタム カウンターを使用することもできます。詳細については、「[Azure Monitoring Services 管理ライブラリ](#the-azure-monitoring-services-management-library)」を参照してください。
- Azure Virtual Machines を自動スケールするときは、自動スケールの開始を許可する最大数と同じ数の仮想マシンのインスタンスを多数デプロイする必要があります。これらのインスタンスは、同じ可用性セットの一部にする必要があります。Virtual Machines の自動スケール メカニズムでは、仮想マシンのインスタンスの作成または削除は行われません。代わりに、構成する自動スケール ルールによって、適切な数のインスランスが開始および停止されます。詳細については、「[Web ロール、ワーカー ロール、または仮想マシンを実行しているアプリケーションの規模の自動設定](cloud-services-how-to-scale.md#autoscale)」を参照してください。
- サブスクリプションの最大値に達したか (Virtual Machines サービスを使用している間にコアの最大数に到達したなど)、起動中にエラーが発生したため新しいインスタンスを起動できない場合、ポータルによって自動スケール操作が成功したと示されることがあります。ただし、ポータルに後で表示される **ChangeDeploymentConfiguration** イベントには、サービスの開始が要求されたことのみが表示され、それが正常に完了されたことを示すイベントは表示されません。
- Azure の自動スケールでは、Web ポータルの UI を使用して、SQL Database インスタンスやキューなどのリソースをコンピューティング サービス インスタンスにリンクできます。これにより、リンクされたリソースごとに、より簡単に別個の手動および自動スケーリング構成オプションにアクセスできます。詳細については、「クラウド サービスの管理方法」ページの「[方法: クラウド サービスに対するリソースのリンク](cloud-services-how-to-manage.md#linkresources)」と、「[アプリケーションの規模の設定方法](cloud-services-how-to-scale.md)」ページを参照してください。
- 複数のポリシーとルールを構成するときは、それらが互いに競合する可能性があります。Azure 自動スケールでは次の競合ソリューション ルールに従って、常に十分な数のインスタンスが実行されます。
  - スケールアウト操作は、スケールイン操作よりも常に優先されます。
  - スケールアウト操作が競合した場合は、インスタンス数を最も増加させるルールが優先されます。
  - スケールイン操作が競合した場合は、インスタンス数を最も減少させるルールが優先されます。

<a name="the-azure-monitoring-services-management-library"></a>

### Azure Monitoring Services 管理ライブラリ
Service Management API を使用して、より詳細な制御レベルで Azure 自動スケールを構成し、Web ポータル経由では使用できない機能にアクセスできます。この API には REST Web API として直接アクセスすることも、Azure Monitoring Services 管理ライブラリを介してアクセスすることもできます。

Azure 自動スケールは、Cloud Services ロール、Virtual Machines 可用性セット、Azure Web サイト (Web スペースのサーバー ファームとして)、または Azure Mobile Services の自動スケール プロファイルを指定することで構成されます。最大 20 個のターゲットを含めることができる各プロファイルでは、次を示します。

- 適用されるタイミング (繰り返しまたは固定の日数間隔を使用)
- 許可されるインスタンス数 (最小、最大、および既定の数)
- 有効になっている自動スケール ルール

Web ポータルでは、CPU 使用率またはキューの長さに基づく単一のスケール ルール ペアを使用して、基本的に昼と夜、および平日と週末のプロファイルを識別することで固定されたプロファイル セットを構成できるようにします。代わりに Service Management API を使用して、プロファイルに対してより詳細に適用可能な日付を設定し、Azure Monitoring Service に使用可能なすべてのメトリックに基づいたトリガーを使用するルールを最大 10 個指定できます。

自動スケール ルールは、ルールが適用されるタイミングを示すトリガーと、ターゲットの構成で実行される変更を示すスケール アクションで構成されています。本書を作成している時点でサポートされているアクションは、インスタンス数の増減のみです。

自動スケール ルールのトリガーは、使用可能なメトリックに基づいています。構成されているメトリックの値が、自動スケールの構成で定義されている適切なソースから定期的にサンプリングされます。アクティブなプロファイルの各ルールが評価され、トリガーに指定されたメトリックの値が時間内にインスタンス全体にわたって集計されます (該当する場合)。この集計はしきい値と比較され、ルールが適用されるかどうかが示されます。特定の期間の有効な集計は、平均値 (規定)、最小値、最大値、最終値、合計値、およびカウント値です。インスタンスに対する有効な集計は、平均値 (既定)、最大値、および最小値です。

トリガーに使用できるメトリックは Azure Storage と Service Bus のキューの長さ、Azure Diagnostics によって発行される標準パフォーマンス カウンター、および各ロールまたは仮想マシンによって発行されるカスタム パフォーマンス カウンターです。Cloud Services ソリューションでは、規定で使用可能なパフォーマンス カウンター以外を使用して処理する場合、UI でサービスの監視レベル設定を [最小] から [詳細] に変更する必要があります。

詳細については、次を参照してください。

- SDK [クラス ライブラリ](http://msdn.microsoft.com/library/azure/dn510414.aspx)の監視
- [パフォーマンス カウンターを構成する方法](http://msdn.microsoft.com/library/azure/dn535595.aspx)
- [自動スケーリングに対する操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [Add Autoscale Settings](http://msdn.microsoft.com/library/azure/dn510372.aspx)
- [Auto Scaling Cloud Services on CPU Percentage with the Azure Monitoring Services Management Library (Azure Monitoring Services 管理ライブラリを使用した CPU 使用率に基づく Cloud Services の自動スケール)](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/)
- [How to use Azure Monitoring Services Management Library to create an Autoscale Rule (Azure Monitoring Services 管理ライブラリを使用した自動スケール ルールの作成方法)](http://blogs.msdn.com/b/cie/archive/2014/02/20/how-to-use-windows-azure-monitoring-services-management-library-to-create-an-autoscale-rule.aspx)

## 関連するパターンとガイダンス
自動スケールを実装する場合、次のパターンとガイダンスも関連する可能性があります。

- [調整パターン](http://msdn.microsoft.com/library/dn589798.aspx)。このパターンは、需要の増加によってリソースに極端な負荷が掛けられた場合に、アプリケーションがどのようにして継続的に機能し、サービス レベル アグリーメントに準拠できるかを示します。自動スケールとともに調整を使用して、システムがスケールアウトしている間にシステムに過剰な負荷が掛からないようにすることができます。
- [競合コンシューマー パターン](http://msdn.microsoft.com/library/dn568101.aspx)。このパターンは、任意のアプリケーション インスタンスからのメッセージを処理できるサービス インスタンスのプールを実装する方法を示します。自動スケールを使用して、予測されるワークロードに対処できるように、サービス インスタンスを開始および停止できます。この手法を使用することで、システムは複数のメッセージを同時に処理し、スループットを最適化し、拡張性と可用性を向上させ、ワークロードのバランスを取ることができます。
- [Instrumentation and Telemetry Guidance (インストルメンテーションと製品利用統計情報のガイダンス)](http://msdn.microsoft.com/library/dn589775.aspx)。インストルメンテーションと製品利用統計情報は、自動スケール プロセスを促進できる情報を収集するために不可欠です。

## 詳細
- [アプリケーションの規模の設定方法](cloud-services-how-to-scale.md)
- [Web ロール、ワーカー ロール、または仮想マシンを実行しているアプリケーションの規模の自動設定](cloud-services-how-to-manage.md#linkresources)
- [方法: クラウド サービスに対するリソースのリンク](cloud-services-how-to-manage.md#linkresources)
- [リンク済みリソースの規模の設定](cloud-services-how-to-scale.md#scalelink)
- [Microsoft Azure Monitoring Services... 4.1.0](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring)
- [サービス管理 REST API リファレンス](http://msdn.microsoft.com/library/azure/ee460799.aspx)
- [自動スケーリングに対する操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [Microsoft.WindowsAzure.Management.Monitoring.Autoscale 名前空間](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.management.monitoring.autoscale.aspx)
- MSDN の「[Autoscaling Application Block (オートスケーリング アプリケーション ブロック)](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx)」ドキュメントと主なシナリオ。

<!---HONumber=62-->