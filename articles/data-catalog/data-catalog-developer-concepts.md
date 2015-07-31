<properties
   pageTitle="Azure Data Catalog 開発者の概念"
   description="Azure Data Catalog での開発者の概念: カタログ、ユーザー、アセット、クラウドソーシング。"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/> <tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Azure Data Catalog 開発者の概念

Microsoft **Azure Data Catalog** は、データ ソース検出およびデータ ソース メタデータのクラウド ソーシングの機能を提供する、完全に管理されたクラウド サービスです。開発者は、REST API を介してサービスを使用できます。開発者が **Azure Data Catalog** を使いこなすには、サービスに実装されている概念を理解することが重要です。

## 主要な概念

**Azure Data Catalog** の概念モデルは、**カタログ**、**ユーザー**、**アセット**、**注釈**という 4 つの主要な概念に基づいています。
     
![概念][1]

*図 1 - Azure Data Catalog の簡略化された概念モデル*

### カタログ

**カタログ**は、組織が格納するすべてのメタデータの最上位レベルのコンテナーです。Azure アカウントごとに許容される**カタログ**1 つだけです。カタログは Azure サブスクリプションに関連付けられますが、アカウントに複数のサブスクリプションが含まれる場合でも、所定の Azure アカウントに対して作成できる**カタログ**は 1 つだけです。

カタログには、**ユーザー**と**アセット**が含まれています。

### ユーザー

ユーザーは、カタログで操作 (カタログの検索、項目の追加、編集、削除など) を実行する権限を持つセキュリティ プリンシパルです。

ユーザーが担うことができるロールには、さまざまなものがあります。ロールの詳細については、ロールと承認に関するセクションを参照してください。

追加できるのは個々のユーザー (セキュリティ グループではなく) だけです。

Azure Data Catalog では、ID およびアクセス管理のために Azure Active Directory が使用されます。各カタログのユーザーは、アカウントの Active Directory のメンバーである必要があります。

### アセット

**カタログ**にはデータ アセットが含まれています。**アセット**は、カタログによって管理される項目の単位です。

アセットの粒度は、データ ソースによって異なります。SQL Server または Oracle データベースのアセットには、テーブルまたはビューを指定できます。SQL Server Analysis Services のアセットには、メジャー、ディメンション、または主要業績評価指標 (KPI) を指定できます。SQL Server Reporting Services のアセットには、レポートを指定できます。

**アセット**は、カタログに追加またはカタログから削除する項目です。これは、**検索**で取得する結果の単位です。

**アセット**は、コアの情報およびその場所と種類のほか、詳細に説明する注釈で構成されています。

### 注釈

注釈は、アセットに関するメタデータを表す項目です。

注釈の例としては、説明、タグ、スキーマ、ドキュメントなどがあります。アセットの型と注釈の型の完全な一覧が、「アセット オブジェクト モデル」セクションに示されています。

## 注釈のクラウド ソーシングとユーザーの観点 (意見の多重度)

Azure Data Catalog の重要な側面は、システム内のメタデータのクラウドソーシングをどのようにサポートするかということです。1 つの意見しか存在せず、最後の書き込みを優先する wiki アプローチとは異なり、Azure Data Catalog モデルでは、システム内に複数の意見が共存できます。

このアプローチは、所定のアセットに対してさまざまなユーザーがさまざまな観点を持ちうるという、企業データの現実世界を反映しています。

-	データベース管理者は、サービス レベル アグリーメント、または ETL の一括操作に使用可能な処理ウィンドウに関する情報を提供する場合があります
-	データ スチュワードは、アセットが適用されるビジネス プロセス、またはビジネスでアセットに適用した分類に関する情報を提供する場合があります
-	財務アナリストは、期末レポート タスク中のデータの使用方法に関する情報を提供する場合があります

この例をサポートするために、DBA、データ スチュワード、アナリストの各ユーザーは、カタログに登録されている単一のテーブルに説明を追加できます。システムですべての説明が管理され、Azure Data Catalog ポータルにすべての説明が表示されます。

