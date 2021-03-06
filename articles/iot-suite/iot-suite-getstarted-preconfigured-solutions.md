<properties
	pageTitle="構成済みソリューションの使用 | Microsoft Azure"
	description="このチュートリアルに従って、Azure IoT Suite 事前構成済みソリューションのデプロイ方法について学習します。"
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# チュートリアル: IoT 事前構成済みソリューションの使用

## はじめに

Azure IoT Suite の事前構成済みソリューションにより、さまざまな Azure IoT サービスを接続し、IoT (Internet of Things: モノのインターネット) のビジネス シナリオを満たすエンド ツー エンド ソリューションを実証できます。

このチュートリアルでは、**リモート監視**という事前構成済みソリューションをプロビジョニングする方法を示します。また、リモート監視事前構成済みソリューションの基本機能を表示する方法も示します。

このチュートリアルを完了するには、以下が必要になります。

-   有効な Azure サブスクリプション

    アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][]を参照してください。

## リモート監視の事前構成済みソリューションのプロビジョニング

1.  https://www.azureiotsuite.com にログオンし、**[+]** をクリックして、新しいソリューションを作成します。

2.  ソリューションの種類として、**[リモート監視]** を選択します。

3.  リモート監視事前構成済みソリューションの **[ソリューション名]** を入力します。

4.  このソリューションをプロビジョニングする **[リージョン]** と **[サブスクリプション]** を検証します。

5.  **[ソリューションの作成]** をクリックします。

## リモート監視ソリューションのダッシュボードの表示

1.  プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** をクリックし、新しいタブでリモート監視ソリューションのダッシュボードを開きます。

2.  既定では、左側のメニューで **[ダッシュボード]** が選択されます。これが、ソリューションのダッシュボードです。

## ソリューションのデバイス一覧の表示

1.  左側のメニューで **[デバイス]** をクリックし、このソリューションのデバイス一覧に移動します。

2.  プロビジョニングすると、プロビジョニング済みの 4 つのシミュレートされたデバイスが表示されます。

3.  デバイス一覧で、**個々のデバイス**をクリックし、そのデバイスに関連付けられているデバイスの詳細を表示します。

## デバイスへのコマンドの送信

1.  選択したシミュレートされたデバイスのデバイスの詳細ペインで **[コマンドの送信]** をクリックします。

2.  コマンドの一覧から **[PingDevice]** を選択します。

3.  **[コマンドの送信]** をクリックします。

4.  コマンドの状態がコマンドの履歴に表示されるので、確認します。

## 新しいシミュレートされたデバイスの追加

1.  **[←]** (左向きの矢印) をクリックし、デバイス一覧に戻ります。

2.  左下隅の **[+ デバイスの追加]** をクリックし、新しいデバイスを追加します。

3.  **シミュレートされたデバイス**で **[新規追加]** をクリックします。

4.  **[自分で自分のデバイス ID を定義する]** を選択し、一意のデバイス ID 名を追加します。

5.  **[作成]** をクリックします。

6.  **[←]** (左向きの矢印) をクリックし、デバイス一覧に戻ります。

7.  デバイス一覧でデバイスが **[実行中]** であることを確認します。

## ソリューションの規則の表示と編集

1.  **ソリューションのダッシュボード**の **[アラーム履歴]** テーブルに注意します。

2.  これらのアラームは、**[規則]** で指定されている規則の出力 **AlarmTemp** によってトリガーされます。

3.  左側のメニューで **[規則]** をクリックし、このソリューションの規則に移動します。

4.  プロビジョニングすると、既に有効になった 1 つの規則が表示されます。

5.  規則の一覧でその**規則**をクリックし、関連付けられている規則のプロパティを表示します。

6.  規則のプロパティ ペインで **[編集]** をクリックします。

7.  **[しきい値]** を 30 に変更して、その他のすべてのプロパティは同じままにします。

8.  **[規則の保存と表示]** をクリックします。

9.  **ソリューションのダッシュボード**の **[アラーム履歴]** テーブルに戻り、規則を更新した結果としてトリガーが変更されていることを確認します。

## 次のステップ

これで、機能する事前構成済みソリューションを構築しましたので、次のシナリオに進むことができます。

-   [事前構成済みソリューションのカスタマイズに関するガイダンス][]

-   [IoT Suite の概要][]

[Azure の無料試用版サイト]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[事前構成済みソリューションのカスタマイズに関するガイダンス]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[IoT Suite の概要]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO3-->