﻿<properties 
	pageTitle="Azure App Service での Web アプリのスケール設定" 
	description="Azure App Service での、自動スケールを含む Web アプリのスケール アップとスケール ダウンの方法について説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# Azure App Service での Web アプリのスケール設定 #

Microsoft Azure で Web アプリのパフォーマンスとスループットを向上させ、より多くの機能を有効にしてビジネス ニーズを満たすために、Azure 管理ポータルを使用して、App Service プランを Free モードから Shared モード、Basic モード、Standard モード、および Premium (プレビュー) モードにスケール アップすることができます。 

Azure の Web アプリのスケール アップには、App Service プランのモードをより高いレベルのサービスに変更することと、より高いレベルのサービスに切り替えた後に特定の設定を構成することの 2 つのアクションが含まれます。この記事では、この 2 つのトピックについて説明します。標準モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

モードの変更と構成は、管理ポータルの [規模の設定] タブで簡単に実行できます。必要に応じて、規模の拡大または縮小が可能です。これらの変更を適用して、サービス プランに含まれるすべての Web アプリに反映するのにかかる時間はわずか数秒です。コードの変更やアプリケーションの再展開は必要ありません。

App Service プラン (以前の Web ホスティング プラン) の詳細については、[App Service ホスティング プラン](web-sites-web-hosting-plan-overview.md)に関するページと [Azure App Service ホスティング プランの詳細な概要](azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。App Service の個々のプランの料金と機能の詳細については、[App Service の料金の詳細](http://http://azure.microsoft.com/pricing/details/web-sites/)に関するページを参照してください。  

> [AZURE.NOTE] Web アプリを **Free** モードから **Basic** モードまたは **Standard** モードに切り替える前に、Azure の App Service サブスクリプションに設定されている使用制限を解除する必要があります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプション][azuresubscriptions]に関するページを参照してください。

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## App Service プランの Shared モードまたは Basic モードへの変更
<!-- ===================================== -->

1. ブラウザーで、[Azure ポータル][portal]を開きます。
	
2. Web アプリのブレードで、**[すべての設定]**、**[スケール]** の順にクリックし、**インスタンスを追加してパフォーマンスを向上させるため、Free プランからアップグレード**するボタンをクリックします。
	
	![Choose Hosting Plan][ChooseWHP]
	
4. **[価格レベルの選択]** ブレードで、**[Shared]** または **[Basic]** モードを選択して、**[選択]** をクリックします。

	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。 
	
5. **[インスタンス]** バーを左から右へスライドしてインスタンス数を増やし、コマンド バーの **[保存]** をクリックします。インスタンス サイズのオプションは、**Shared** モードでは利用できません。これらのインスタンス サイズの詳細については、[Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]に関するページを参照してください。
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## App Service プランの Standard モードへの変更
<!-- ================================= -->

> [AZURE.NOTE] App Service プランを **Free** モードから **Standard** モードに切り替える前に、Microsoft Azure の App Service サブスクリプションに設定されている使用制限を解除する必要があります。解除しないと、請求期間が終了する前に使用制限に達した場合に、サイトが使用できなくなるおそれがあります。Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure サブスクリプション][azuresubscriptions]に関するページを参照してください。

1. **Standard** モードに設定を変更する場合、最初の手順は **Shared** モードまたは **Basic** モードへの設定変更と同じです。次に、**[価格レベルの選択]** で **[Standard]** モードを選択して、**[選択]** をクリックします。 
	
	操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅し、**自動スケール モード**が有効になります。

	![Scale in Standard Mode][ScaleStandard]

	この場合も、上の **Basic** モードの場合と同様に、**インスタンス** バーをスライドして手動でインスタンス数を増やすことができます。ただし、ここでは**自動スケール モード**の使用方法について説明します。 

2. **[自動スケール モード]** で、**[パフォーマンス]** を選択し、パフォーマンス メトリックに基づいた自動スケールを設定します。
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. **[インスタンス範囲]** で 2 つのスライダーを移動して、App Service 用に自動スケールを設定するインスタンスの最小数と最大数を定義します。このチュートリアルでは、最大のスライダーを **6** インスタンスに移動します。

4. コマンド バーの **[保存]** をクリックします。

4. **[ターゲット メトリック]** で **[>]** をクリックして、既定のメトリックの自動スケール規則を構成します。  
	
	![Set Target Metrics][SetTargetMetrics]
	
	CPU、メモリ、ディスク キュー、HTTP キュー、データ フローなど、さまざまなパフォーマンス メトリックの自動スケール規則を構成することができます。ここでは、次のように CPU の割合の自動スケールを構成します。

	- 過去 10 分間の CPU が 70% を超える場合は、インスタンスを 1 増やす
	- 過去 5 分間の CPU が 90% を超える場合は、インスタンスを 3 増やす
	- 過去 30 分間の CPU が 50% 未満の場合は、インスタンスを 1 減らす 