このパターンは、オブジェクト モデル内のほとんどの項目に適用されます。JSON ペイロードのオブジェクト型が、多くの場合、シングルトンが想定されるプロパティの配列であるのはこのためです。
 
たとえば、アセット下で、ルートは説明オブジェクトの配列です。配列プロパティは、“descriptions” という名前です。説明オブジェクトには、説明、タグ、friendlyName の 3 つのプロパティがあります。パターンとしては、これらのプロパティの 1 つ以上を入力する各ユーザーが、ユーザーが指定した値に対して作成された説明オブジェクトを取得します。

その後、UX は組み合わせの表示方法を選択できます。表示には 3 つの異なるパターンがあります。

-	最も単純なパターンは、「すべて表示」です。このパターンでは、すべてのオブジェクトが何らかのリスト ビューに表示されます。これは、Azure Data Catalog ポータル UX が説明に対して行う処理です。 
-	もう 1 つのパターンは、「統合」です。このパターンでは、さまざまなユーザーからのすべての値がマージされ、重複部分が削除されます。Azure Data Catalog ポータル UX でのこのパターンの例には、タグやエキスパート プロパティがあります。 
-	3 つ目のパターンは、「最後の書き込みを優先」です。このパターンでは、最後に入力された最新の値のみが表示されます。このパターンの例には、friendlyName があります。型によっては複数のインスタンスを使用できないものもあります。 

複数のインスタンスを使用できる型と使用できない型の一覧を次に示します。

複数のインスタンスを使用できる型は、次のとおりです。

- 説明
- エキスパート
- スキーマの説明

複数のインスタンスを使用**できない**型は、次のとおりです。

- ルート型
- スキーマ
- 更新

## アセット オブジェクト モデル

「主要な概念」セクションで説明したように、**Azure Data Catalog** オブジェクト モデルには、アセットまたは注釈を指定できる項目が含まれます。項目には、省略可能または必須のプロパティがあります。一部のプロパティは、すべての項目に適用されます。一部のプロパティは、すべてのアセットに適用されます。一部のプロパティは、特定のアセットの型のみに適用されます。

### 共通のプロパティ

これらのプロパティは、すべてのルート アセットの型とすべての注釈の型に適用されます。

> [AZURE.NOTE]名前がダブル アンダースコアで始まるプロパティは、システムの型です。

<table><tr><td><b>プロパティ名</b></td><td><b>データ型</b></td><td><b>説明</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>ルートが最後に変更された日時。これは、クライアントによって設定されます (この値はサーバーでは管理されません)。</td></tr><tr><td>__id</td><td>Guid</td><td>項目の ID (読み取り専用)。この ID は、アセットに対して一意であることが保証されています。したがって、項目のキーは __Id、ルートの __id ですこの組は、ディレクトリ内でのみ一意であることが保証されています。</td></tr><tr><td>__typeId</td><td>Guid</td><td>アセットの型 (読み取り専用)。</td></tr><tr><td>__creatorId</td><td>String</td><td>アセットを一意に識別するためにアセットの作成者によって使用される文字列。</td></tr></table>

### 共通のルート プロパティ

これらのプロパティは、すべてのルート アセットの型に適用されます。

<table><tr><td><b>プロパティ名</b></td><td><b>データ型</b></td><td><b>説明</b></td></tr><tr><td>name</td><td>String</td><td>データ ソースの場所の情報から派生した名前。</td></tr><tr><td>dsl</td><td>Data Source Location</td><td>データ ソースを一意に説明するもので、アセットの識別子の 1 つです (デュアル ID のセクションを参照してください)。dsl の構造は、ソースの種類によって異なります。</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>アセットの型の詳細な説明。</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>このアセットを最後に登録したユーザーを説明します。ユーザーの一意の ID (upn) と、表示名 (lastName および firstName) の両方が含まれています。</td></tr><tr><td>lastRegisteredTime</td><td>dateTime</td><td>このアセットが最後にカタログに登録された日時。</td></tr></table>

