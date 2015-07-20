<properties 
	pageTitle="Azure AD Connect のカスタム インストール" 
	description="このドキュメントでは、Azure AD Connect のカスタム インストール オプションについて説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect のカスタム インストール


次のドキュメントは、Azure AD Connect のカスタム インストール オプションの使用について説明します。このオプションは、追加の構成オプションがある場合や、高速インストールでは設定されないオプションの機能が必要な場合に設定します。

高速インストールの詳細については、[高速インストール](active-directory-aadconnect-get-started/#express-installation-of-azure-ad-connect)のページを参照してください。DirSync から Azure AD Connect へのアップグレードについては、[DirSync から Azure Active Directory Connect へのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md)に関するページを参照してください。



## 必須コンポーネントのインストール

同期サービスをインストールするとき、オプションの構成セクションをオフのままにすると、Azure AD Connect によってすべて自動的に設定されます。これには、SQL Server 2012 Express のインスタンスの設定、適切なグループの作成、アクセス許可の割り当てが含まれます。既定値を変更する場合、次の表を使用すると、使用できるオプションの構成を理解できます。

<center>![必須コンポーネント](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png) </center>

オプションの構成 | 説明 
------------- | ------------- |
SQL Server 名 |SQL Server 名とインスタンス名を指定することができます。使用する AD データベース サーバーが既にある場合は、このオプションを選択します。
サービス アカウント |既定では Azure AD Connect は、使用する同期サービス用のローカル サービス アカウントを作成します。ここで発生する問題は、パスワードが自動的に生成され、Azure AD Connect をインストールしているユーザーには不明であるということです。ほとんどのシナリオでは、このことは問題ありません。ただし、同期する組織単位のスコープなどの高度な構成を行う場合は、アカウントを作成し、パスワードを選択する必要があります。また、リモート SQL サーバーを使用する場合も、ドメインのサービス アカウントとそのパスワードが必要です。このような場合は、使用するサービス アカウントを入力します。 |
アクセス許可 | 同期サービスがインストールされている場合、既定では、Azure AD Connect によって 4 つのグループがサーバーでローカルに作成されます。これらのグループは。管理者グループ、オペレーター グループ、参照グループ、およびパスワード再設定グループです。独自のグループを指定する場合は、ここから行うことができます。


## ユーザーのサインイン
必須コンポーネントをインストールすると、ユーザーが使用するシングル サインオンの方法を指定するように求められます。次の表に、指定できるオプションの簡単な説明を示します。

<center>![ユーザー サインイン](./media/active-directory-aadconnect-get-started-custom/usersignin.png) </center>


シングル サインオン オプション | 説明 
------------- | ------------- |
パスワードの同期 |ユーザーは、オンプレミスのネットワークへのログインに使用しているものと同じパスワードを使用して、Microsoft クラウド サービス (Office 365、Dynamics CRM、Windows InTune など) にサインインできます。ユーザーのパスワードはパスワードのハッシュを使用して Azure と同期され、クラウドで認証が行われます。
AD FS とのフェデレーション|ユーザーは、オンプレミスのネットワークへのログインに使用しているものと同じパスワードを使用して、Microsoft クラウド サービス (Office 365、Dynamics CRM、Windows InTune など) にサインインできます。ユーザーはサインイン用のオンプレミス AD FS インスタンスにリダイレクトされ、認証はオンプレミスで行われます。
構成しない| 機能はインストールも構成もされません。サード パーティのフェデレーション サーバーまたは別の既存のソリューションが既に設置されている場合は、このオプションを選択します。



## Azure AD に接続
[Azure AD に接続] 画面で、グローバル管理者のアカウントとパスワードを入力します。このアカウントで Multi-Factor Authentication が有効にされていないことを確認します。有効にされていると、認証が失敗します。このアカウントは、Azure AD のサービス アカウントを作成するためにのみ使用され、ウィザードが完了した後は使用されないことに注意してください。

<center>![ユーザー サインイン](./media/active-directory-aadconnect-get-started-custom/connectaad.png) </center>

### ディレクトリの接続
Azure AD Connect では、Active Directory ドメイン サービスに接続するには、十分なアクセス許可を持つアカウントの資格情報が必要です。このアカウントには既定の読み取りアクセス許可が必要なだけなので、通常のユーザー アカウントを指定できます。ただし、シナリオによっては、追加のアクセス許可が必要なことがあります。詳細については [Azure AD Connect アカウントの概要](active-directory-addconnect-account-summary)に関するページを参照してください。

<center>![ユーザー サインイン](./media/active-directory-aadconnect-get-started-custom/connectdir.png) </center>

### ユーザーを一意に識別

[フォレスト全体で一致] 機能を使用すると、AD DS フォレストのユーザーを Azure AD でどう表すかを定義することができます。ユーザーは、すべてのフォレストで 1 回だけ表すか、有効アカウントと無効アカウントを組み合わせることができます。

<center>![ユーザー サインイン](./media/active-directory-aadconnect-get-started-custom/unique.png) </center>

設定 | 説明 
------------- | ------------- |
ユーザーがフォレスト全体で表されるのは 1 度のみです| すべてのユーザーは、Azure AD で個別のオブジェクトとして作成されます。<br> オブジェクトは、メタバースに結合されません。
メール属性 | このオプションは、異なるフォレスト間でメール属性が同じ値である場合に、ユーザーと連絡先を結合します。連絡先が GALSync を使用して作成されているときに、このオプションを使用することをお勧めします。
ObjectSID および msExchangeMasterAccountSID|このオプションは、アカウント フォレストで有効なユーザーと、Exchange リソース フォレストで無効なユーザーを結合します。これは、Exchange のリンクされたメールボックスとして知られています。
sAMAccountName および MailNickName|このオプションは、ユーザーを見つけられるように、ログイン ID が必要な属性同士を結合します。
独自の属性|このオプションでは、独自の属性を選択することができます。**制限:** かならず、メタバース内に既に存在する属性を選択してください。カスタム属性を選択すると、ウィザードは完了できません。

- **ソース アンカー** -sourceAnchor 属性はユーザー オブジェクトの有効期間中に変更できない属性です。オンプレミスのユーザーと Azure AD のユーザーをリンクするプライマリ キーです。この属性は変更できないため、適切な属性を使用するように計画する必要があります。適切な属性として考えられるのは objectGUID です。この属性は、ユーザー アカウントをフォレスト/ドメイン間で移動しなければ、変更されません。フォレスト間でアカウントを移動するマルチ フォレスト環境では、employeeID を持つ属性など、別の属性を使用する必要があります。ユーザーが結婚したり割り当てを変更したりした場合に変更される可能性のある属性は、使用すべきではありません。@ 記号が含まれる属性は使用できないので、電子メールや userPrincipalName は使用できません。この属性では大文字小文字も区別されるため、フォレスト間でオブジェクトを移動する場合は、大文字と小文字を維持してください。バイナリ属性の値は base64 でエンコードされますが、他のタイプの属性はエンコードされていない状態が保たれます。フェデレーション シナリオと一部の Azure AD インターフェイスでは、この属性は immutableID とも呼ばれます。

- **UserPrincipalName** - userPrincipalName 属性は、ユーザーが Azure AD と Office 365 にログインするときに使用する属性です。使用するドメイン (UPN サフィックス) は、ユーザーを同期する前に、Azure AD で検証する必要があります。既定の userPrincipalName 属性のままにしておくことを強くお勧めします。この属性がルーティング不可能で検証できない場合は、ログイン ID を保持する属性として、電子メールなどの別の属性を選択することができます。警告: 代替 ID の使用は、すべての Office 365 ワークロードと互換性があるわけではありません。詳細については、https://technet.microsoft.com/ja-jp/library/dn659436.aspx を参照してください。





### グループに基づく同期フィルタリング
グループのフィルタリングでは、Azure AD と Office 365 でオブジェクトの小さなサブセットのみを作成する小規模なパイロットを実行することができます。この機能を使用するには、Active Directory でグループを作成して、ダイレクト メンバーとして Azure AD と同期するユーザーやグループを追加します。このグループには後でユーザーを追加したりユーザーを削除したりして、Azure AD に表示するオブジェクトの一覧を管理することができます。この機能を使用するには、カスタマイズされたパスで次のようなページが表示されます。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/filter2.png) </center>

