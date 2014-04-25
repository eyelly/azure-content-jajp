<properties linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Excel を HDInsight に接続" pageTitle="Hive ODBC ドライバーを使用した Excel から HDInsight への接続 | Azure" metaKeywords="" description="Excel 用の Microsoft Hive ODBC ドライバーを使用できるようにセットアップし、HDInsight クラスターのデータを照会する方法を説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />



#Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続


Microsoft のビッグ データ ソリューションの大きな特長の 1 つは、Azure HDInsight を使用して展開した Apache Hadoop クラスターに Microsoft Business Intelligence (BI) コンポーネントを統合できることです。たとえば、Microsoft Hive Open Database Connectivity (ODBC) ドライバーを使用すれば、HDInsight Hadoop クラスターの Hive データ ウェアハウス フレームワークに Excel を接続できます。

また、Microsoft Power Query for Excel アドインを使用して Excel から HDInsight クラスターや、その他の (HDInsight 以外の) Hadoop クラスターなどのデータ ソースを接続することもできます。Power Query のインストール方法と使用方法については、「[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]」を参照してください。

**前提条件**:

この記事を読み始める前に、次の項目を用意する必要があります。

- HDInsight クラスターが必要です。その構成方法については、「[Azure HDInsight の概要][hdinsight-get-started]」を参照してください。
- Windows 8、Windows 7、Windows Server 2012、Windows Server 2008 R2 のいずれかを実行しているコンピューターが必要です。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、Office 2010 Professional Plus のいずれかが必要です。

##この記事の内容

