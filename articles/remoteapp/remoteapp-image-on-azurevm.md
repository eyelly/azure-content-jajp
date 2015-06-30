<properties 
    pageTitle="Azure VM に基づく RemoteApp イメージの作成"
    description="Azure 仮想マシンを使用して RemoteApp 起動用のイメージを作成する方法について説明します。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/27/2015" 
    ms.author="elizapo" />



# Azure 仮想マシンに基づく RemoteApp イメージの作成

お客様からの多くのフィードバックに基づいて、Microsoft では Azure 仮想マシンからの RemoteApp イメージ (コレクション内で共有するアプリを保持します) 作成のサポートを始めました。また、Azure VM イメージ ギャラリーに、RemoteApp イメージのすべての要件を満たす仮想マシン イメージも追加しました。必要に応じて、この VM イメージを独自の VM の開始点として使用することができます。ライブラリにある "Windows Server Remote Desktop Session Host" イメージを探してみましょう。

イメージの作成と、Azure VM ライブラリから RemoteApp へのイメージのアップロードという 2 つの手順があります。

## Azure VM に基づくカスタム イメージの作成

次の手順を使用して、Azure VM に基づくイメージを作成します。

1. Azure 仮想マシンを作成します。Azure 仮想マシン イメージ ギャラリーの "Windows Server Remote Desktop Session Host" イメージを使用できます。このイメージは、Azure RemoteApp テンプレート イメージのすべての要件を満たしています。 

	詳細については、[Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md).に関するページを参照してください。

2. VM に接続し、RemoteApp を使用して共有するアプリをインストールして構成します。アプリに必要な追加の Windows の構成も実行してください。 

	詳細については、[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md).に関するページを参照してください。 

3. Windows Server リモート デスクトップ セッション ホストのイメージを使用している場合は、VM が RemoteApp の前提条件を満たしていることを確認するための検証スクリプトが含まれています。スクリプトを実行するには、デスクトップ上にある **[ValidateRemoteAppImage]** をダブルクリックします。次の手順に進む前に、スクリプトによって報告されたエラーはすべて修正します。

4. SYSPREP は、イメージを一般化してキャプチャします。手順については、[テンプレートとして使用する Windows 仮想マシンのキャプチャ方法](../virtual-machines-capture-image-windows-server.md)に関するページを参照してください。

 

## RemoteApp イメージ ライブラリへのイメージのインポート

次の手順を使用して、新しいイメージを RemoteApp にインポートします。

1. **[テンプレート イメージ]** タブで次の操作を実行します。
	- 既存のイメージがない場合は、**[テンプレート イメージのアップロードまたはインポート]** をクリックします。 
	- 既に 1 つ以上のイメージがある場合は、**[+]** をクリックして新しいイメージを追加します。

2. **[仮想マシンからイメージをインポート]** ライブラリを選択し、**[次へ]** をクリックします。

3. 次のページで、一覧からカスタム イメージを選択し、イメージを作成したときに表示された手順に従っていることを確認します。**[次へ]** をクリックします。
4. 新しい RemoteApp イメージの名前を入力して場所を選択したら、インポート プロセスを開始するチェックマークをクリックします。

> [AZURE.NOTE] Azure 仮想マシンでサポートされている Azure の任意の場所から、Azure RemoteApp でサポートされている Azure の任意の場所に、イメージをインポートできます。インポート時間は場所によって異なり、最大 25 分かかります。

これで、ニーズに応じて新しいコレクション ([クラウド](remoteapp-create-cloud-deployment.md)  コレクションか[ハイブリッド](remoteapp-create-hybrid-deployment.md) コレクションのいずれか) を作成する準備ができました。


<!--HONumber=52--> 