### ルート アセットの型

ルート アセットの型は、カタログに登録できるデータ アセットのさまざまな種類を表す型です。

<table><tr><td><b>アセットの型</b></td><td><b>追加のプロパティ</b></td><td><b>データ型</b></td><td><b>説明</b></td></tr><tr><td>テーブル</td><td></td><td></td><td>テーブルは、表形式のデータを表します。これには、SQL テーブル、SQL ビュー、Analysis Services 表形式テーブル、Analysis Services 多次元ディメンション、Oracle テーブルなどがあります。   </td></tr><tr><td>Measure</td><td></td><td></td><td>この型は、Analysis Services のメジャーを表します。</td></tr><tr><td></td><td>Measure</td><td>分割</td><td>メジャーを説明するメタデータ。</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td>メジャーが計算されるかどうかを指定します。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>メジャーの物理的なコンテナー。</td></tr><tr><td>KPI</td><td></td><td></td><td>この型は、Analysis Services の主要業績評価指標を表します。</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td>KPI の対象の値を返す MDX 数値式または計算。</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td>KPI の実際の値を返す MDX 数値式。</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td>指定された時点での KPI の状態を表す MDX 式。</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td>時間ごとに KPI の値を評価する MDX 式。トレンドには、特定のビジネス コンテキストで役立つ、時間ベースの任意の条件を指定できます。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td>メジャーの物理的なコンテナー。</td></tr><tr><td>レポート</td><td></td><td></td><td>この型は、SQL Server Reporting Services のレポートを表します。 </td></tr><tr><td></td><td>CreatedBy</td><td>String</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>String</td><td></td></tr></table>

### 注釈の型

注釈の型は、カタログ内で他の型に割り当てることのできるメタデータの種類を表します。

<table><tr><td><b>注釈の型</b></td><td><b>追加のプロパティ</b></td><td><b>データ型</b></td><td><b>説明</b></td></tr><tr><td>説明</td><td></td><td></td><td>システムの各ユーザーは、独自の説明とタグを追加できます。そのユーザーだけが説明オブジェクトを編集できます (管理者およびアセットの所有者は、説明オブジェクトを削除できますが、編集することはできません)。システムで、これらは別々に管理されます。したがって、アセットごとに説明の配列が存在します (場合により、データ ソースから派生した情報を含む説明。加えて、アセットに関する知識を提供した各ユーザーの説明)。</td></tr><tr><td></td><td>friendlyName</td><td>string</td><td>データ ソースから派生した名前の代わりに使用できるフレンドリ名。これは、表示と検索を行う場合に有用です。</td></tr><tr><td></td><td>tags</td><td>string[]</td><td>アセットのタグの配列。</td></tr><tr><td></td><td>description</td><td>string</td><td>アセットの簡単な説明 (2 ～ 3 行)。</td></tr><tr><td>スキーマ</td><td></td><td></td><td>スキーマは、データの構造を説明します。属性 (列、属性、フィールドなど) の名前と型、およびその他のメタデータを一覧表示します。この情報はすべて、データ ソースから派生します。通常、アセットには 1 つのスキーマ項目があります。</td></tr><tr><td></td><td>columns</td><td>Column[]</td><td>列オブジェクトの配列。データ ソースから派生した情報を含む列を説明します。</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>スキーマで定義されている各属性の説明とタグ セットが含まれています。システムの各ユーザーは、独自の説明とタグを追加できます。そのユーザーだけが説明オブジェクトを編集できます (管理者およびアセットの所有者は、SchemaDescription オブジェクトを削除できますが、編集することはできません)。システムで、これらは別々に管理されます。したがって、アセットごとに SchemaDescription オブジェクトの配列が存在します (場合により、データ ソースから派生した情報を含む説明。加えて、属性に関する知識を提供した各ユーザーの説明)。SchemaAttributes はスキーマに疎結合されているため、同期しなくなる可能性があります。つまり、SchemaDescription は、スキーマに存在しない列を説明する場合や、最近追加された新しい列を参照できない場合があります。これらの同期を保つのは、ライターの責任です。データ ソースには、説明情報も含まれる場合があります。これは、ツールを実行するときに作成される追加の schemaDescription オブジェクトです。</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>スキーマ内の列を説明する ColumnDescriptions の配列です。</td></tr><tr><td>エキスパート</td><td></td><td></td><td>データ セット内でエキスパートと見なされるユーザーの一覧が含まれています。説明の一覧を表示すると、エキスパートの意見 (説明) が UX の一番上に表示されます。各ユーザーは、エキスパートの独自の一覧を指定できます。そのユーザーだけがエキスパート オブジェクトを編集できます (管理者およびアセットの所有者は、エキスパート オブジェクトを削除できますが、編集することはできません)。</td></tr><tr><td></td><td>experts</td><td>string[]</td><td>電子メール アドレスの配列。</td></tr><tr><td>更新</td><td></td><td></td><td>プレビューには、アセットのデータの上位 20 行のスナップショットが含まれています。プレビューは、一部のアセットの型に対してのみ意味を持ちます (テーブルに対しては意味を持ちますが、メジャーに対しては意味を持ちません)。</td></tr><tr><td></td><td>プレビュー</td><td>object[]</td><td>列を表すオブジェクトの配列。各オブジェクトには、列へのプロパティ マッピングが、行に対するその列の値と共に含まれます。</td></tr></table>