### オプション機能

この画面では、特定のシナリオのためにオプション機能を選択することができます。次に、個別の機能のそれぞれについて簡単な説明を示します。

<center>![高速インストール](./media/active-directory-aadconnect-get-started-custom/of.png)</center>


オプション機能 | 説明
-------------------    | ------------- | 
Exchange ハイブリッド展開 |Exchange ハイブリッド展開機能によって、オンプレミスと Azure の両方で、Exchange メールボックスの共存が可能となります。そのためには、Azure AD から特定の属性のセットを、オンプレミスのディレクトリと同期します。
Azure AD アプリと属性フィルター|Azure AD アプリと属性フィルターを有効にして、ウィザードの後続のページで、同期する属性のセットを特定のセットに合わせることができます。このことを行うと、ウィザードで 2 つの追加の構成ページが開きます。  
パスワードの書き戻し|パスワードの書き戻しを有効にすると、Azure AD で発信されるパスワードの変更が、オンプレミスのディレクトリに書き戻されます。
ユーザーの書き戻し|ユーザーの書き戻しを有効にすると、Azure AD に作成されたユーザーが、オンプレミスのディレクトリに書き戻されます。このことを行うと、ウィザードで追加の構成ページが開きます。  
ディレクトリ拡張属性の同期|ディレクトリ拡張属性の同期を有効にすると、指定した属性が Azure AD に同期されます。このことを行うと、ウィザードで追加の構成ページが開きます。  

