<properties 
   pageTitle="クラウド サービスのパフォーマンスのテスト"
   description="Visual Studio プロファイラーを使用した、クラウド サービスのパフォーマンスのテスト"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# クラウド サービスのパフォーマンスのテスト 

##概要

クラウド サービスのパフォーマンスは次の方法でテストすることができます。

- Azure 診断を使用して、要求と接続に関する情報を収集し、顧客の観点からサービスを実行する方法を示すサイトの統計情報を確認します。開始するには、「[Azure Cloud Services および Virtual Machines 用の診断の構成](http://go.microsoft.com/fwlink/p/?LinkId=623009)」を参照してください。

- Virtual Studio のプロファイラーを使用して、サービスの実行方法について計算的側面から詳細な分析を取得します。このトピックの説明のように、プロファイラーを使用して Azure で実行されるサービスのパフォーマンスを測定できます。コンピューティング エミュレーターでローカルに実行されるサービスのパフォーマンスをプロファイラーで測定する方法については、「[Visual Studio プロファイラーを使用した、Azure コンピューティング エミュレーターでのクラウド サービスのパフォーマンスのローカルなテスト](http://go.microsoft.com/fwlink/p/?LinkId=262845)」を参照してください。



## パフォーマンスのテスト方法を選択する

###Azure 診断を使用して収集するもの:###

- 要求や接続などの Web ページまたはサービスの統計情報。

- ロールの再起動の頻度などのロールの統計情報。

- ガベージ コレクターにかかる時間の割合や実行中のロールのメモリ総量など、メモリの使用状況に関する全体的な情報。

###Visual Studio プロファイラーを使用する目的:###

- 特に時間がかかる機能を決定する。

- 負荷の高いプログラムの各部分にかかる時間を測定する。

- 詳細なパフォーマンス レポートをサービスの 2 つのバージョンで比較する。

- 個別のメモリ割り当てよりも詳細なレベルでメモリ割り当てを分析する。

- マルチスレッド コードの同時実行の問題を分析する。

プロファイラーを使用すると、ローカルまたは Azure でクラウド サービスを実行する際にデータを収集できます。

###プロファイル データをローカルで収集する目的:###

- 実際にシミュレートされた負荷を必要としない特定の worker ロールの実行など、クラウド サービスの一部のパフォーマンスをテストする。

- 分離され、制御された条件下で、クラウド サービスのパフォーマンスをテストする。

- クラウド サービスを Azure にデプロイする前にパフォーマンスをテストする。

- 既存のデプロイメントに影響を及ぼすことなく、非公開でクラウド サービスのパフォーマンスをテストする。

- Azure で実行するための料金を発生させることなく、サービスのパフォーマンスをテストする。

###プロファイル データを Azure で収集する目的:###

- シミュレートされた、または実際の負荷でクラウド サービスのパフォーマンスをテストする。

- このトピックの後半で説明するように、プロファイル データの収集のインストルメンテーション メソッドを使用する。

- サービスの運用時と同じ環境で、サービスのパフォーマンスをテストする。

通常は、標準またはストレス条件で Cloud Services をテストするための負荷をシミュレートします。

## Azure でクラウド サービスをプロファイルする

Visual Studio からクラウド サービスを発行すると、サービスをプロファイルし、必要な情報を提供するプロファイル設定を指定できます。プロファイル セッションは、ロールの各インスタンスで開始されます。Visual Studio からサービスを発行する方法の詳細については、「[Visual Studio から Azure クラウド サービスへの発行](https://msdn.microsoft.com/library/azure/ee460772.aspx)」を参照してください。

Visual Studio でのパフォーマンスのプロファイルの詳細については、「[パフォーマンス プロファイルの初心者向けガイド](https://msdn.microsoft.com/library/azure/ms182372.aspx)」および「[プロファイル ツールを使用したアプリケーション パフォーマンスの分析](https://msdn.microsoft.com/library/azure/z9z62c29.aspx)」を参照してください。

>[AZURE.NOTE]クラウド サービスを発行するときに、IntelliTrace またはプロファイルを有効にすることができます。両方を有効にすることはできません。

###プロファイラーの収集メソッド###
パフォーマンスの問題に基づいて、プロファイルのためのさまざまな収集メソッドを使用できます。

- **CPU サンプリング** - このメソッドでは、CPU 使用率の問題の初期分析に役立つアプリケーションの統計情報を収集します。CPU サンプリングは、ほとんどのパフォーマンス調査を開始するために推奨されるメソッドです。CPU のサンプリング データを収集する際に、プロファイルするアプリケーションにはあまり影響を与えません。

- **インストルメンテーション** - このメソッドでは、対象を絞った分析および入出力のパフォーマンスの問題の分析に役立つ詳細なタイミング データを収集します。インストルメンテーション メソッドはプロファイル実行中にモジュールで行われた関数の入力、終了、および関数呼び出しをすべて記録します。このメソッドは、コードのセクションに関する詳細なタイミング情報を収集したり、入出力操作がアプリケーションのパフォーマンスに与える影響を理解したりするのに便利です。このメソッドは、32 ビットのオペレーティング システムを実行するコンピューターでは無効です。このオプションは、クラウド サービスをコンピューティング エミュレーターでローカルにではなく、Azure で実行する場合にのみ有効です。

- **.NET メモリ割り当て** - このメソッドは、サンプリング プロファイル メソッドを使用して .NET Framework メモリ割り当てデータを収集します。収集されたデータには割り当てられたオブジェクトの数とサイズが含まれています。

- **同時実行** - このメソッドは、リソース競合データと、マルチスレッドおよびマルチプロセス アプリケーションの分析に役立つプロセスおよびスレッドの実行データを収集します。同時実行メソッドは、アプリケーション リソースへのアクセスのロックが解放されるのをスレッドが待機している場合などに、コードの実行をブロックする各イベントのデータを収集します。このメソッドはマルチスレッド アプリケーションを分析する場合に便利です。

- **階層の相互作用のプロファイル**を有効にすることもできます。これは、1 つまたは複数のデータベースと通信する多層アプリケーションの関数の ADO.NET 同期呼び出しの実行時間に関する追加情報を提供します。どのプロファイル メソッドでも階層の相互作用データを収集できます。階層の相互作用のプロファイルの詳細については、「[階層の相互作用ビュー](https://msdn.microsoft.com/library/azure/dd557764.aspx)」を参照してください。

## プロファイル設定を構成する

次の図は、[Azure アプリケーションの公開] ダイアログ ボックスからプロファイル設定を構成する方法を示します。

![プロファイル設定の構成](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)


### プロファイル設定を構成するには

1. ソリューション エクスプローラーで Azure プロジェクトのショートカット メニューを開き、**[発行]** を選択します。クラウド サービスを発行する方法の詳細については、「[Azure Tools を使用したクラウド サービスの発行](http://go.microsoft.com/fwlink/p?LinkId=623012)」を参照してください。

1. **[Azure アプリケーションの公開]** ダイアログ ボックスで、**[詳細設定]** タブを選択します。

1. プロファイルを有効にするには、**[プロファイルを有効にする]** チェック ボックスを選択します。

1. プロファイル設定を構成するには、**[設定]** ハイパーリンクを選択します。[プロファイル設定] ダイアログ ボックスが表示されます。

1. **[使用するプロファイル方法を指定してください]** オプション ボタンで、必要なプロファイルの種類を選択します。

1. 階層の相互作用プロファイル データを収集するには、**[階層の相互作用のプロファイルを有効にする]** チェック ボックスを選択します。

1. 設定を保存するには、**[OK]** ボタンをクリックします。このアプリケーションを発行すると、これらの設定が各ロールのプロファイル セッションを作成するために使用されます。

## プロファイル レポートを表示する

プロファイル セッションは、クラウド サービスのロールの各インスタンスに対して作成されます。Visual Studio から各セッションのプロファイル レポートを表示するには、[サーバー エクスプローラー] ウィンドウを表示し、Azure の計算ノードを選択することで、ロールのインスタンスを選択できます。これで、次の図に示すように、プロファイル レポートを表示できます。

![Azure からのプロファイル レポートの表示](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)



### プロファイル レポートを表示するには

1. Visual Studio でサーバー エクスプローラーを表示するには、メニュー バーの [表示] を選択して、[サーバー エクスプローラー] を選択します。

1. Azure の計算ノードを選択し、Visual Studio から発行したときにプロファイルに選択したクラウド サービスの Azure デプロイメント ノードを選択します。

1. インスタンスのプロファイル レポートを表示するには、サービスのロールを選択し、特定のインスタンスのショートカット メニューを開いてから、[プロファイル レポートの表示] を選択します。レポートが .vsp ファイルで Azure からダウンロードされ、ダウンロードの状態が Azure のアクティビティ ログに表示されます。ダウンロードが完了したら、<Role name>\_<Instance Number>\_<identifier>.vsp という名前のプロファイル レポートが Visual Studio のエディターのタブに表示されます。レポートの概要データが表示されます。

1. [現在のビュー] の一覧にレポートの異なるビューを表示するには、表示するビューの種類を選択します。詳細については、「[プロファイル ツールのレポート ビュー](https://msdn.microsoft.com/library/bb385755.aspx)」を参照してください。

## 次のステップ

[Cloud Services のデバッグ](http://go.microsoft.com/fwlink//p/?LinkID=62304)

[Visual Studio から Azure クラウド サービスへの発行](http://go.microsoft.com/fwlink//p/?LinkID=623012)

<!---HONumber=August15_HO8-->