### 共通の型

共通の型は、プロパティの型として使用できますが、項目ではありません。

<table><tr><td><b>共通の型</b></td><td><b>プロパティ</b></td><td><b>データ型</b></td><td><b>説明</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>string</td><td>SQL Server、Oracle データベースなど、データ ソースの種類を説明します。  </td></tr><tr><td></td><td>ObjectType</td><td>string</td><td>SQL Server のテーブルやビューなど、データ ソース内のオブジェクトの種類を説明します。</td></tr><tr><td></td><td>formatType</td><td>string</td><td>データの構造について説明します。現在の値は、構造化済みであるか、または構造化されていません。</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>string</td><td>ユーザーの一意の電子メール アドレス。</td></tr><tr><td></td><td>firstName</td><td>string</td><td>ユーザーの名前 (表示用)。</td></tr><tr><td></td><td>lastName</td><td>string</td><td>ユーザーの姓 (表示用)。</td></tr><tr><td>分割</td><td></td><td></td><td></td></tr><tr><td></td><td>name</td><td>string</td><td>列または属性の名前。</td></tr><tr><td></td><td>type</td><td>string</td><td>列または属性のデータ型。使用可能な型は、アセットのデータ ソースの種類によって異なります。型のサブセットのみがサポートされます。</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>列または属性に使用できる最大長。データ ソースから派生します。一部のソースの種類のみに適用されます。</td></tr><tr><td></td><td>Precision</td><td>byte</td><td>列または属性の有効桁数。データ ソースから派生します。一部のソースの種類のみに適用されます。</td></tr><tr><td></td><td>isNullable</td><td>Boolean</td><td>列が null 値を含むことができるかどうか。データ ソースから派生します。一部のソースの種類のみに適用されます。</td></tr><tr><td></td><td>expression</td><td>string</td><td>値が計算列である場合、このフィールドには値を表す式が含まれています。データ ソースから派生します。一部のソースの種類のみに適用されます。</td></tr><tr><td></td><td>defaultValue</td><td>オブジェクト</td><td>オブジェクトに対して insert ステートメントで指定されていない場合に挿入される既定値。データ ソースから派生します。一部のソースの種類のみに適用されます。</td></tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr><tr><td></td><td>tags</td><td>string[]</td><td>列を説明するタグの配列。</td></tr><tr><td></td><td>description</td><td>string</td><td>列を説明する説明。</td></tr><tr><td></td><td>columnName</td><td>string</td><td>この情報が参照する列の名前。</td></tr></table>