既定の構成の変更、同期規則エディターと宣言型のプロビジョニングなど追加の構成オプションについては、「[Azure AD Connect の管理](active-directory-aadconnect-whats-next.md)」を参照してください。




ユーザーとグループが含まれているグループの名前を追加します。このグループのメンバーのみが Azure AD に同期されます。

## ディレクトリ拡張機能の属性の同期
ディレクトリ拡張機能では、Active Directory で組織やその他の属性によって追加されたカスタム属性を使用して Azure AD のスキーマを拡張することができます。この機能を使用するには、[オプション機能] ページの [ディレクトリ拡張機能の属性の同期] を選択します。選択すると、追加の属性を選択できる次のページが表示されます。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/extension2.png) </center>

単一値の属性のみがサポートされます。250 文字を超える値は使用できません。選択した属性を使用してメタバースと Azure AD のスキーマが拡張されます。Azure AD では、これらの属性を使用して新しいアプリケーションが追加されます。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/extension3.png) </center>

これらの属性がグラフで使用可能になります。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/extension4.png) </center>

## ユーザーの書き戻し (プレビュー)
ユーザーの書き戻しでは、Azure AD で作成されたユーザーを取得し (ポータル、グラフ、PowerShell、または他の方法で)、そのユーザーをオンプレミスの ADDS に書き戻すことができます。この機能を有効にするには、[オプション機能] ページで [ユーザーの書き戻し] を選択します。これらのユーザーを作成する場所が表示されます。既定の構成では、AD DS 内の 1 つの場所にすべてのユーザーが作成されます。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/writeback2.png) </center>
ユーザーはランダムなパスワードで作成されるため、ユーザーが実際にログインできるようにするには、AD DS でパスワードをリセットする必要があります。

