<properties 
	pageTitle="BizTalk サービス:バックアップと復元 | Azure" 
	description="BizTalk Services には、バックアップと復元の機能が備わっています。バックアップを作成して復元する方法を説明すると共に、何がバックアップされるのかを確認します。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>


# BizTalk サービス: バックアップと復元

Azure BizTalk サービスには、バックアップ機能と復元機能が備わっています。このトピックでは、Azure の管理ポータルを使用して BizTalk Services をバックアップおよび復元する方法を次の各項に分けて説明します。 

- [開始する前に](#beforebackup)
- [バックアップの作成](#createbu)
- [バックアップの復元](#restore)
- [バックアップ対象](#budata)

BizTalk Services のバックアップは、[BizTalk Services の REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584) を使用して行うこともできます。 

##<a name="beforebackup"></a>開始する前に

- バックアップと復元は、どのエディションでもできるとは限りません。「[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://azure.microsoft.com/documentation/articles/biztalk-editions-feature-chart/)」を参照してください。

	**注** ハイブリッド接続は、エディションにかかわらずバックアップされません。

- Azure 管理ポータルを使用して、オンデマンド バックアップを作成することも、スケジュールされたバックアップを作成することもできます。 

- バックアップ コンテンツは、同じ BizTalk サービスまたは新しい BizTalk サービスに復元できます。同じ名前を使用して BizTalk サービスを復元するには、既存の BizTalk サービスを削除する必要があります。さらにその名前が使用可能である必要があります。BizTalk サービスを削除した後、同じ名前が使用可能になるまで長い時間がかかる場合があります。同じ名前が使用可能になるまで待つことができない場合は、新しい BizTalk サービスに復元します。

- BizTalk サービスは、同じエディション以上に復元できます。バックアップを実行したときよりも下位のエディションへの BizTalk Services の復元はサポートされていません。

	 たとえば、基本エディションを使用したバックアップはプレミアム エディションに復元できます。プレミアム エディションを使用したバックアップを標準エディションに復元することはできません。

- コントロール番号の連続性を維持するため、EDI コントロール番号がバックアップされます。前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

- バッチの中にアクティブなメッセージがある場合は、バックアップを実行する**前に**そのバッチを処理してください。バックアップを作成するときに (オンデマンドかスケジュールされたものかにかかわらず)、バッチの中のメッセージが保存されることはありません。 

	**アクティブなメッセージがバッチになっている状態で、それらに対応するバックアップを取得しようとしても、それらのメッセージはバックアップされないため、失われます。**

- 省略可能: BizTalk サービス ポータルで、管理操作をすべて停止します。


##<a name="createbu"></a>バックアップの作成

バックアップはいつでも取得でき、完全にユーザーによって制御されます。このセクションでは、Azure の管理ポータルを使用してバックアップを作成する手順を次の各項に分けて説明します。

[オンデマンド バックアップ](#backupnow)

[バックアップのスケジュール](#backupschedule)

####<a name="backupnow"></a>オンデマンド バックアップ
1. Azure の管理ポータルで、[**BizTalk サービス**] を選択してから、どの BizTalk サービスをバックアップするかを選択します。
2. [**ダッシュボード**] タブで、ページの一番下にある [**バックアップ**] を選択します。
3. バックアップ名を入力します。たとえば、「*myBizTalkService*BU*日付*」と入力します。
4. BLOB ストレージ アカウントを選択し、チェックマークを選択してバックアップを開始します。

バックアップが完了すると、入力したバックアップ名が付けられたコンテナーがストレージ アカウントの下に作成されます。このコンテナーには、BizTalk サービスのバックアップ構成が含まれています。

####<a name="backupschedule"></a>バックアップのスケジュール

1. Azure の管理ポータルで、[**BizTalk サービス**] を選択し、どの BizTalk サービスのバックアップをスケジュールするかを選択してから、[**構成**] タブを選択します。
2. [**バックアップ ステータス**] を [**自動**] に設定します。 
3. [**ストレージ アカウント**] で、どのアカウントにバックアップを保存するかを選択し、[**頻度**] にバックアップ作成の間隔を入力し、バックアップを保持する期間の長さを [**保持日数**] に入力します。

	![][AutomaticBU]

	**メモ** 	
- [**保持日数**] に入力する期間の長さは、バックアップ頻度よりも大きくする必要があります。
- [**常に 1 つ以上のバックアップを保存してください**] を選択します。保持期間が過ぎてもバックアップが 1 つは存在するようにするためです。
	

4. [**保存**] を選択します。


スケジュールされたバックアップ ジョブを実行すると、指定したストレージ アカウント内に、(バックアップ データを格納するための) コンテナーが作成されます。コンテナーの名前は、"*BizTalk サービス名-日付-時刻*" となります。 

BizTalk サービスのダッシュボードにステータスが [**失敗**] と表示される場合:

![Last scheduled backup status][BackupStatus] 

このリンクをクリックすると、トラブルシューティングに役立つ管理サービス操作ログのページが開きます。「[BizTalk Services: Troubleshoot using operation logs (BizTalk Services: 操作ログを使用したトラブルシューティング)](http://go.microsoft.com/fwlink/?LinkId=391211)」を参照してください。

##<a name="restore"></a>復元

バックアップの復元は、Azure の管理ポータルからでも、[BizTalk サービスの REST API](http://go.microsoft.com/fwlink/p/?LinkID=325582) からでも実行できます。このセクションでは、管理ポータルを使用して復元する手順を説明します。

####バックアップを復元する前に

- BizTalk サービスを復元するときに、トラッキング、アーカイブ、および監視のための新しい保存場所を指定できます。

- 同じ EDI ランタイム データが復元されます。EDI ランタイム バックアップでは、コントロール番号が保存されます。復元されるコントロール番号は、バックアップの時点から順番に付けられます。前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

####バックアップの復元

1. Azure の管理ポータルで、[**新規**]、[**アプリケーション サービス**]、[**BizTalk サービス**]、[**復元**] の順に選択します。

	![Restore a backup][Restore]

2. [**バックアップ URL**] で、フォルダーのアイコンを選択し、BizTalk サービス構成バックアップが保存されている Azure ストレージ アカウントを展開します。コンテナーを展開し、右のウィンドウで対応するバックアップの .txt ファイルを選択します。 
<br/><br/>
[**開く**] を選択します。

3. [**BizTalk サービスの復元**] ページで、[**BizTalk サービス名**] に名前を入力し、復元される BizTalk サービスの [**ドメイン URL**]、[**エディション**]、[**リージョン**] を確認します。[トラッキング データベース] では [**新しい SQL データベース インスタンスを作成する**] を選択します。

	![][RestoreBizTalkService]


	次へ進む矢印を選択します。

4. 	SQL データベースの名前を確認し、SQL データベースの作成場所となる物理サーバーを入力し、そのサーバーのユーザー名とパスワードを入力します。


	SQL データベースのエディションやサイズなどのプロパティを構成する場合は、[**データベースの詳細設定を構成します**] チェック ボックスをオンにします。 

	次へ進む矢印を選択します。

5. 新しいストレージ アカウントを作成するか、BizTalk サービス用の既存のストレージ アカウントを指定します。

7. チェックマークをクリックして復元を開始します。

復元が正常に完了すると、新しい BizTalk サービスが Azure 管理ポータルの [BizTalk サービス] ページに "中断" という状態で表示されます。



###<a name="postrestore"></a>バックアップの復元後

BizTalk サービスは常に、[**中断**] 状態で復元されます。この状態のときは、新しい環境を機能させる前に次のような構成変更を行うことができます。

- Azure BizTalk サービス SDK を使用して BizTalk サービス アプリケーションを作成した場合は、そのアプリケーション内の ACS 資格情報が復元後の環境で動作するように更新することが必要になる可能性があります。

- 既存の BizTalk サービス環境の複製を目的として BizTalk サービスを復元します。これに該当するときに、元の BizTalk サービス ポータルでソース FTP フォルダーを使用するように契約が構成されている場合は、新たに復元された環境で別のソース FTP フォルダーを使用するように契約を更新することが必要になる可能性があります。更新しなかった場合は、2 つの異なる契約が同じメッセージを取り出そうとする可能性があります。

- 復元操作の結果として複数の BizTalk サービス環境が存在することになった場合は、Visual Studio アプリケーション、PowerShell コマンドレット、REST API、取引先管理オブジェクト モデル (TPM OM) API でのターゲットとして必ず正しい環境を指定してください。

- 新しく復元した BizTalk サービス環境で自動バックアップを構成することをお勧めします。

Azure の管理ポータルで BizTalk サービスを開始するには、復元された BizTalk サービスを選択してタスク バーの [**再開**] をクリックします。 



##<a name="budata"></a>バックアップ対象

バックアップが作成されるとき、次の項目がバックアップされます。

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>バックアップされる項目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk サービス ポータル</strong></td>
</tr> 
<tr>
<td>構成とランタイム</td> 
<td>
<ul>
<li>パートナーとプロファイルの詳細</li>
<li>パートナー契約</li>
<li>デプロイされているカスタム アセンブリ</li>
<li>デプロイされているブリッジ</li>
<li>証明書</li>
<li>デプロイされている変換</li>
<li>パイプライン</li>
<li>BizTalk サービス ポータルで作成および保存されたテンプレート</li>
<li>X12 ST01 および GS01 マッピング</li>
<li>コントロール番号 (EDI)</li>
<li>AS2 メッセージ MIC 値</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk サービス</strong></td>
</tr> 
<tr>
<td>SSL 証明書</td> 
<td>
<ul>
<li>SSL 証明書データ</li>
<li>SSL 証明書のパスワード</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk サービスの設定</td> 
<td>
<ul>
<li>スケール ユニット数</li>
<li>エディション</li>
<li>製品バージョン</li>
<li>リージョン/データセンター</li>
<li>Access Control サービス (ACS) の名前空間とキー</li>
<li>トラッキング データベースの接続文字列</li>
<li>アーカイブ ストレージ アカウントの接続文字列</li>
<li>監視ストレージ アカウントの接続文字列</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>その他の項目</strong></td>
</tr> 
<tr>
<td>トラッキング データベース</td> 
<td>BizTalk サービスを作成するときに、トラッキング データベースの詳細 (Azure SQL データベース サーバーやトラッキング データベース名など) が入力されます。トラッキング データベースは自動的にはバックアップされません。
<br/><br/>
<strong>重要</strong><br/>
トラッキング データベースが削除されてデータベースの復旧が必要である場合は、以前のバックアップが存在している必要があります。バックアップが存在しない場合、トラッキング データベースとそのデータは復旧できません。このような状況では、同じデータベース名の新しいトラッキング データベースを作成します。geo レプリケーションが推奨されます。</td>
</tr> 
</table>

## 次へ

Azure BizTalk Services を Azure の管理ポータルで作成するには、「[Create a BizTalk Service using Azure management portal (Azure の管理ポータルを使用した BizTalk サービスの作成)](http://go.microsoft.com/fwlink/p/?LinkID=302280)」を参照してください。アプリケーションの作成を開始するには、「[BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)」を参照してください。

## 関連項目
- [BizTalk サービスのバックアップに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [バックアップからの BizTalk サービスの復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk サービス: プロビジョニングの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk サービス: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

<!--HONumber=46--> 
 