## ロールと承認

Microsoft Azure Data Catalog は、アセットと注釈の CRUD 操作に対する承認機能を提供します。

## 主要な概念

Azure Data Catalog では、次の 2 つの承認機構が使用されます。

- ロール ベースの承認
- アクセス許可ベースの承認

### ロール

**管理者**、**所有者**、**共同作成者** の 3 つのロールがあります。各ロールは、次の表に示すスコープと権限を持ちます。

<table><tr><td><b>役割</b></td><td><b>スコープ</b></td><td><b>権限</b></td></tr><tr><td>管理者</td><td>カタログ (カタログ内のすべてのアセットと注釈)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>所有者</td><td>各アセット (ルート項目とも呼ばれる)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>共同作成者</td><td>個別の各アセットおよび注釈</td><td>Read Update Delete ViewRoles Note: all the rights are revoked if the Read right on the item is revoked from the Contributor</td></tr></table>

> [AZURE.NOTE]**Read**、**Update**、**Delete**、**ViewRoles** 権限はいずれの項目 (アセットまたは注釈) にも適用されますが、**TakeOwnership**、**ChangeOwnership**、**ChangeVisibility**、**ViewPermissions** はルート アセットのみに適用されます。
>
>**Delete** 権限は、項目およびその下にあるサブ項目または単一項目に適用されます。たとえば、アセットを削除すると、そのアセットのすべての注釈も削除されます。

### アクセス許可

アクセス許可は、アクセス制御エントリの一覧です。各アクセス制御エントリによって、権限のセットがセキュリティ プリンシパルに割り当てられます。アクセス許可は、アセット (つまり、ルート項目) に対してのみ指定でき、アセットおよびすべてのサブ項目に適用されます。

**Azure Data Catalog** プレビュー中は、アセットの可視性を制限するシナリオを有効にするために、アクセス許可の一覧で **Read** 権限のみがサポートされています。

既定では、アクセス許可で可視性がプリンシパルのセットに限定されていない限り、認証されたすべてのユーザーがカタログ内のすべての項目に対して **Read** 権限を持ちます。

## REST API

**PUT** および **POST** の「項目の表示」要求を使用して、ロールおよびアクセス許可を制御することができます。項目のペイロードに加えて、2 つのシステム プロパティ: **__roles** と **__permissions** を指定できます。

> [AZURE.NOTE]
>
> **__permissions** は、ルート項目のみに適用されます。
>
> **所有者**ロールは、ルート項目のみに適用されます。
>
> 既定では、カタログで項目が作成されると、その**共同作成者**が、現在認証されているユーザーに設定されます。項目がすべてのユーザーによって更新される必要がある場合、**共同作成者**<Everyone>は、項目が最初に公開されたときに **__roles** プロパティで特別なセキュリティ プリンシパルに設定する必要があります (次の例を参照)。**共同作成者**は変更することができず、項目の有効期間中同じままになります (つまり、**管理者**または**所有者**であっても、**共同作成者**を変更する権限がありません)。**共同作成者**の明示的な設定に対してサポートされている唯一の値は、<Everyone> です。つまり、**共同作成者**には、項目または <Everyone> を作成したユーザーのみを指定できます。

###例
**項目を公開する場合は、共同作成者を <Everyone> に設定します。** 特別なセキュリティ プリンシパル <Everyone> の objectId は「00000000-0000-0000-0000-000000000201」です。**POST** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview **本文**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**所有者を割り当て、既存のルート項目に対する可視性を制限します。** **PUT** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE]PUT では、本文に項目のペイロードを指定する必要はありません。 PUT は、ロールまたはアクセス許可だけを更新するために使用できます。

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept.png

<!---HONumber=July15_HO3-->