4. **[メトリック]** ドロップダウンは **[CPU の割合]** のままにしておきます。
	
5. **[スケール アップ規則]** で、**[条件]** を **[より大きい]**、**[しきい値]** を **70** (%)、**[過去の持続時間]** を **10** (分)、**[スケール アップ設定]** を **1** (インスタンス)、**[クール ダウン]** を **10** (分) に設定して、最初の規則を構成します。 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] **[クール ダウン]** の設定は、直前のスケール操作が実行された後、再度スケール操作を実行するまでに、この規則が待機する時間の長さを指定します。
	
6. **[スケール アップ規則の追加]** をクリックし、**[条件]** を **[より大きい]**、**[しきい値]** を **90** (%)、**[過去の持続時間]** を **1** (分)、**[スケール アップ設定]** を **3** (インスタンス)、**[クール ダウン]** を **1** (分) に設定して、2 番目の規則を構成します。
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. **[スケール ダウン規則]** で、**[条件]** を **[より小さい]**、**[しきい値]** を **50** (%)、**[過去の持続時間]** を **30** (分)、**[スケール ダウン設定]** を **1** (インスタンス)、**[クール ダウン]** を **60** (分) に設定して、3 番目の規則を構成します。 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. コマンド バーの **[保存]** をクリックします。これで、自動スケール規則が **[スケール]** ブレードに反映されます。 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
##Web アプリに接続されている SQL Server データベースのスケール設定
(App Service のプラン モードにかかわらず) 1 つ以上の SQL Server データベースが Web アプリにリンクされている場合、ニーズに合わせてすばやくスケールを設定できます。

1. リンクされたデータベースのいずれかのスケールを設定するには、[Azure ポータル][portal]で Web アプリのブレードを開きます。折りたたみ可能な **[Essentials]** ドロップダウンで、**[リソース グループ]** リンクをクリックします。次に、リソース グループ ブレードの **[概要]** パートで、リンクされているデータベースのいずれかをクリックします。
	
	![Linked database][ResourceGroup]
	
2. リンクされている SQL データベースのブレードで **[価格レベル]** パートをクリックし、パフォーマンス要件に応じていずれかの価格レベルを選択して、**[選択]** をクリックします。 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. また、geo レプリケーションを設定して、SQL データベースの高可用性と障害復旧機能を向上させることもできます。そのためには、**[ジオ (主要地域) レプリケーション]** パートをクリックします。

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## 開発者機能
Web アプリのサービス プラン モードに応じて、以下の開発者向け機能が利用可能です。

### ビット ###

- Basic プラン モードと Standard プラン モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- Free プラン モードと Shared プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

### デバッガー サポート ###

- App Service の Free、Shared、Basic の各プラン モードでは、アプリケーションごとに 1 つの同時接続でデバッガー サポートを利用できます。
- App Service の Standard プラン モードでは、アプリケーションごとに 5 つの同時接続でデバッガー サポートを利用できます。

<a name="OtherFeatures"></a>
## その他の機能

### Web エンドポイントの監視 ###

- Web エンドポイントの監視は、App Service プランの Basic モードと Standard モードで利用可能です。Web エンドポイントの監視の詳細については、[Web アプリの監視方法](web-sites-monitor.md)に関するページを参照してください。

- すべてのユーザー (開発者を含む) が関心を持つ料金や機能など、App Service プランのその他すべての機能の詳細については、[App Service の料金の詳細](http://azure.microsoft.com/pricing/details/web-sites/)に関するページを参照してください。

<a name="Next Steps"></a>	
## 次のステップ

- Azure の使用を開始するには、[Microsoft Azure の無料評価版](http://azure.microsoft.com/pricing/free-trial/)のサイトを参照してください。

- 料金、サポート、および SLA については、次のリンクを参照してください。
	
	[Data Transfers (データ転送) の料金詳細](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Azure のサポート プラン](http://azure.microsoft.com/support/plans/)
	
	[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)
	
	[SQL Database 料金](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]
	
	[App Service の料金の詳細](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[App Service の料金の詳細 - SSL 接続](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- 拡張性と回復力に優れたアーキテクチャの構築など、Azure App Service のベスト プラクティスについては、[Microsft Azure Websites のベスト プラクティス](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)に関するページを参照してください。

- Azure Websites のスケールに関するビデオ:
	
	[Azure Websites のスケール設定のタイミング - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)のビデオ
	
	[Azure Websites の自動スケール (CPU またはスケジュール設定) - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)のビデオ

	[Azure Websites のスケール設定方法 - Stefan Schackow 共演](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)のビデオ



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->