>[AZURE.NOTE]パスワードの同期とパスワードの書き戻しは、このプレビュー機能と互換性はありません。

## グループの書き戻し (プレビュー)
オプション機能のグループの書き戻しのためのオプションでは、"Office 365 のグループ" を Exchange がインストールされているフォレストに書き戻すことができます。これは、クラウドで常に管理される新しいグループの種類です。次のように outlook.office365.com や myapps.microsoft.com で確認することができます。


<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/office365.png) </center>


<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/myapps.png) </center>

このグループは、オンプレミスの AD DS では配布グループとして表現されます。この新しいグループの種類が表示されるようにするには、オンプレミスの Exchange サーバーを Exchange 2013 累積更新プログラム 8 (2015 年 3 月リリース) で更新する必要があります。

**注**

- 現在、アドレス帳の属性は設定されません。最も簡単な方法としては、組織内の別のグループからアドレス帳のプロパティを検索し、同期エンジン以外の場所にこのプロパティを設定します。**これを最も簡単に行うには、PowerShell コマンドレットの更新受信者を使用します。**
- Exchange のスキーマを持つフォレストのみを、グループの有効なターゲットとすることができます。Exchange が見つからない場合は、グループの書き戻しを有効にすることはできません。
- 現在、グループの書き戻し機能では、セキュリティ グループや配布グループは扱えません。

詳細については、[こちら](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/)で確認できます。

## デバイスの書き戻し (プレビュー)
デバイスの書き戻し機能では、クラウドに登録されているデバイス (Intune など) を AD DS に置いて、条件付きアクセスすることができます。この機能を有効にするには、AD DS を準備する必要があります。AD FS とデバイス登録サービス (DRS) をインストールしている場合は、AD でデバイスの書き戻しを準備するための PowerShell コマンドレットが DRS に用意されています。DRS をインストールしていない場合は、C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncAdPrep.psm1 as an enterprise admin を実行できます。



## ステージング モード
ステージング モードでは、新しい同期サーバーをセットアップする処理を既存のサーバーと並行して行うことができます。クラウド内の 1 つのディレクトリに接続する 1 つの同期サーバーのみがサポートされます。ただし、別のサーバー (実行中の 1 つの DirSync など) から移動する場合は、ステージング モードで Azure AD Connect を有効にすることができます。有効にすると、同期エンジンは通常の方法でデータをインポートして同期しますが、Azure AD には何もエクスポートしません。また、パスワードの同期とパスワードの書き戻しはオフになります。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/stagingmode.png) </center>

ステージング モード中は、同期エンジンに必要な変更を加え、エクスポートされる内容を確認することができます。構成が適切な状態になったら、インストール ウィザードをもう一度実行し、ステージング モードを無効にします。ステージング モードを無効にすると、データを Azure AD にエクスポートできるようになります。1 つのサーバーのみをアクティブにしてエクスポートするために、このとき他のサーバーは無効にしてください。

### 誤って削除されないように保護する
Azure AD Connect のインストール中は、誤った削除操作を防止する機能が既定で有効になり、500 個を超える削除を行うエクスポートを許可しないように構成されます。この 500 という値は既定値で、変更できます。この機能が有効なときに、削除の数が多すぎる場合は、エクスポートは続行されず、次のような電子メールが送信されます。

<center>![同期フィルタリング](./media/active-directory-aadconnect-get-started-custom/email.png) </center>

このメールの受信を予想していなかった場合は、調査し、修正のアクションを実行します。

この保護を一時的に無効にし、これらの削除を行うには、Disable-ADSyncExportDeletionThreshold を実行します。

再度保護を有効にする場合、または既定のしきい値の設定を変更するには、Enable-ADSyncExportDeletionThreshold を実行します。


## AD FS とのフェデレーションの構成
Azure AD Connect との AD FS の構成は、わずか数クリックで簡単です。セットアップの前に次が必要です。