1. [Microsoft Hive ODBC ドライバーのインストール](#InstallHiveODBCDriver)
2. [Hive ODBC データ ソースの作成](#CreateHiveODBCDataSource)
3. [HDInsight クラスターから Excel へのデータのインポート](#ImportData)
4. [次の手順](#nextsteps)

##<a id="InstallHiveODBCDriver"></a>Microsoft Hive ODBC ドライバーのインストール

Microsoft Hive ODBC ドライバーは、[ダウンロード センター][hive-odbc-driver-download]からダウンロードしてインストールします。

このドライバーは Windows 7、Windows 8、Windows Server 2008 R2、Windows Server 2012 のいずれかの 32 ビットまたは 64 ビット バージョンにインストールすることができ、これによって Azure HDInsight (バージョン 1.6 以降) および Azure HDInsight Emulator (v.1.0.0.0 以降) への接続が許可されます。ODBC ドライバーを使用するアプリケーションのバージョンに合致したバージョンをインストールする必要があります。このチュートリアルでは、Office Excel のドライバーが使用されます。

##<a id="CreateHiveODBCDataSource"></a>Hive ODBC データ ソースの作成

次の手順に従って、Hive ODBC データ ソースを作成します。

1. Windows 8 で、Windows キーを押してスタート画面を開き、**データ ソース**を入力します。
2. 使用している Office のバージョンに応じて、**[ODBC データ ソースのセットアップ (32 ビット)]** または **[ODBC データ ソースのセットアップ (64 ビット)]** をクリックします。Windows 7 を使用している場合は、**[管理ツール]** の **[ODBC データ ソース (32 ビット)]** または **[ODBC データ ソース (64 ビット)]** をクリックします。**[ODBC データ ソース アドミニストレーター]** ダイアログが開きます。
 
	![ODBC データ ソース アドミニストレーター][img-hdi-simbahiveodbc-datasource-admin]

3. [ユーザー DNS] の **[追加]** をクリックすると、**データ ソースの新規作成**ウィザードが開きます。
4. **[Microsoft Hive ODBC ドライバー]** を選択し、**[完了]** をクリックします。**[Microsoft Hive ODBC ドライバーの DNS セットアップ]** ダイアログが開きます。

5. 次の値を入力または選択します。

	<table border="1">
	<tr><td><strong>プロパティ</strong></td><td><strong>説明</strong></td></tr>
	<tr><td>データ ソース名</td><td>データ ソースの名前を入力する</td></tr>
	<tr><td>ホスト</td><td><HDInsightClusterName>「.azurehdinsight.net」と入力します。たとえば、「myHDICluster.azurehdinsight.net」と入力します。</td></tr>
	<tr><td>ポート</td><td><strong>443</strong> を使用します。(このポートは 563 から 443 に変更されました。)</td></tr>
	<tr><td>データベース</td><td><strong>既定</strong>のデータベースを使用します。</td></tr>
	<tr><td>Hive サーバーの種類</td><td><strong>Hive Server 2</strong> を選択</td></tr>
	<tr><td>メカニズム</td><td><strong>Azure HDInsight サービス</strong>を選択</td></tr>
	<tr><td>HTTP パス</td><td>空白のままにします。</td></tr>
	<tr><td>ユーザー名</td><td>HDInsight クラスター ユーザーのユーザー名を入力します。これは、クラスターのプロビジョニング処理中に作成されるユーザー名です。簡易作成オプションを使用する場合、既定のユーザー名は "<strong>admin</strong>" です。</td></tr>
	<tr><td>パスワード</td><td>HDInsight クラスターのユーザー パスワードを入力します。</td></tr>
	</table>

	**[詳細オプション]** をクリックするときに、注意する必要のある重要なパラメーターがいくつかあります。

	<table border="1">
	<tr><td>ネイティブ クエリの使用</td><td>これを選択すると、ODBC ドライバーは TSQL を HiveQL に変換しません。純粋な HiveQL ステートメントを送信していることを確認している場合にのみ使用します。SQL Server または Azure SQL データベースに接続している場合は、オフのままにします。</td></tr>
	<tr><td>ブロック単位でフェッチされた行</td><td>大量のレコードをフェッチする場合、このパラメーターを調整してパフォーマンスを最適化する必要がある場合があります。</td></tr>
	<tr><td>既定の文字列の列の長さ、<br/>
			バイナリ列の長さ、<br/>
			10 進数の列の桁数</td><td>データ型の長さおよび精度がデータが返される方法に影響する可能性があります。精度が失われるまたは切り捨てられるために間違った情報が返されます。</td></tr>
	</table>

	![詳細オプション][img-HiveOdbc-DataSource-AdvancedOptions]

6. **[テスト]** をクリックして、データ ソースをテストします。データ ソースが正しく構成された場合、*テストは無事に完了しました。*と表示されます。
7. **[OK]** をクリックして [テスト] ダイアログを閉じます。これで新しいデータ ソースが **[ODBC データ ソース アドミニストレーター]** ダイアログに表示されます。
8. **[OK]** をクリックしてウィザードを終了します。
	
##<a id="ImportData"></a>HDInsight クラスターから Excel へのデータのインポート

ここでは、上記の手順で作成した ODBC データ ソースを使用して、Hive テーブルから Excel ブックへデータをインポートする方法を説明します。

1. Excel で新しいブックまたは既存のブックを開きます。
2. **[データ]** タブから **[外部データの取得]** タイルをクリックし、**[その他のデータ ソース]**、**[データ接続ウィザード]** の順にクリックして、**データ接続ウィザード**を開きます。

	![データ接続ウィザードを開く][img-hdi-simbahiveodbc.excel.dataconnection]

3. データ ソースとして **[ODBC DSN]** を選択して、**[次へ]** をクリックします。
4. ODBC データソースから、前の手順で作成したデータ ソース名を選択して、**[次へ]** をクリックします。
5. ウィザードで、クラスターのパスワードを再入力し、**[テスト]** をクリックして構成を確認します。
6. **[OK]** をクリックして、テスト ダイアログを閉じます。
7. **[OK]** をクリックします。**[データベースとテーブルの選択]** ダイアログが開くのを待ちます。この処理には数秒かかります。
8. インポートするテーブルを選択し、**[次へ]** をクリックします。*hivesampletable* は HDInsight クラスターに付属する Hive テーブルのサンプルです。作成していない場合は、ここで選択できます。Hive クエリの実行および Hive テーブルの作成の詳細については、「[HDInsight での Hive の使用][hdinsight-hive]」を参照してください。
8. **[完了]** をクリックします。
9. **[データのインポート]** ダイアログでは、クエリを変更または指定できます。これを行うには、**[プロパティ]** をクリックします。この処理には数秒かかります。
10. **[定義]** タブをクリックして、**[コマンド文字列]** ボックスの Hive select ステートメントに「**LIMIT 200**」を追加します。変更によって、返されるレコード セットが 200 に制限されます。

	![接続のプロパティ][img-hdi-simbahiveodbc-excel-connectionproperties]

11. **[OK]** をクリックして [接続プロパティ] ダイアログを閉じます。
12. **[OK]** をクリックして **[データのインポート]** ダイアログを閉じます。
13. パスワードを再入力して **[OK]** をクリックします。データが Excel にインポートされるまでに、数秒かかります。

##<a id="nextsteps"></a>次のステップ

この記事では、Microsoft Hive ODBC ドライバーを使用して HDInsight サービスから Excel にデータを取得する方法を学習しました。同様に、SQL データベースに HDInsight サービスからデータを取得することもできます。また、HDInsight サービスにデータをアップロードすることもできます。詳細については、次を参照してください。

- [HDInsight を使用したフライトの遅延データの分析][hdinsight-analyze-flight-delay-data]
- [データを HDInsight へアップロードする方法][hdinsight-upload-data]
- [HDInsight での Sqoop の使用][hdinsight-sqoop]


[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-analyze-flight-delay-data]: /ja-jp/documentation/articles/hdinsight-analyze-flight-delay-data/
[hdinsight-hive]: /ja-jp/documentation/articles/hdinsight-use-hive/
[hdinsight-upload-data]: /ja-jp/documentation/articles/hdinsight-upload-data/
[hdinsight-power-query]: /ja-jp/documentation/articles/hdinsight-connect-excel-power-query/
[hdinsight-get-started]: /ja-jp/documentation/articles/hdinsight-get-started/

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 
