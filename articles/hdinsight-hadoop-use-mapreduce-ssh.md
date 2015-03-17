﻿<properties
   pageTitle="HDInsight での MapReduce と Hadoop | Azure"
   description="SSH を使用して HDInsight で Hadoop を使用して MapReduce ジョブを実行する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# SSH による HDInsight での Hive と Hadoop の使用

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、SSH を使用して HDInsight クラスターで Hadoop に接続し、Hadoop コマンドを使用して MapReduce ジョブを送信する方法を説明します。

> [AZURE.NOTE] Linux ベースの Hadoop サーバーに慣れており、HDInsight を初めて使用するユーザーの場合は、「 <a href="../hdinsight-hadoop-linux-information/" target="_blank">What you need to know about Hadoop on Linux-based HDInsight (Linux ベースの HDInsight の Hadoop について知っておくべきこと)</a>」を参照してください。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Linux ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* SSH クライアントssh クライアントを備えた Linux、Unix、および Mac OSWindows ユーザーは、 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a> などのクライアントをダウンロードする必要があります。

## <a id="ssh"></a>SSH を使用した接続

SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。FQDN はクラスターに指定した名前で、その後、**.azurehdinsight.net** が続きます。以下の例では、**myhdinsight** という名前のクラスターに接続します。

	ssh admin@myhdinsight-ssh.azurehdinsight.net

HDInsight クラスターの作成時に **SSH 認証に証明書キーを指定した場合**は、クライアント システムのプライベート キーの場所を指定する必要があることがあります。

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

HDInsight クラスターの作成時に **SSH 認証のパスワードを指定した場合**は、パスワードの入力を求められます。

### Putty (Windows クライアント)

Windows ではビルトイン SSH クライアントは提供されません。**Putty** を使用することをお勧めします。これは、 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a> からダウンロードできます。

Putty の使用の詳細については、「**Putty を使用して Linux マシンに接続する**」セクション (「 <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Azure 上の Linux における SSH の使用方法」) を参照してください</a>。

> [AZURE.NOTE] HDInsight クラスターの SSH 認証で証明書を使用した場合は、「**Create a PPK for Putty (Putty 用の PPK の作成)**」セクション (「 <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Azure 上の Linux における SSH の使用方法」) を参照してください。</a>

## <a id="hadoop"></a>Hadoop コマンドの使用

1. HDInsight クラスターに接続されたら、以下に従って **Hadoop** コマンドを使用して MapReduce ジョブを起動します。

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	これは、**hadoop-mapreduce-examples.jar** ファイルに含まれる **wordcount** クラスを起動します。入力として **wasb://example/data/gutenberg/davinci.txt** ドキュメントを使用し、出力は **wasb:///example/data/WordCountOutput** に格納されます。

	> [AZURE.NOTE] MapReduce ジョブとサンプル データついては、「 <a href="../hdinsight-use-mapreduce/" target="_blank">HDInsight での MapReduce と Hadoop の使用</a>」を参照してください。

2. 処理中に詳細が生成され、ジョブが完了すると、次のような情報が返されます。

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 完了したら、次のコマンドを使用して、**wasb://example/data/WordCountOutput** に格納された出力ファイルを一覧表示します。

		hadoop fs -ls wasb:///example/data/WordCountOutput

	ここでは、**_SUCCESS** および **part-r-00000** の 2 つのファイルが表示されます。**part-r-00000** ファイルには、このジョブの出力が含まれています。

	> [AZURE.NOTE] 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。このとき、ファイルの順番を特定するには ##### サフィックスを使用します。

4. 出力を表示するには、次のコマンドを使用します。

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	**wasb://example/data/gutenberg/davinci.txt** ファイルに含まれる文字の一覧と、各文字の出現回数が表示されます。ファイルに含まれるデータの例を次に示します。

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

## <a id="summary"></a>まとめ

このように、Hadoop を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報

* [Use MapReduce on HDInsight Hadoop (HDInsight Hadoop での MapReduce の使用)](../hdinsight-use-mapreduce/)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

* [HDInsight での Pig と Hadoop の使用](../hdinsight-use-pig/)
<!--HONumber=45--> 