- フェデレーション サーバー用の Windows Server 2012 R2 サーバー (リモート管理を有効に設定)
- Web アプリケーション プロキシ サーバー用の Windows Server 2012 R2 サーバー (リモート管理を有効に設定)
- 使用する予定のフェデレーション サービス名の SSL 証明書 (adfs.contoso.com など)

### 新しい AD FS ファームの作成または既存の AD FS ファームの使用
既存の AD FS ファームを使用することも、新しい AD FS ファームを作成することもできます。新しく作成する場合は、SSL 証明書を提供する必要があります。SSL 証明書がパスワードで保護されている場合は、パスワードを入力するように求められます。

<center>![AD FS ファーム](./media/active-directory-aadconnect-get-started-custom/adfs1.png) </center>
**注:** 既存の AD FS ファームを使用する場合は、いくつかのページはスキップし、AD FS と Azure AD の間の信頼関係を構成する画面に直接移動できます。

### AD FS サーバーの指定

ここでは、AD FS をインストールする特定のサーバーを入力します。容量計画のニーズに基づいて 1 つまたは複数のサーバーを追加することができます。追加するすべてのサーバーは、この構成を実行する前に、Active Directory ドメインに参加させる必要があります。テスト デプロイとパイロット デプロイでは AD FS サーバーを 1 つインストールし、スケーリングのニーズに合わせて追加のサーバーをデプロイすることをお勧めします。追加のサーバーをデプロイするには、Azure AD Connect を開き、AD FS を追加のサーバーにデプロイします。


> [AZURE.NOTE]この構成を実行する前に、すべてのサーバーを 1 つの AD ドメインに参加させてください。　

<center>![AD FS サーバー](./media/active-directory-aadconnect-get-started-custom/adfs2.png) </center>
### Web アプリケーション プロキシ サーバーの指定
ここでは、Web アプリケーション プロキシ サーバーとする、特定のサーバーを入力します。Web アプリケーション プロキシ サーバーは DMZ (エクストラネット接続) に展開され、エクストラネットからの認証要求をサポートします。容量計画のニーズに基づいて 1 つまたは複数のサーバーを追加することができます。テスト デプロイとパイロット デプロイでは Web アプロケーション プロキシ サーバーを 1 つインストールし、その後、追加のサーバーをデプロイすることをお勧めします。追加のサーバーをデプロイするには、Azure AD Connect を開き、Web アプリケーション プロキシを追加のサーバーにデプロイします。通常は、イントラネットからの認証を処理するために同数のプロキシ サーバーを設定することをお勧めします。

> [AZURE.NOTE]<li>Azure AD Connect のインストールに使用しているアカウントが AD FS サーバーのローカル管理者ではない場合は、十分なアクセス許可を持つアカウントの資格情報を求められます。</li><li>この手順を構成する前に、Azure AD Connect サーバーと Web アプリケーション プロキシ サーバーの間の HTTP/HTTPS 接続が設定されていることを確認します。</li><li> さらに、認証要求の通過を許可するために、Web アプリケーション サーバーと AD FS サーバーの間の HTTP/HTTPS 接続が設定されていることを確認します。</li>


<center>![Web アプリ](./media/active-directory-aadconnect-get-started-custom/adfs3.png) </center>

Web アプリケーション サーバーが AD FS サーバーとセキュリティで保護された接続を確立できるように、資格情報を入力するよう求められます。これらの資格情報は、AD FS サーバーのローカル管理者である必要があります。

<center>![プロキシ](./media/active-directory-aadconnect-get-started-custom/adfs4.png) </center>
### AD FS サービスのサービス アカウントを指定します。
AD FS サービスには、ユーザーを認証し Active Directory のユーザー情報を参照するドメイン サービス アカウントが必要です。次の 2 種類のサービス アカウントがサポートされます。

