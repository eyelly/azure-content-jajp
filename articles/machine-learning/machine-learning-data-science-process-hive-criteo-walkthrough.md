<properties 
	pageTitle="Advanced Analytics Process and Technology の活用: 1 TB Criteo データセットで Hadoop クラスターを使用する | Microsoft Azure" 
	description="HDInsight Hadoop クラスターを用いたエンド ツー エンドのシナリオに Adva nced Analytics Process and Technology (ADAPT) を使用し、大量の (1 TB) 公開されている使用可能なデータセットを使用してモデルを構築してデプロイします。" 
	metaKeywords="" 
	services="machine-learning,hdinsight" 
	solutions="" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Advanced Analytics Process and Technology の活用 - 1 TB (テラバイト) データセットで Azure HDInsight Hadoop クラスターを使用する

このチュートリアルでは、[Azure HDInsight Hadoop クラスター](http://azure.microsoft.com/services/hdinsight/)によるエンド ツー エンドのAdvanced Analytics Process and Technology (ADAPT) を使用して、公開されている 1 つの [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットからデータの格納、探索、特徴エンジニアリング、ダウンサンプリングを行う方法について説明します。Azure Machine Learning を使用して、このデータに対して二項分類モデルと回帰モデルを構築します。また、これらのモデルのいずれかを Web サービスとして発行する方法も示します。

iPython Notebook を使用して、このチュートリアルで説明するタスクを実行することもできます。この方法を試してみたい方は、「[Criteo walkthrough using a Hive ODBC connection (Hive ODBC の接続を使用した Criteo チュートリアル)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)」のトピックをご覧ください。


## <a name="dataset"></a>Criteo データセットの説明

Criteo データは、gzip で圧縮された約 370 GB の TSV ファイル (非圧縮で ~1.3TB) のクリック予測データセットで、43 億以上のレコードが含まれています。これは、[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) によって提供される 24 日間のクリック データから取得されます。データ サイエンティストが使いやすいように、実験のために公開されているたデータを解凍しました。

このデータセットの各レコードには、40 の列があります。

- 最初の列は、ユーザーが追加をクリックする (値 1) またはクリックしない (値 0) ことを示すラベル列です。 
- 次の 13 の列は数値列です。 
- 最後の 26 の列はカテゴリ列です。 

列は匿名で、"Col1" (ラベル列) から "Col40" (最後のカテゴリ列) まで一連の列挙型名を使用します。

次に、このデータセットから最初の 20 列の 2 つの観測 (行) を抜粋して示します。

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

不足値がこのデータセットの数値列とカテゴリ列の両方にあります。不足値を処理するための単純なメソッドについて以下で説明します。データの詳細については、Hive テーブルにデータを格納するときに以下で説明します。

**定義:** *クリックスルー率 (CTR):* これは、データ内のクリックの割合です。この Criteo データセットの CTR は、約 3.3% または 0.033 です。

## <a name="mltasks"></a>予測タスクの例
このチュートリアルでは、次の 2 つのサンプルの予測問題に対処します。

1. **二項分類**: ユーザーが追加をクリックしたかどうかを予測します。
	- クラス 0: クリックしない
	- クラス 1: クリックする

2. **回帰**: ユーザーの特徴から広告クリックの確率を予測します。


## <a name="setup"></a>データ サイエンス用に HDInsight Hadoop クラスターをセット アップする

**注:** これは、通常 **Admin** タスクです。

HDInsight クラスターを使用して予測分析ソリューションを構築するために、3 つの手順で Azure のデータ サイエンス環境をセット アップします。

1. [ストレージ アカウントの作成](../storage-whatis-account.md): このストレージ アカウントは、Azure BLOB ストレージにデータを格納するために使用します。ここには、HDInsight クラスターで使用するデータが格納されます。

2. [Azure HDInsight Hadoop Clusters for Data Science のカスタマイズ](machine-learning-data-science-customize-hadoop-cluster.md): この手順では、すべてのノードにインストールされている 64 ビット Anaconda Python 2.7 を使用して Azure HDInsight Hadoop クラスターを作成します。HDInsight クラスターをカスタマイズするときに実行する、2 つの重要な手順があります (このトピックで説明します)。

	* 作成時に、手順 1. で作成したストレージ アカウントを HDInsight クラスターにリンクする必要があります。このストレージ アカウントは、クラスター内で処理可能なデータにアクセスするために使用されます。

	* 作成したら、クラスターのヘッド ノードへのリモート アクセスを有効にする必要があります。ここで指定するリモート アクセス資格情報を覚えておいてください (作成時にクラスターに指定したものとは異なります)。以下で必要になります。

3. [Azure ML ワークスペースを作成する](machine-learning-create-workspace.md): この Azure Machine Learning ワークスペースは、HDInsight クラスターでの初期データの探索とダウンサンプリング後に、機械学習モデルを構築するために使用します。

## <a name="getdata"></a>公開されているソースからデータを取得して使用する

[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットにアクセスするには、リンクをクリックし、使用条件に同意して名前を入力します。この画面のスナップショットを以下に示します。

![Criteo 条項に同意する](http://i.imgur.com/hLxfI2E.png)

**[Continue to Download]** をクリックしてデータセットとその可用性に関する詳細をお読みください。

このデータはパブリックの [Azure BLOB ストレージ](../storage-dotnet-how-to-use-blobs.md)にありますwasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/。"wasb" は、Azure BLOB ストレージの場所を表します。

1. このパブリック BLOB ストレージ内のデータは、解凍されたデータの 3 つのサブフォルダーで構成されています。
		
	1. サブフォルダー *raw/count/* には、最初の 21 日間 (day_00 から day_20 まで) のデータが含まれています。
	2. サブフォルダー *raw/train/* には、1 日分 (day_21) のデータが含まれています。
	3. サブ フォルダー *raw/test/* には、2 日間 (day_22 と day_23) のデータが含まれています。

2. 生の gzip データを使用したいユーザーのために、これらはメインフォルダー *raw/* 内で day_NN.gz (NN は 00 ～ 23) として使用することもできます。

ローカルのダウンロードを必要としない別の手法でこのデータにアクセスして探索し、モデリングする方法については、Hive テーブルを作成するときに、このチュートリアルの後半で説明します。

## <a name="login"></a>クラスター ヘッドノードにログインする

クラスターのヘッドノードにログインするは、[Azure 管理](manage.windowsazure.com)ポータルを使用してクラスターを検索します。左側の HDInsight の象のアイコンをクリックし、クラスターの名前をダブルクリックします。**[構成]** タブに移動し、ページの下部にある [接続] アイコンをダブルクリックし、プロンプトが表示されたら、リモート アクセスの資格情報を入力します。入力すると、クラスターのヘッドノードに移動します。

クラスター ヘッドノードに初めてログインすると、通常、次のような画面が表示されます。

![クラスターにログインする](http://i.imgur.com/Yys9Vvm.png)

左側に、"Hadoop コマンド ライン" が表示されます。これは、データ探索用のマイクロソフトの主力製品です。2 つの便利な URL "Hadoop Yarn のステータス" と "Hadoop 名前ノード" も表示されます。Yarn ステータスの URL はジョブの進行状況を示し、名前ノードの URL はクラスター構成の詳細を示します。

これで、チュートリアルの最初の部分 (Hive を使用したデータ探索と Azure Machine Learning のデータの準備) を設定して開始する準備ができました、

## <a name="hive-db-tables"></a>Hive データベースとテーブルを作成する

Criteo データセットの Hive テーブルを作成するには、ヘッド ノードのデスクトップで ***Hadoop コマンド ライン*** を開き、次のコマンドを入力して Hive ディレクトリに入ります。

    cd %hive_home%\bin

**重要**: **このチュートリアルでは、上記の Hive bin/ ディレクトリ プロンプからのすべての Hive コマンドを実行します。これは自動的にすべてのパスの問題に対処します。"Hive ディレクトリ プロンプト"、"Hive bin/ ディレクトリ プロンプト"、"Hadoop コマンドライン" という用語は同じ意味で使用されます。**

**重要 2**: **Hive クエリを実行するには、次のコマンドの後に実行してください。** cd %hive_home%\bin hive

Hive REPL が "hive >" 記号と共に表示されたら、単にクエリをカット アンド ペーストして実行します。

以下のコードは、データベース "criteo" を作成した後、次の 4 つのテーブルを生成します。


* day_00 ～ day_20 の期間に構築された 1 つの*カウント テーブル*。 
* day_21 に構築された 1 つの*トレーニング テーブル*。 
* day_22 と day_23 にそれぞれ構築された 2 つの*テスト テーブル*。 

いずれかの日が休日であり、モデルがクリックスルー率から休日と休日以外の違いを検出できるかどうかを判断するため、テスト データセットは 2 つの異なるテーブルに分割します。

スクリプト [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) を以下に示します。

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

単に Azure BLOB ストレージ (wasb) の場所を指す場合、これらのテーブルはすべて外部にあることに注意してください。

**任意の Hive クエリを実行するには 2 つの方法があります。**

1. **Hive REPL コマンド ラインを使用する**: 最初の方法は "hive" コマンドを発行し、Hive REPL コマンド ラインで上記のクエリをコピーして貼り付けることです。これを行うには、次のコマンドを実行します。

		cd %hive_home%\bin
		hive

 	REPL コマンド ラインで、クエリをカット アンド ペースして実行します。

2. **ファイルにクエリを保存し、コマンドを実行する**: 2 番目の方法は .hql ファイル ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) にクエリを保存し、次のコマンドを発行してクエリを実行することです。

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### データベースとテーブルの作成を確認する

次に、Hive bin/ ディレクトリ プロンプトから次のコマンドを発行してデータベースの作成を確認します。

		hive -e "show databases;"

次のように表示されます。

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

これは、新しいデータベース "criteo" の作成を確認します。

作成されたテーブルを表示するには、Hive bin/ ディレクトリ プロンプトから次のコマンドを発行します。

		hive -e "show tables in criteo;"

次の出力が表示されます。

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Hive でデータを探索する

これで、Hive でいくつかの基本的なデータ探索を行う準備ができました。まず、トレーニングとテストのデータ テーブルでサンプル数をカウントすることから始めます。

### トレーニングのサンプル数

[sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) の内容を以下に示します。

		SELECT COUNT(*) FROM criteo.criteo_train;

次が生成されます。

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

代わりに、Hive bin/ ディレクトリ プロンプトから次のコマンドを発行することもできます。

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### 2 つのテスト データセットのテスト サンプル数

2 つのテスト データセットのサンプル数をカウントします。[sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) の内容を以下に示します。

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

次が生成されます。
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

通常どおり、次のコマンドを発行して Hive bin/ ディレクトリ プロンプトからスクリプトを呼び出すこともできます。

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

最後に、day_23 に基づくテスト データセット内のテスト サンプル数を確認します。

このコマンドは上記と同様です ([sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql) をご覧ください)。

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

次のように表示されます。
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### トレーニング データセット内のラベルの分布

対象はトレーニング データセット内のラベルの分布です。これを参照するために、[sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) の内容を示します。

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

ラベルの分布が生成されます。

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

正の値のラベルの割合が約 3.3% であることに注意してください (元のデータセットと一致します)。
		
### トレーニング データセット内の一部の数値変数のヒストグラム分布

Hive のネイティブの "histogram_numeric" 関数を使用して、数値変数の分布がどのように表示されるかを確認できます。[sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) の内容を次に示します。

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

次のように生成されます。

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

Hive の LATERAL VIEW - explode の組み合わせは、通常のリストの代わりに、SQL に似た出力を生成する機能があります。上の表では、最初の列は bin center に対応し、2 番目の列は bin frequency に対応しています。

### トレーニング データセット内の一部の数値変数の百分位を概算する

概算の百分位の計算も数値変数の対象です。Hive のネイティブ "percentile_approx" がこれを実行します。[sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) の内容を以下に示します。

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

次が生成されます。

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

百分位の分布が任意の数値変数のヒストグラム分布に密接に関連していることがわかります。

### トレーニング データセット内の一部のカテゴリ列の一意の値の数を検索する

データの探索を続けると、一部のカテゴリ列が一意の値の数を取得することがわかります。これを行うために、[sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) の内容を示します。

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

次が生成されます。

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 は 19 M 個の一意の値を持つことに注意してください。 "one-hot エンコーディング" のような単純な手法を使用して、このような高次元カテゴリ変数をエンコードすることはできません。この問題に効率的に取り組むために、[カウントを使用した学習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)と呼ばれる強力で信頼性の高い手法について説明します。

他のカテゴリ列の一意の値の数を確認すると、このサブ セクションが完了します。[sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) の内容を以下に示します。

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

次が生成されます。

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

ここでも、Col20 以外のすべての列が、多くの一意の値を持つことがわかります。

### トレーニング データセット内のカテゴリ変数ペアの同時発生カウント

カテゴリ変数ペアの同時発生カウントも対象です。これは、[sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) 内のコードを使用して判断できます。

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

発生カウントの順序を逆にして上位 15 を示します。次のように表示されます。

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Azure Machine Learning のデータセットのダウンサンプリング

データセットを探索し、この種類の任意の変数 (組み合わせを含む) の探索を行う方法を示すと、データ セットをダウンサンプリングして Azure Machine Learning にモデルを構築できるようになります。重点を置く問題を思い出してください。属性のサンプル セット (Col2 ～ Col40 の特徴の値) がある場合、Col1 が 0 (クリックする) か、1 (クリックしない) かを予測します。

トレーニングとテストのデータセットを元のサイズの 1% にダウンサンプリングするには、Hive のネイティブ RAND() 関数を使用します。次のスクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) は、トレーニング データセット対して実行します。

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

次が生成されます。

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

スクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) は、day_22 のテスト データに対し実行します。

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

次が生成されます。

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


最後に、スクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) は、day_23 のテスト データに対し実行します。

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

次が生成されます。

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

これで、ダウンサンプリングされたトレーニングとテストのデータセットを使用して Azure Machine Learning でモデルを構築する準備ができました。

Azure Machine Learning に移動する前に、カウント テーブルに関する最後の重要なコンポーネントがあります。次のサブ セクションでは、これについて説明します。

##<a name="count"></a>カウント テーブルの簡単な説明

説明したように、いくつかのカテゴリ変数は非常に高次元です。このチュートリアルでは、[カウントを使用した学習](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)と呼ばれる強力な手法について説明し、これらの変数を効率的で信頼性の高い方法でエンコードします。この手法の詳細については、リンクをクリックしてください。

**注:** このチュートリアルでは、高次元のカテゴリ特徴のコンパクトな表現を生成するために、カウント テーブルの使用に重点を置いていします。これは、必ずしもカテゴリ特徴をエンコードする唯一の方法ではありません。関心のある方は、他の手法の詳細について [one-hot エンコーディング](http://en.wikipedia.org/wiki/One-hot)と[特徴ハッシュ](http://en.wikipedia.org/wiki/Feature_hashing)をご覧ください。

カウント データのカウント テーブルを作成するには、raw/count フォルダーのデータを使用します。モデリング セクションでは、カテゴリ特徴のカウント テーブルを最初から構築する方法と、探査のために事前に構築されたカウント テーブルを使用する別の方法を示します。以下では、"事前に構築されたカウント テーブル" に言及する場合は、提供されているカウント テーブルを使用することを意味します。次に、これらのテーブルにアクセスする方法の詳細について説明します。

## <a name="aml"></a>Azure Machine Learning でモデルをビルドする

Azure Machine Learning のモデル構築プロセスは、次の手順を実行します。

1. [Azure Machine Learning に Hive テーブルからデータを取得する](#step1)
2. [実験を作成する: データを整理し、学習者を選択し、カウント テーブルを特徴付ける](#step2)
3. [モデルをトレーニングする](#step3)
4. [テスト データでモデルにスコアを付ける](#step4)
5. [モデルを評価する](#step5)
6. [使用する Web サービスとしてモデルを発行する](#step6)

これで、Azure Machine Learning Studio でモデルを構築する準備ができました。ダウンサンプリングされたデータは、クラスター内に Hive テーブルとして保存されます。このデータを読み取るには、Azure Machine Learning リーダー モジュールを使用します。このクラスターのストレージ アカウントにアクセスするための資格情報は、以下のとおりです。

### <a name="step1"></a> 手順 1: リーダー モジュールを使用して、Azure Machine Learning に Hive テーブルからデータを取得し、機械学習の実験用に選ぶ

**リーダー** モジュールでグラフィックに設定されているパラメーターの値は、単なる例を示します。ここでは、**リーダー** モジュールのパラメーター セットを記述する場合の一般的なガイダンスを示します。

1. [Hive クエリ] をデータ ソースに選択します。
2. [Hive クエリ] では、SELECT * FROM <your_database_name.your_table_name> -のみで十分です。
3. [Hcatalog サーバー URI]: クラスターが "abc" の場合は、https://abc.azurehdinsight.net となります。
4. [Hadoop ユーザーのアカウント名]: クラスターの設定時に選択したユーザー名 (リモート アクセスのユーザー名ではありません)。
5. [Hadoop ユーザー アカウントのパスワード]: クラスターの設定時に選択したユーザー名のパスワード (リモート アクセスのパスワードではありません)。
6. [出力データの場所]: [Azure] を選択します。
7. [Azure ストレージ アカウント名]: クラスターに関連付けられているストレージ アカウント。
8. [Azure ストレージ アカウント キー]: クラスターに関連付けられているストレージ アカウントのキー。
9. [Azure コンテナー名]: クラスター名が "abc" の場合、通常は単に "abc" となります。 

**リーダー**は、Hive テーブルからデータを取得中に次のように表示されます。

![リーダーによるデータの取得](http://i.imgur.com/i3zRaoj.png)

**リーダー** がデータの取得を終了したら (モジュールに緑色のチェック マークが表示されます)、このデータをデータセットとして保存します (任意の名前を付けます)。次のように表示されます。

![リーダーによるデータの保存](http://i.imgur.com/oxM73Np.png)


**リーダー** モジュールの出力ポートを右クリックすると、**[データセットとして保存]** オプションと **[視覚化]** オプションが表示されます。**[視覚化]** オプションをクリックすると、概要統計情報に役立つ 100 行のデータが右側のパネルに表示されます。データを保存するには、**[データセットとして保存]** を選択して指示に従います。

機械学習の実験用で使用する保存済みのデータセットを選ぶには、以下に示す **[検索]** を使用してデータセットを検索します。名前の一部を入力してデータセットにアクセスし、このデータセットをメイン パネルにドラッグします。メイン パネル上にドロップしたら、機械学習のモデリングで使用するために選択します。

![データセットを検索する](http://i.imgur.com/rx4nnUH.png)

トレーニングとテストの両方のデータセットでこれを行います。

### <a name="step2"></a> 手順 2: Azure Machine Learning Studio で簡単な実験を作成して、クリックする / クリックしないを予測します。

最初に単純な実験のアーキテクチャを示し、次に詳細にドリルダウンします。まず、データを整理して学習者を選択します。最後に、事前に構築されたカウント テーブルまたはユーザーが最初から構築したカウント テーブルを特徴付ける方法を示します。

![実験のアーキテクチャ](http://i.imgur.com/R4iTLYi.png)

ドリルダウンするには、以下に示すように保存済みのデータセットを使用します。

**見つからないデータのクリーンアップ** モジュールは、ユーザーが指定できる方法で不足しているデータをクリーンアップします。このモジュールは次のように表示されます。

![見つからないデータのクリーンアップ](http://i.imgur.com/0ycXod6.png)

ここでは、すべての不足値を 0 に置き換えることにしました。他のオプションは、モジュールのドロップダウン リストに表示されます。

次に、学習者を選ぶ必要があります。2 つのクラスのブースト デシジョン ツリーを学習者として使用します。特に、高次元のカテゴリ特徴のカウント特徴を使用して、モデルのコンパクトな表現を構築するする方法と、効率的なトレーニングとテストについても説明します。

続いて、カウント テーブルとは何かについて説明します。これらはクラスの条件付きのカウントです ("条件付きのカウント" と呼ぶ場合もあります)。これらは基本的に、各クラスの特徴の値をカウントし、このカウントを使用して対数オッズを計算する方法です。


#### モデリングのために事前に構築されたカウント テーブルにアクセスする

事前に構築されたカウント テーブルにアクセスするには、**[ギャラリー]** クリックしてください。

![ギャラリー](http://i.imgur.com/TsWkig3.png)

**[ギャラリー]** をクリックすると、次のようなページが表示されます。

![ギャラリー メインページ](http://i.imgur.com/dmXo0KR.png)

ここで、"criteo counts" という語句を検索し、結果の一覧を下にスクロールします。次のような結果が表示されます。

![Criteo カウント](http://i.imgur.com/JZ119Jf.png)

この実験をクリックすると、次のようなページが表示されます。

![カウント](http://i.imgur.com/dxdjMjh.png)

**[Open in studio]** をクリックし、この実験をワークスペースにコピーします。これにより、データセットもコピーされます。この場合は、対象の 2 つのデータセットは、カウント テーブルとカウント メタデータです。詳しく説明します。

#### データセット内のカウント特徴

この実験の次のモジュールには、事前に構築されたカウント テーブルの使用が含まれています。事前に構築されたカウント テーブルを使用するには、新しい実験の [検索] タブで "cr_count_" を検索すると、2 つのデータセット ("cr_count_cleanednulls_metadata" と "cr_count_table_cleanednulls") が表示されます。これらの両方を右側の実験ウィンドウにドラッグ アンド ドロップします。出力ポートを右クリックすると、視覚化が有効になります。

カウント テーブルのメタデータの視覚化は次のように表示されます。

![カウント テーブルのメタデータ](http://i.imgur.com/A39PIe7.png)

メタデータには、条件付きのカウントを構築した列、構築にディクショナリを使用したかどうか (別の方法は最小カウントのスケッチ)、使用されたハッシュ シード、特徴のハッシュに使用されたハッシュ ビット数、クラス数などの情報が含まれています。

カウント テーブルの視覚化は次のように表示されます。

![カウント テーブル](http://i.imgur.com/NJn1EQO.png)

カウント テーブルには、クラスの条件付きのカウントに関する情報が含まれています。カテゴリ特徴の値は、"ハッシュ値" にあるため、その特徴自体がハッシュされています。

カウント特徴はデータセットにどのように組み込まれいますか。 ここでは、以下に示すカウント **Featurizer** モジュールを使用します。

![カウント Featurizer モジュール](http://i.imgur.com/dnMdrR1.png)

カウント テーブルが構築されたら (ここで、カテゴリ特徴のクラス条件付きのカウントを作成することを忘れないでください)、上記の**カウント Featurizer** モジュールを使用してデータセットにこれらのカウント特徴を構築します。**Featurizer** モジュールを使用すると、カウントする特徴、必要な対数オッズやカウント、他の高度なオプションを選択できます。

#### カウント テーブルを最初からビルドする

"カウント テーブルの簡単な説明" で説明したように、事前に構築されたカウント テーブルを使用する (これは前のセクションで詳しく説明しました) ほかに、ユーザーは自身のカウント テーブルを最初から構築することもできます。

このセクションでは、カウント テーブルを最初から構築する方法を示します。これを行うには、以下に示す**カウント テーブルの構築**モジュールを設定で使用します。

![カウント テーブルの構築モジュール](http://i.imgur.com/r7pP8Qq.png)

このモジュールの設定の後半部分は次のようになります。

![カウント テーブルの構築モジュールの設定](http://i.imgur.com/PvmSh3C.png)


**重要:** クラスターとストレージ アカウントの設定には、自分の関連する値を使用してください。

**[実行]** をクリックすると、選択したクラスターにカウント テーブルを構築できます。出力は、前に示したように、カウント テーブルと関連するメタデータです。テーブルの準備ができたら、ここで実験を構築できます。


### <a name="step3"></a>手順 3: モデルをトレーニングする

これを選ぶには、検索ボックスに「two class boosted」と入力してモジュールをドラッグするだけです。次のように、ブースト デシジョン ツリーの学習者の既定値を使用します。

![BDT 学習者](http://i.imgur.com/dDk0Jtv.png)

ML 実験を行う前に、最後の 3 つのコンポーネントが必要です。

1 つは、モデルのトレーニング モジュールです。この最初のポートは入力として学習者を取り込み、次のポートは学習用のトレーニング データセットを取り込みます。これは以下のように表示されます。次にモジュールで設定する必要があるパラメーターを示します。

![BDT モデルのトレーニング モジュールの接続](http://i.imgur.com/szS2xBb.png)

![BDT モデルのトレーニング モジュールの設定](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> 手順 4: テスト データセットでモデルにスコアを付ける

2 番目のコンポーネントは、テスト データセットにスコアを付ける方法です。これは、**モデルのスコア付け**モジュールを使用して簡単に実行できます。このモジュールは、トレーニング データから学習したモデルとテスト データセットを入力として取り込み、予測します。次のように表示されます。

![スコア BDT モデルの接続](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> 手順 5: モデルを評価する

最後に、モデルのパフォーマンスを見てみましょう。通常、2 つのクラス (二項) 分類の問題の効果的な手段は、AUC です。これを視覚化するには、"モデルのスコア付け" モジュールを "モデルの評価" モジュールに関連付けます。**[モデルの評価]** モジュールで **[視覚化]** クリックすると、次のようなグラフィックが表示されます。

![評価モジュール BDT モデル](http://i.imgur.com/0Tl0cdg.png)

二項 (または 2 つのクラス) 分類の問題で、予測精度の効果的な手段は AUC です。次に、このモデルをテスト データセットで使用した結果を示します。これを確認するには、**[モデルの評価]** モジュールの出力ポートを右クリックし、**[視覚化]** をクリックします。

![視覚化のモデルの評価モジュール](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> 手順 6: Web サービスとしてモデルを発行する
機械学習モデルを Web サービスとして発行できるようにすることが最も重要です。これが完了したら、予測する必要があるデータを使用して Web サービスを呼び出すことができ、モデルは理想的に何らかの予測を返します。

これを行うには、まずトレーニング済みのモデルをトレーニング済みのモデル オブジェクトとして保存します。**[モデルのトレーニング]** モジュールを右クリックし、[トレーニング済みのモデルとして保存する] オプションを使用します。

次に、Web サービス用の入力ポートと出力ポートを作成します (予測に必要なデータと同じ形式でデータを取得する入力ポートと、スコア付け確率と関連付けられた確率を返す出力ポート)。

#### 入力ポートにいくつかのデータ行を選択する

ここでは、入力ポートにいくつかのデータ行を選択する方法を説明します。

![入力ポート データ](http://i.imgur.com/XqVtSxu.png)

Apply SQL 変換を使用して、入力ポート データとして機能するように 10 行のみを選択できます。

#### Web サービス
これで、Web サービスの発行のために使用できる小規模の実験を行う準備ができました。

#### Web サービスの入力データを生成する

手順 0 として、カウント テーブルが大きいため、いくつかの行のテスト データを取り出し、カウント特徴を使用してそれから出力データを生成します。これは、Web サービスの入力データ形式として機能します。これは次のように表示されます。

![BDT 入力データを作成する](http://i.imgur.com/OEJMmst.png)

注: 入力データ形式は、**カウント Featurizer** モジュールの出力を使用できます。この実験が完了したら、**カウント Featurizer** モジュールから出力をデータセットとして保存します。**重要:** このデータセットは、Web サービスの入力データに使用されます。

#### Web サービスの公開のためのスコア付け実験

最初に、次のように表示されます。重要な構造は、**カウント Featurizer** モジュールを使用して、前の手順で生成したトレーニング済みのモデル オブジェクトといくつかの行の入力データを受け入れる、モデルのスコア付けモジュールです。"プロジェクト列" を使用して、スコア付けラベルとスコア付け確率を予測します。

![プロジェクト列](http://i.imgur.com/kRHrIbe.png)

プロジェクト列モジュールを使用して、データセットからデータをフィルター処理する方法を確認できます。以下に内容を説明します。

![プロジェクト列モジュールでのフィルター処理](http://i.imgur.com/oVUJC9K.png)

青色の入力ポートと出力ポートを取得するには、右下にある [prepare webservice] をクリックします。この実験中に、右下にある **[Web サービスの発行]** アイコンをクリックして、Web サービスを公開することもできます。

![Web サービスを発行する](http://i.imgur.com/WO0nens.png)

Web サービスが公開されると、次のページにリダイレクトされます。

![](http://i.imgur.com/YKzxAA5.png)

Web サービス用の 2 つのリンクが左側に表示されます。

* **[要求/応答]** サービス (または RRS) は、1 つの予測向けのサービスで、このワーク ショップで使用されます。 
* **[バッチ実行]** サービス (BES) は、名前が示すとおり、バッチ予測に使用され、Azure BLOB 内にあるデータを予測する必要があります。

**[REQUEST/RESPONSE]** リンクをクリックすると、C#、python、R で作成されたコードを紹介するページに移動します。そのコードを使用して、Web サービスの呼び出しを手間なく行うことができます。このページの API キーを認証に使用する必要があります。

次の python コードを iPython Notebook の新しいセルにコピーすると便利です。

以下に、python コードのセグメントと適切な API キーを示します。

![Python コード](http://i.imgur.com/f8N4L4g.png)

既定の API キーは、Web サービスの API キーに置き換えてあることに注意してください。iPython Notebook でこのセルの実行ボタンをクリックすると、次の応答が返されます。

![iPython の応答](http://i.imgur.com/KSxmia2.png)

質問した 2 つのテスト サンプルに対して (python スクリプトの JSON フレームワークで)、"Scored Labels (スコア付けラベル), Scored Probabilities (スコア付け確率)" の形式で回答が返されました。この場合は、事前に定義されたコード (すべての数値列に 0、すべてのカテゴリ列に文字列 "値") を指定する既定値を選択します。

これで、TB (テラバイト) 規模のデータからクラウド内の Web サービスとしてデプロイされる予測モデルまで、Azure ML を使用したエンド ツー エンドの大規模データセットの処理の説明は終わります。

 

<!---HONumber=58_postMigration-->