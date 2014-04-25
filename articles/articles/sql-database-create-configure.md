<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="構成方法" pageTitle="SQL データベースの構成方法 - Azure" metaKeywords="Azure 作成 SQL Server, Azure 構成 SQL Server" description="Azure で SQL Server を使用して論理サーバーを作成し構成する方法について説明します。" metaCanonical="" services="sql-database" documentationCenter="" title="SQL データベースを作成し構成する方法" authors="" solutions="" manager="" editor="" />





<h1><a id="configLogical"></a>SQL データベースを作成し構成する方法</h1>

このトピックでは、論理サーバーを作成し構成する手順を説明します。新しい Azure (プレビュー) 管理ポータルの改訂されたワークフローでは、まずデータベースを作成し、次にサーバーを作成します。

ただしこのトピックでは、最初にサーバーを作成します。アップロードする既存の SQL Server データベースがある場合は、この方が便利なことがあります。

##目次##
* [How to: 論理サーバーの作成](#createLogical)
* [How to: 論理サーバーのファイアウォールの構成](#configFWLogical)

<h2><a id="createLogical"></a>How to: 論理サーバーの作成</h2>

1. [管理ポータル](http://manage.windowsazure.com)にサインインします。

2. **[SQL データベース]** をクリックし、SQL データベースのホーム ページで **[サーバー]** をクリックします。

4. ページの下部にある **[追加]** をクリックします。

5. [サーバーの設定] で、空白を含まない 1 語で管理者名を入力します。

	SQL データベースでは、暗号化された接続に対して SQL 認証を使用します。sysadmin 固定のサーバー ロールに割り当てられた新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。

	ログインには、電子メール アドレス、Windows ユーザー アカウント、または Windows Live ID は使用できません。クレーム認証と Windows 認証のどちらも SQL データベースではサポートされません。

6. 大文字、小文字、および数字または記号を組み合わせた 9 文字以上の強力なパスワードを指定します。

7. リージョンを選択します。リージョンによって、サーバーのジオ (主要地域)が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

8. **[Azure サービスにサーバーへのアクセスを許可します]** オプションを選択したままにしてください。そうすれば、SQL データベースの管理ポータル、ストレージ サービス、Azure のその他のサービスを使用してこのデータベースに接続できます。

9. 終了したら、ページの下部にあるチェックマークをクリックします。

サーバー名を指定しなかったことに注意してください。SQL データベースでは、DNS エントリが重複しないようにサーバー名は自動的に生成されます。サーバー名は、10 文字の英数字文字列です。SQL データベース サーバーの名前は変更できません。

次の手順では、ネットワークで実行中のアプリケーションからの接続によるアクセスが許可されるようにファイアウォールを構成します。

<h2><a id="configFWLogical"></a>How to: 論理サーバーのファイアウォールの構成</h2>

1. [管理ポータル](http://manage.windowsazure.com)で **[SQL データベース]**、**[サーバー]** の順にクリックして、作成したサーバーをクリックします。

2. **[構成]** をクリックします。

3. 現在のクライアント IP アドレスをコピーします。ネットワークから接続している場合、これは使用するルーターまたはプロキシ サーバーがリッスンしている IP アドレスです。SQL データベースが現在の接続で使用されている IP アドレスを検出すると、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成できます。

4. 開始範囲と終了範囲の両方に IP アドレスを貼り付けます。その後、この範囲が狭すぎることを示す接続エラーが発生した場合は、このルールを編集して範囲を広げることができます。

	動的に割り当てられた IP アドレスをクライアント コンピューターで使用する場合は、ネットワーク上でコンピューターに割り当てられた IP アドレスを含む範囲を指定する必要があります。狭い範囲から始めて、必要になった場合にのみ範囲を広げます。

5. 使用するコンピューターや会社の名前など、ファイアウォール ルールの名前を入力します。

6. チェックマークをクリックして、ルールを保存します。

7. ページの下部にある **[保存]** をクリックして、手順を完了します。**[保存]** が表示されない場合は、ブラウザーのページを更新します。

これで、論理サーバー、IP アドレスからの受信接続を許可するファイアウォール ルール、および管理者ログインが用意できました。次の手順では、ローカル コンピューターに切り替えて、残りの構成手順を完了します。

**注:** 作成した論理サーバーは一時的で、データ センターの物理サーバー上で動的にホストされます。サーバーを削除する場合は、あらかじめこれは回復不可能な操作であることを知っておく必要があります。後でサーバーにアップロードするデータベースは必ずバックアップしておいてください。