- **グループ管理サービス アカウント** - Windows Server 2012 の Active Directory ドメイン サービスで導入されたサービス アカウントの種類。この種類のアカウントは、AD FS のようなサービスを提供します。1 つのアカウントを使用し、アカウントのパスワードを定期的に更新する必要はありません。AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コント ローラーが既にある場合は、このオプションを使用します。
- **ドメイン ユーザー アカウント** -この種類のアカウントはパスワードの入力を求め、パスワードを変更するときに、パスワードを定期的に更新する必要があります。AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コント ローラーがない場合は、このオプションを使用します。

ドメイン管理者としてログインしている場合は、Azure AD Connect によってグループ管理サービス アカウントが自動作成されます。
 
<center>![AD FS サービ アカウント](./media/active-directory-aadconnect-get-started-custom/adfs5.png) </center>

### フェデレーションする Azure AD ドメインの選択
この構成を使用して、AD FS と Azure AD のフェデレーション関係をセットアップします。Azure AD にセキュリティ トークンを発行するように AD FS を構成し、この特定の AD FS インスタンスからのトークンを信頼するように Azure AD を構成します。このページでは、初回は 1 つのドメインしか構成できません。追加のドメインは、Azure AD Connect を再度開きこのタスクを実行することで、いつでも構成できます。

 
<center>![Azure AD ドメイン](./media/active-directory-aadconnect-get-started-custom/adfs6.png) </center>
### フェデレーションの構成を完了するその他のタスクの実行
フェデレーションの構成を完了するには、次のその他のタスクを完了する必要があります。

- イントラネット (内部 DNS サーバー) とエクストラネット (ドメイン レジストラー経由のパブリック DNS) の両方の AD FS フェデレーション サービス名 (adfs.contoso.com など) の DNS レコードを設定します。イントラネットの DNS レコードの場合は、A レコードを使用し、CNAME レコードは使用しないようにします。これは、windows 認証をドメイン参加しているマシンから正常に動作するために必要なことです。
- 1 つ以上の AD FS サーバーまたは Web アプリケーション プロキシ サーバーをデプロイする場合は、かならずロード バランサーを構成し、AD FS フェデレーション サービス名 (adfs.contoso.com など) の DNS レコードでロード バランサーを指定してください。
- イントラネットで Internet Explorer を使用するブラウザー アプリケーションに対して動作する windows 統合認証の場合は、かならず AD FS フェデレーション サービス名 (adfs.contoso.com など) を、IE のイントラネット ゾーンに追加してください。これは、グループ ポリシーを使用して制御し、ドメインに参加しているすべてのコンピューターにデプロイすることができます。 

### フェデレーション構成の確認

[検証] ボタンをクリックすると、Azure AD Connect で DNS 設定が確認されます。

<center>![完了](./media/active-directory-aadconnect-get-started-custom/adfs7.png) </center>
さらに、次の検証手順を実行します。

- イントラネットのドメイン参加しているマシンのインターネット エクスプローラーからブラウザー ログインを検証する: https://myapps.microsoft.com に接続し、ログインしたアカウントでログインできているか確認します。
- エクストラネットの任意のデバイスからブラウザー ログインを検証する: 自宅にあるマシンまたはモバイル デバイスで https://myapps.microsoft.com に接続し、ログイン ID とパスワード資格情報を入力します。
- リッチ クライアントのログインを検証する: https://testconnectivity.microsoft.com に接続し、[Office 365] タブを選択し、[Office 365 シングル サインオン テスト] を選択します。

### AD FS サービスでのオプションの構成
AD FS ログイン ページのイラストとロゴのイメージをカスタマイズできます。それには、AD FS にログインし、PSH を使用して構成します。
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}






**その他のリソース**

* [クラウド内のオンプレミスの ID インフラストラクチャの使用](active-directory-aadconnect.md)
* [Azure AD Connect の動作](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect の次のトピック](active-directory-aadconnect-whats-next.md)
* [詳細情報](active-directory-aadconnect-learn-more.md)
* [MSDN の Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->