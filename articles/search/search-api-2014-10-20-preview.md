<properties 
	pageTitle="Azure Search サービス REST API: 2014-10-20-Preview" 
	description="Azure Search サービス REST API: 2014-10-20-Preview" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/31/2015" 
	ms.author="heidist"/>

# Azure Search サービス REST API バージョン 2014-10-20-Preview #

このドキュメントでは、Azure Search サービス REST API の **2014-10-20-Preview** バージョンについて説明します。これは API のプロトタイプ バージョンであり、随時変更される可能性があります。運用環境のコードではこの API に依存しないでください。

> [AZURE.NOTE]これは試験用のバージョンであるため、このドキュメントで説明されている機能の中には Azure ポータルのユーザー インターフェイスで使用できないものがあります。具体的には、フィールドの `analyzer` オプションによる複数言語サポート、新しい `tag` スコアリング関数、新しい `suggesters` 機能は、当面は API のみで公開されます。API を使用してこれらの機能のいずれかを有効にした場合、ポータルでインデックス定義を表示することはできますが、更新することはできず、新しい機能は表示されません。

このバージョンに関連するその他の API コンテンツとしては次のものがあります。

- [スコアリング プロファイル (Azure Search サービス REST API: 2014-10-20-Preview)](../search-api-scoring-profiles-2014-10-20-preview/)

Azure Search REST API のリリース バージョンのドキュメントについては、MSDN を参照してください。詳細については、「[Azure Search サービス REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)」を参照してください。

##サービス REST API について##

Azure Search はクラウド ベースのサービスであり、カスタム検索アプリケーションの作成に使用できます。Azure Search には*検索サービス*と*インデックス*の概念があり、検索サービスには 1 つまたは複数のインデックスが含まれています。検索サービスは、完全修飾ドメイン名によって一意に識別されます (例: `mysearchservice.search.windows.net`)。API キーは、サービスのプロビジョニング時に生成され、Azure Search サービスへの要求を認証するために使用されます。

Azure Search サービスに対して実行できるアクションには 2 つの種類があります。

- **インデックス管理**: これには、検索サービスまたは検索インデックスに対して実行される管理タスクが含まれます。 

- **ドキュメント アクション**: これらのアクションは、特定のインデックスに対するコーパスのクエリおよび管理を行います。

このセクションに記載されている API は、インデックスの作成と設定、ドキュメントのアップロード、クエリなど、検索データに対する操作へのアクセスを提供します。API を呼び出すときは、次の点に留意してください。

- すべての API は、OData JSON 形式での HTTP 要求および応答を前提に定義されています。

- 後の注で説明するように、すべての API に対して付随する `api-key` がヘッダーまたはクエリ文字列で指定されている必要があります。

- すべての API は、HTTPS で発行される必要があります (既定のポート 443)。

- すべての API 要求には、`api-version` クエリ文字列パラメーターが含まれる必要があります。その値は、現在のサービス リリースのバージョンに設定される必要があります。次にその例を示します。

    GET /indexes?api-version=2014-10-20-Preview

- すべての API 要求では、オプションとして、`Accept` HTTP ヘッダーを設定できます。このヘッダーを設定しないと、既定で `application/json` と想定されます。

サービス管理用に別の API が提供されます。サービス管理操作の例としては、サービスのプロビジョニングや容量の変更などがあります。この API の詳細については、Azure Search 管理 REST API を参照してください。

### エンドポイント ###

サービス操作のエンドポイントは、プロビジョニングした Azure Search サービスの URL である https://*your-service-name*.search.windows.net です。


### バージョン ###

Azure Search の API には複数のバージョンがあります。運用アプリケーションで使用するために Azure Search を評価する場合は、`api-version=2014-07-31-Preview` をお勧めします。現時点で固定されている唯一のバージョンです。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。


<a name="Authentication"></a>
### 認証とアクセス制御###

Azure Search サービスの認証には、Search サービスの URL と `api-key` の 2 つの情報が必要です。`api-keys` は、サービスが作成されたときに生成され、サービスがプロビジョニングされた後は必要に応じて再生成できます。`api-key` は、すべての操作へのアクセスを許可する管理者キーまたはクエリ要求のみを認証するクエリ キーのいずれかです。サービスごとに、2 個の管理者キーと最大 50 個のクエリ キーを使用できます。

アクセス制御は、Azure プレビュー ポータルで提供されるロール ベースのアクセス制御 (RBAC) によってサービス管理に限定されます。ロールは、サービス管理のアクセス レベルの設定に使用されます。たとえば、管理者キーの表示は所有者ロールと共同作成者ロールに制限されるのに対し、サービスの状態はすべてのロールのメンバーが表示できます。

インデックスの管理、インデックスの設定、クエリなど、Search サービスのエンドポイントに対して実行されるデータ操作は、`api-keys` を使用して排他的にアクセスされます。RBAC は、インデックスまたはドキュメント関連の操作には適用されません。Azure Search での `api-keys` または RBAC の詳細については、「[Microsoft Azure で検索サービスを管理する](search-manage.md)」を参照してください。

**注**: 一般に、`api-key` などの機密データを要求 URI で渡すことはセキュリティが劣る方法であると考えられます。この理由から、Azure Search はクエリ文字列の `api-key` としてクエリ キーのみを受け付けます。インデックスの内容を一般に公開する必要がある場合を除き、このようなことは避ける必要があります。代わりに、要求ヘッダーとして `api-key` を渡すことをお勧めします。

###API の概要###

Azure Search サービス API では、エンティティの検索に対して 2 種類の構文がサポートされます。簡単な構文と代替の OData 構文です (詳細については「[OData のサポート (Azure Search)](http://msdn.microsoft.com/library/azure/dn798932.aspx)」を参照)。簡単な構文の一覧を次に示します。

[インデックスの作成](#CreateIndex)

    POST /indexes?api-version=2014-10-20-Preview

[インデックスの更新](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-10-20-Preview

[インデックスの取得](#GetIndex)

    GET /indexes/[index name]?api-version=2014-10-20-Preview

[インデックスの一覧取得](#ListIndexes)

    GET /indexes?api-version=2014-10-20-Preview

[インデックス統計の取得](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-10-20-Preview

[インデックスの削除](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-10-20-Preview

[インデックス内のデータの追加、削除、更新](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-10-20-Preview 

[ドキュメントの検索](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[ドキュメントの参照](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[ドキュメントのカウント](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-10-20-Preview

[検索候補](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________ <a name="IndexOps"></a>
# インデックス操作 #

特定のインデックス リソースに対して簡単な HTTP 要求 (POST、GET、PUT、DELETE) を実行することにより、Azure Search サービスでインデックスを作成および管理できます。インデックスを作成するには、最初にインデックス スキーマが記述されている JSON ドキュメントを POST します。スキーマでは、インデックスのフィールド、データ型、使用可能な方法 (たとえば、フルテキスト検索、フィルター、並べ替え、ファセット、検索候補) が定義されています。また、インデックスの動作を構成するスコアリング プロファイル、サジェスター、および他の属性も定義されています。

次の例では、2 つの言語で Description フィールドが定義されているホテル情報の検索に使用されるスキーマを示します。フィールドの使用方法を属性でどのように制御しているのかに注意してください。たとえば、`hotelId` はドキュメント キー (`"key": true`) として使用され、フルテキスト検索からは除外されます (`"searchable": false`)。

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer: "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ] 
    }
 
インデックスが作成された後、インデックスを設定するドキュメントをアップロードします。この次の手順については、「[ドキュメントの追加、削除、更新](#AddOrUpdateDocuments)」を参照してください。

Azure Search でのインデックス処理の概要に関するビデオについては、「[Channel 9 Cloud Cover episode on Azure Search (チャネル 9: Azure Search に関するクラウド カバー エピソード)](http://go.microsoft.com/fwlink/p/?LinkId=511509)」をご覧ください。


<a name="CreateIndex"></a>
## インデックスの作成 ##

HTTP POST 要求を使用して、Azure Search サービス内に新しいインデックスを作成できます。要求の本文は JSON ドキュメントであり、インデックス名、フィールド、クエリの動作を制御する属性、結果のスコアリング、サジェスター、および CORS オプションを指定します。

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

または、PUT を使用して、URI でインデックス名を指定することもできます。インデックスが存在しない場合は、作成されます。

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**注**: 許可されるインデックスの最大数は価格レベルによって異なります。無料サービスの場合、最大 3 個のインデックスが許可されます。標準サービスでは、Search サービスごとに 50 個のインデックスが許可されます。詳細については、「[Limits and constraints (Azure Search API) (制限と制約 (Azure Search API))](http://msdn.microsoft.com/library/azure/dn798934.aspx)」を参照してください。

**要求**

HTTPS はすべてのサービス要求に必要です。**Create Index** 要求は、POST メソッドと PUT メソッドのいずれかを使用して作成できます。POST を使用するときは、インデックス スキーマの定義とともに、要求本文でインデックスの名前を指定する必要があります。PUT の場合、インデックス名は URL の一部です。インデックスが存在しない場合は作成されます。既に存在する場合は、新しい定義に更新されます。

インデックスの名前は小文字にします。文字または数字で始めます。スラッシュとドットは使用できません。文字数の制限は 128 文字です。インデックスの名前は文字または数字で始め、残りの部分には文字、数字、ダッシュを使用できます。ダッシュは連続することができません。

`api-version` は必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。または、アナライザー インデックス属性によって表される言語アナライザーのサポートなどの試験的機能を評価するには、`2014-10-20-Preview` を使用します。API のバージョンの詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。言語アナライザーの詳細については、「[言語のサポート](#LanguageSupport)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `Content-Type`: 必須。これを `application/json` に設定します
- `api-key`: 必須。`api-key` は、 
- Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**Create Index** 要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。 
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` はどちらも、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

<a name="RequestData"></a> **要求本文の構文**

要求の本文にはスキーマ定義が含まれ、スキーマ定義には、そのインデックスにフィードされるドキュメント内のデータ フィールドのリスト、データ型、属性、およびクエリ時に一致するドキュメントのスコア付けに使用されるスコアリング プロファイルのオプションのリストが含まれます。

POST 要求の場合、要求本文でインデックスの名前を指定する必要があることに注意してください。

インデックスに存在できるキー フィールドは 1 つだけです。文字列フィールドでなければなりません。このフィールドは、インデックス内に格納される各ドキュメントの一意の識別子を表します。

要求ペイロードを構築するための構文は次のとおりです。サンプルの要求はこのトピックをさらに進むと登場します。

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

注: データ型 `Edm.Int64` は、API バージョン 2014-10-20-Preview 以降でサポートされます。
    
**インデックスの属性**

インデックスを作成するときに、以下の属性を設定できます。スコアリングおよびスコアリング プロファイルの詳細については、「[スコアリング プロファイル (Azure Search サービス REST API: 2014-10-20-Preview)](../search-api-scoring-profiles-2014-10-20-preview/)」を参照してください。

`name` - フィールドの名前を設定します。

`type` - フィールドのデータ型を設定します。サポートされる型の一覧については、「[サポートされるデータ型](#DataTypes)」を参照してください。

`searchable` - フィールドをフルテキスト検索可能としてマークします。これは、インデックス処理中に単語の区切りなどの分析が行われることを意味します。`searchable` フィールドに "sunny day" のような値を設定した場合、個別トークン "sunny" と "day" に内部的に分割されます。これにより、これらの語句をフルテキスト検索できます。`Edm.String` 型または `Collection(Edm.String)` 型のフィールドは、既定で `searchable` になります。他の型のフィールドは、`searchable` にすることはできません。

  - **注**: Azure Search はフルテキスト検索のためにフィールド値のトークン化されたバージョンを追加して格納するため、`searchable` フィールドを使用するとインデックスで消費される領域が増えます。インデックスの領域を節約する必要があり、フィールドを検索に含める必要がない場合は、`searchable` を `false` に設定してください。

`filterable` - フィールドを `$filter` クエリで参照できるようにします。`filterable` と `searchable` では文字列の処理方法が異なります。`filterable` である `Edm.String` 型または `Collection(Edm.String)` 型のフィールドは、単語分割を行われないため、比較は完全一致のみになります。たとえば、そのようなフィールド `f` に "sunny day" を設定した場合、`$filter=f eq 'sunny'` では一致が見つかりませんが、`$filter=f eq 'sunny day'` では見つかります。既定では、すべてのフィールドが `filterable` です。

`sortable` - 既定ではシステムは結果をスコア順に並べ替えますが、多くの場合、ユーザーはドキュメントのフィールドで並べ替えることを望みます。`Collection(Edm.String)` 型のフィールドを `sortable` にすることはできません。他のすべてのフィールドは、既定で `sortable` になります。

`facetable` - 通常、カテゴリ別のヒット カウントを含む検索結果のプレゼンテーションで使用されます (たとえば、デジタル カメラを検索し、ブランド別、メガピクセル別、価格別などでヒットを表示する場合)。このオプションは、`Edm.GeographyPoint` 型のフィールドでは使用できません。他のすべてのフィールドは、既定で `facetable` になります。

  - **注**: `filterable`、`sortable`、または `facetable` である `Edm.String` 型のフィールドは、長さの上限が 32 KB です。これは、このようなフィールドは 1 つの検索語句として扱われ、Azure Search での語句の最大長は 32 KB であるためです。これより多くのテキストを単一の文字列フィールドに格納する必要がある場合は、インデックスの定義で明示的に `filterable`、`sortable`、および `facetable` を `false` に設定する必要があります。

`suggestions` - フィールドを先行入力のオートコンプリートに使用できるかどうかを設定します。これは、`Edm.String` 型または `Collection(Edm.String)` 型のフィールドにのみ設定できます。`suggestions` はインデックスに余分な領域が必要になるので、既定では `false` になっています。**注**: 検索候補の場合は、このオプションではなく、バージョン 2014-10-20-Preview で導入された `suggesters` プロパティの使用を検討してください。将来のバージョンでは、`suggestions` プロパティは廃止されて、代わりに `suggesters` の指定が使用されるようになります。

  - **注**: 上記のどの属性 (`searchable`、`filterable`、`sortable`、`facetable`、`suggestions`) も `true` に設定されていないフィールドは、実質的に逆インデックスから除外されます。このオプションは、クエリでは使用されませんが、検索結果には必要なフィールドに便利です。このようなフィールドをインデックスから除外と、パフォーマンスが向上します。

`key` - インデックス内のドキュメントに対する一意の識別子を含んでいるものとしてフィールドをマークします。正確に 1 つのフィールドを `key` として選択する必要があり、そのフィールドは `Edm.String` 型でなければなりません。キー フィールドは、[参照 API](#LookupAPI) を使用してドキュメントを直接参照するために使用できます。

`retrievable` - 検索結果でフィールドを返すことができるかどうかを設定します。これは、フィールド (たとえば、マージン) をフィルター、並べ替え、またはスコアリングのメカニズムとして使用するけれども、エンド ユーザーに対しては表示したくない場合に役立ちます。`key` フィールドに対してはこの属性が `true` である必要があります。

`scoringProfiles` - 検索結果での項目の順序を変更できるカスタム スコアリング動作を定義します。スコアリング プロファイルは、重み付けされたフィールドと関数で構成されます。スコアリング プロファイルで使用される属性の詳細については、「[スコアリング プロファイル (Azure Search サービス REST API: 2014-10-20-Preview)](../search-api-scoring-profiles-2014-10-20-preview/)」を参照してください。

`analyzer` - フィールドに使用するテキスト アナライザーの名前を設定します。設定できる値については、「[言語のサポート](#LanguageSupport)」を参照してください。このオプションは、`searchable` フィールドでのみ使用できます。フィールドのアナライザーを選択した後は変更できません。


<a name="LanguageSupport"></a> **言語のサポート**

検索可能なフィールドで最も頻繁に行われる分析は、単語の分割、テキストの正規化、語句の除外などです。既定では、Azure Search の検索可能フィールドは [Apache Lucene 標準アナライザー](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)で分析されます。このアナライザーは、["Unicode Text Segmentation"](http://unicode.org/reports/tr29/) ルールに従ってテキストを要素に分割します。さらに、標準アナライザーはすべての文字を小文字形式に変換します。インデックス付きドキュメントと検索語句の両方について、インデックス作成とクエリ処理の間に分析が行われます。

Azure Search では、さまざまな言語でフィールドのインデックスを作成できます。これらの各言語には、特定の言語の特性が考慮されている標準以外のテキスト アナライザーが必要です。たとえば、フランス語のアナライザーは、[Light French Stemmer](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html) を適用して単語を[単語の語幹](http://en.wikipedia.org/wiki/Stemming)に削減します。さらに、分析されたテキストから[エリジョン](http://en.wikipedia.org/wiki/Elision)およびフランス語のストップ ワードを除去します。英語のアナライザーは標準アナライザーを拡張します。単語から所有格を (末尾の 's) を除去し、[Porter Stemming アルゴリズム](http://tartarus.org/~martin/PorterStemmer/)に従ってステミングを適用し、英語の[ストップ ワード](http://en.wikipedia.org/wiki/Stop_words)を除去します。
 
`analyzer` プロパティを設定することにより、インデックス定義のフィールドごとに個別のアナライザーを構成できます。たとえば、英語、フランス語、スペイン語によるホテルの説明を含む個別のフィールドを同じインデックスに同時に作成できます。クエリでは、検索クエリでどの言語固有フィールドを返すかを指定します。

サポートされているアナライザーとその機能の簡単な説明を以下に示します。

<table style="font-size:12">
    <tr>
		<th>言語</th>
		<th>アナライザーの名前</th>
		<th>説明</th>
	</tr>
    <tr>
		<td>アラビア語</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>アラビア語の正字法正規化を実装します</li>
			<li>ライト アルゴリズム ステミングを適用します</li>
			<li>アラビア語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ブラジル語</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ブラジル語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>中国語 (簡体字)</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>確率的知識モデルを使用して、最適な単語の分割を探します</li>
			<li>中国語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>中国語 (繁体字)</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>二重字 (隣接する 2 つの中国語文字の重なり合ったグループ) のインデックスを作成します</li>
			<li>文字幅の違いを正規化します</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>チェコ語</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>チェコ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>デンマーク語</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>デンマーク語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>オランダ語</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>オランダ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ドイツ語</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ドイツ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ギリシャ語</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ギリシャ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>日本語</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>英語のストップ ワードを除外します</li>
			<li>所有格を除去します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>フィンランド語</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>フィンランド語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>フランス語</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>フランス語のストップ ワードを除外します</li>
			<li>エリジョンを除去します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ヒンディー語</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ヒンディー語のストップ ワードを除外します</li>
			<li>スペル バリエーションの一部の相違を除去します</li>
			<li>インドの言語でのテキストの Unicode 表現を正規化します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ハンガリー語</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ハンガリー語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>インドネシア語</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>インドネシア語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>イタリア語</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>イタリア語のストップ ワードを除外します</li>
			<li>エリジョンを除去します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>日本語</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>形態素分析を使用します</li>
			<li>一般的なカタカナのスペルのバリエーションを正規化します</li>
			<li>ライト ストップ ワード/ストップ タグを除去します</li>
			<li>文字幅を正規化します</li>
			<li>レンマ化 - 屈折した形容詞と動詞を基本形式に削減します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>韓国語</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>二重字 (隣接する 2 つの中国語文字の重なり合ったグループ) のインデックスを作成します</li>
			<li>文字幅の違いを正規化します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ラトビア語</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ラトビア語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>ノルウェー語</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ノルウェー語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ポーランド語</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>アルゴリズム ステミング (Stempel) を適用します</li>
			<li>ポーランド語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ポルトガル語</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ポルトガル語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>ルーマニア語</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ルーマニア語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>ロシア語</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>ロシア語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>スペイン語</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>スペイン語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>スウェーデン語</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>スウェーデン語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>トルコ語</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>アポストロフィから後のすべての文字を除去します (アポストロフィ自体を含みます)</li>
			<li>ライト ステミングを適用します</li>
			<li>トルコ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>タイ語</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>ライト ステミングを適用します</li>
			<li>タイ語のストップ ワードを除外します</li>
		</ul>
		</td>
	</tr>
	<td colspan="3">さらに、Azure Search では言語に依存しないアナライザー構成が提供されます。</td> 
    <tr>
		<td>標準 ASCII フォールディング</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>Unicode テキストのセグメント化 (標準トークナイザー)</li>
			<li>ASCII フォールディング フィルター - 最初の 127 ASCII 文字セットに属していない Unicode 文字を ASCII と同等のものに変換します。これは、付加記号の除去に便利です。</li>
		</ul>
		</td>
	</tr>
</table>

名前に <i>lucene</i> が含まれるすべてのアナライザーは、[Apache Lucene の言語アナライザー](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)を利用しています。ASCII フォールディング フィルターの詳細については、[こちら](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)を参照してください。

**CORS のオプション**

ブラウザーがすべてのクロス オリジン要求を禁止するので、既定ではクライアント側 Javascript はどの API も呼び出すことができません。インデックスへのクロス オリジン クエリを許可するには、`corsOptions` 属性を設定することによって CORS (クロス オリジン リソース共有) を有効にします。セキュリティ上の理由から、CORS をサポートするのはクエリ API だけであることに注意してください。CORS に対しては以下のオプションを設定できます。

- `allowedOrigins` (必須): インデックスへのアクセスが許可されるオリジンのリストです。つまり、これらのオリジンから提供されるすべての Javascript コードは、インデックスのクエリを許可されます (正しい API キーを提供する場合)。各オリジンの標準的な形式は `protocol://fully-qualified-domain-name:port` ですが、多くの場合ポートは省略されます。詳細については、[この記事](http://go.microsoft.com/fwlink/?LinkId=330822)を参照してください。
 - すべてのオリジンにアクセスを許可する場合は、`allowedOrigins` 配列の唯一の要素として `*` を指定します。**運用環境の検索サービスではこの方法は推奨されない**ことに注意してください。 ただし、開発やデバッグの目的では役に立つ場合があります。
- `maxAgeInSeconds` (省略可能): ブラウザーはこの値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) を決定します。負ではない整数を指定する必要があります。この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。これを設定しないと、既定の期間として 5 分が使用されます。

<a name="Suggesters"></a> **サジェスター**

サジェスターは検索でのオートコンプリートを有効にします。通常、ユーザーの入力中に部分的な検索文字列が検索候補 API に送信されて、API は検索候補の語句のセットを返します。

Azure Search は新しいサジェスター API に移行しています。バージョン 2014-07-31-Preview の限定的な検索候補用 API では、フィールドを `"suggestions": true` でマークし、短い文字列 (3 ～ 25 文字) のプレフィックス検索候補を実行できます。バージョン 2014-10-20-Preview Azure Search 以降には、このセクションで説明する「サジェスター」に基づくさらに強力なバージョンの検索候補機能があります。この新しい実装は、接頭辞一致および挿入辞一致を行うことができ、検索文字列の誤りに対してより優れた許容範囲を持ちます。バージョン 2014-10-20-Preview 以降では、新しいサジェスター API を使用することを強くお勧めします。

現在のサジェスターのサポートは、厳密ではない語句より特定のドキュメントの検索候補に使用したときの方が動作が優れています。この種類のサジェスターに適しているソース フィールドは、タイトル、名前、および項目を識別できる他の比較的短い語句です。あまり効果的ではないフィールドの種類の例は、カテゴリやタグなどの非常に反復的なフィールドまたは説明やコメントなどの非常に長いフィールドです。

インデックス定義の一部として、単一のサジェスターを `suggesters` コレクションに追加できます。サジェスターを定義するプロパティは次のとおりです。

- `name`: サジェスターの名前。`suggest` API を呼び出すときは、サジェスターの名前を使用します。
- `searchMode`: 候補語句の検索に使用する方法。現在サポートされている唯一のモードは `analyzingInfixMatching` であり、文章の先頭または途中でのフレーズの柔軟な一致を実行します。
- `sourceFields`: 検索候補の内容のソースである 1 つまたは複数のフィールドのリスト。`Edm.String` 型および `Collection(Edm.String)` 型のフィールドだけを、検索候補のソースにできます。サジェスターの現在のプレビュー バージョンでは、カスタム アナライザーが設定されていないフィールドだけを使用できます。

現在のバージョンの API のサジェスター コレクションで使用できるサジェスターは 1 つだけです。

<a name="CreateUpdateIndexExample"></a> **要求本文の例**
 
    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ] 
    }

**応答**

要求が成功した場合:要求成功の場合: 201 作成されました。

既定では、応答本文には作成されたインデックス定義の JSON が含まれます。`Prefer` 要求ヘッダーが `return=minimal` に設定されている場合、応答本文は空で、成功の状態コードは「201 作成されました」ではなく「204 コンテンツがありません」になります。これは、インデックスの作成に PUT または POST のどちらが使用された場合でも同じです。

<a name="UpdateIndex"></a>
## インデックスの更新 ##

Azure Search 内の既存のインデックスを、HTTP PUT 要求を使用して更新できます。パブリック プレビューでの更新には、既存のスキーマへの新しいフィールドの追加、CORS オプションの変更、スコアリング プロファイルの変更が含まれます (「[スコアリング プロファイル (Azure Search サービス REST API: 2014-10-20-Preview)](../search-api-scoring-profiles-2014-10-20-preview/)」を参照)。更新するインデックスの名前を要求 URI で指定します。

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**重要:** インデックス スキーマ更新のサポートは、検索インデックスの再作成を必要としない操作に制限されます。フィールドの種類の変更といったインデックスの再作成を必要とするスキーマ更新は、現在はサポートされていません。新しいフィールドはいつでも追加できますが、既存のフィールドを変更または削除することはできません。同じことがサジェスターにも当てはまります。フィールドを追加するときに新しいフィールドをサジェスターに追加できますが、サジェスターからフィールドを削除したり、既存のフィールドをサジェスターに追加したりすることはできません。

新しいフィールドをインデックスに追加すると、インデックス内のすべての既存ドキュメントでそのフィールドに null 値が自動的に設定されます。新しいドキュメントがインデックスに追加されるまで、追加の記憶域は使用されません。

**要求**

HTTPS はすべてのサービス要求に必要です。**Update Index** 要求は、HTTP PUT を使用して作成されます。PUT の場合、インデックス名は URL の一部です。インデックスが存在しない場合は作成されます。インデックスが既に存在する場合は、新しい定義に更新されます。

インデックスの名前は小文字にします。文字または数字で始めます。スラッシュとドットは使用できません。文字数の制限は 128 文字です。インデックスの名前は文字または数字で始め、残りの部分には文字、数字、ダッシュを使用できます。ダッシュは連続することができません。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `Content-Type`: 必須。これを `application/json` に設定します
- `api-key`: 必須。`api-key` は Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**Update Index** 要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文の構文**

既存のインデックスを更新する場合、本文には、元のスキーマ定義に加えて、追加する新しいフィールドと、ある場合は変更後のスコアリング プロファイル、サジェスター、および CORS オプションが、含まれる必要があります。スコアリング プロファイルおよび CORS オプションを変更しない場合は、インデックスが作成されたときの元の値を含める必要があります。一般に、更新に使用する最善のパターンは、GET でインデックス定義を取得し、変更した後、PUT で更新するというものです。

利便性を考えて、インデックスの作成に使用されるスキーマ構文をここに再掲します。詳細については、「[インデックスの作成](#CreateIndex)」を参照してください。

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

注: データ型 `Edm.Int64` は、API バージョン 2014-10-20-Preview 以降でサポートされます。

**応答**

要求が成功した場合: 204 コンテンツがありません。

既定では、応答本文は空になります。ただし、`Prefer` 要求ヘッダーを `return=representation` に設定した場合は、応答本文には更新されたインデックス定義の JSON が含まれます。この場合、成功の状態コードは "200 OK" になります。

<a name="ListIndexes"></a>
## インデックスの一覧取得 ##

**List Indexes** 操作は、Azure Search サービスに現在存在するインデックスの一覧を返します。

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**要求**

HTTPS はすべてのサービス要求に必要です。**List Indexes** 要求は、GET メソッドを使用して作成できます。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。
 
- `api-key`: 必須。`api-key` は Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**List Indexes** 要求の `api-key` には (クエリ キーではなく) 管理者キーを設定する必要があります。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

次は応答本文の例です。

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

フィルター処理で応答を興味のあるプロパティだけに絞り込むことができます。たとえば、インデックス名の一覧だけを必要とする場合、OData `$select` クエリ オプションを使用します。

    GET /indexes?api-version=2014-10-20-Preview&$select=name

この場合は、上記の例の応答は次のように表示されます。

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

これは、Search サービスにインデックスがたくさんある場合、帯域幅を節約する便利な方法となります。

<a name="GetIndex"></a>
## インデックスの取得 ##

**Get Index** 操作は Azure Search からインデックスの定義を取得します。

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**要求**

サービス要求には HTTPS が必要です。**Get Index** 要求は、GET メソッドを使用して作成できます。
 
要求 URI の [index name] では、インデックス コレクションから返すインデックスを指定します。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。
 
- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**Get Index** 要求の `api-key` には (クエリ キーではなく) 管理者キーを設定する必要があります。

要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答ペイロードの例については、[インデックスの作成と更新](#CreateUpdateIndexExample)の JSON の例を参照してください。

<a name="DeleteIndex"></a>
## インデックスの削除 ##

**Delete Index** 操作は、Azure Search サービスからインデックスおよび関連するドキュメントを削除します。インデックス名は、Azure プレビュー ポータルのサービス ダッシュボードまたは API から取得できます。詳細については、「[インデックスの一覧取得](#ListIndexes)」を参照してください。

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]
    
**要求**

サービス要求には HTTPS が必要です。**Delete Index** 要求は、DELETE メソッドを使用して作成できます。
 
要求 URI の [index name] では、インデックス コレクションから削除するインデックスを指定します。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。
 
- `api-key`: 必須。`api-key` は Search サービスに対する要求の認証に使用されます。これはサービス URL に固有の文字列値です。**Delete Index** 要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

<a name="GetIndexStats"></a>
## Get Index Statistics (Azure Search API) (インデックス統計の取得 (Azure Search API)) ##

**Get Index Statistics** 操作は、現在のインデックスに対するドキュメントの数と記憶域の使用状況を Azure Search から返します。

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**要求**

すべてのサービス要求には HTTPS が必要です。**Get Index Statistics** 要求は、GET メソッドを使用して作成できます。

要求 URI の [index name] では、インデックスの統計情報を取得するインデックスをサービスに対して指定します。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。
 
- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**Get Index Statistics** 要求の `api-key` には (クエリ キーではなく) 管理者キーを設定する必要があります。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答本文の形式は次のとおりです。

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________ <a name="DocOps"></a>
# ドキュメントの操作 #

Azure Search では、インデックスはサービスにアップロードされた JSON ドキュメントを使用して設定されます。アップロードされたすべてのドキュメントが、検索データのコーパスを構成します。ドキュメントにはフィールドが含まれ、その一部はアップロード時に検索語句にトークン化されます。Azure Search API の `/docs` URL セグメントは、インデックス内のドキュメントのコレクションを表します。ドキュメントのアップロード、マージ、削除、クエリなど、コレクションに対して実行される操作はすべて単一インデックスのコンテキストで行われるので、これらの操作の URL は常に特定のインデックス名に対する `/indexes/[index name]/docs` で始まります。

リレーショナル データベースまたはその他の構造化されたデータ ソースからの結果セットを使用して、アプリケーション コードで JSON ドキュメントを生成して Azure Search にアップロードできます。Codeplex の Azure Search Adventure Works デモ サンプル アプリケーションには、Adventure Works サンプル データベースからの結果セットを使用して JSON ドキュメントを作成するコードが含まれます。サンプル アプリケーションの詳細については、[こちら](search-create-first-solution.md)を参照してください。

ほとんどのアプリケーション開発シナリオでは、検索データは分離されて、アプリケーションのデータ層の外部に存在します。アプリケーションでオンプレミス データベースを使用してインベントリのステータスを追跡している場合、Azure Search に保持されるドキュメントには、製品名、価格、利用可能性に関して似た、または同一のデータ値が含まれますが、検索を最適化するために逆インデックスで格納されます。

検索する各項目に対して 1 つのドキュメントを用意するように計画する必要があります。たとえば、映画レンタル アプリケーションでは映画ごとに 1 つのドキュメントを使用し、店舗アプリケーションでは SKU ごとに 1 つのドキュメントを使用し、オンライン コースウェア アプリケーションではコースごとに 1 つのドキュメントを使用し、調査会社ではリポジトリ内の論文ごとに 1 つのドキュメントを使用する、という具合です。

ドキュメントは、1 つまたは複数のフィールドで構成されます。フィールドには、検索語句にトークン化されるテキスト、およびフィルターまたはスコアリング プロファイルで使用できるトークン化されないテキストまたはテキスト以外の値を格納できます。各フィールドに対してサポートされる名前、データ型、検索機能は、インデックス スキーマによって決まります。各インデックス スキーマのフィールドの 1 つは ID として指定される必要があり、ドキュメントごとにインデックス内でそのドキュメントを一意に識別する ID フィールドの値が必要です。他のすべてのドキュメント フィールドは省略可能であり、指定しないと既定で null 値になります。null 値は、逆インデックスの領域を使用しないことに注意してください。

ドキュメントをアップロードする前に、サービスでインデックスを作成しておく必要があります。この最初の手順の詳細については、「[インデックスの作成](#CreateIndex)」を参照してください。

**注**: Azure Search パブリック プレビューは、フルテキスト検索に対してのみ英語をサポートします。

<a name="AddOrUpdateDocuments"></a>
## ドキュメントの追加、更新、削除 ##

HTTP POST を使用して、指定したインデックスのドキュメントのアップロード、マージ、マージまたはアップロード、または削除を行うことができます。更新の数が多い場合は、ドキュメントのバッチ処理 (バッチごとに最大 1000 ドキュメント、またはバッチごとに約 16 MB) をお勧めします。

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    
**要求**

HTTPS はすべてのサービス要求に必要です。HTTP POST を使用して、指定したインデックスのドキュメントのアップロード、マージ、マージまたはアップロード、または削除を行うことができます。

要求 URI に含まれる [index name] では、ドキュメントを POST するインデックスを指定します。一度に 1 つのインデックスに対してのみ、ドキュメントを POST できます。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `Content-Type`: 必須。これを `application/json` に設定します
- `api-key`: 必須。`api-key` は Search サービスに対する要求の認証に使用されます。これはサービスに固有の文字列値です。**Add Documents** 要求の `api-key` ヘッダーには (クエリ キーではなく) 管理者キーを設定する必要があります。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

要求の本文には、インデックスを作成する 1 つまたは複数のドキュメントが含まれます。ドキュメントは、一意のキーによって識別されます。各ドキュメントは、アクション (upload、merge、mergeOrUpload、delete) と関連付けられています。アップロード要求には、キー/値ペアのセットとしてドキュメント データが含まれる必要があります。

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }
    
**ドキュメント アクション**

- `upload`: 更新アクションは、ドキュメントが新しい場合は挿入されて存在する場合は更新/置換される "upsert" に似ています。更新の場合はすべてのフィールドが置換されることに注意してください。
- `merge`: マージは、指定したフィールドで既存のドキュメントを更新します。ドキュメントが存在しない場合、マージは失敗します。マージで指定したすべてのフィールドは、ドキュメント内の既存のフィールドを置き換えます。これには、`Collection(Edm.String)` 型のフィールドも含まれます。たとえば、ドキュメントにフィールド "tags" があって値が `["budget"]` である場合、"tags" に値 `["economy", "pool"]` を指定してマージを実行すると、"tags" フィールドの最終的な値は `["economy", "pool"]` になります。`["budget", "economy", "pool"]` では**ありません**。
- `mergeOrUpload`: 指定したキーを持つドキュメントがインデックスに既に存在する場合は、`merge` と同じように動作します。ドキュメントが存在しない場合は、新しいドキュメントの `upload` と同じように動作します。
- `delete`: インデックスから指定したドキュメントを削除します。`delete` 操作ではキー フィールドの値のみを指定できることに注意してください。他のフィールドを指定すると、HTTP 400 エラーになります。ドキュメントから個々のフィールドを削除する場合は、代わりに `merge` を使用して、フィールドを明示的に `null` に設定します。 

**応答**

状態コード: 成功応答に対して返される「200 OK」は、すべての項目のインデックスが正常に作成されたことを意味します (すべての項目の "status" フィールドが true に設定されることによって示される状態)。

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

状態コード: 1 つ以上の項目のインデックスが正常に作成されなかった場合、207 が返されます (インデックスが作成されなかった項目の "status" フィールドが false に設定されることによって示される状態)。
 
    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

`errorMessage` プロパティは、可能な場合はインデックス作成エラーの理由を示します。

**注**: クライアント コードで頻繁に 207 応答が発生する場合、考えられる理由の 1 つはシステムの負荷が高いことです。`errorMessage` プロパティを調べることによってこれを確認できます。負荷が高い場合は、***インデックス作成の要求を調整する***ことをお勧めします。調整しないでインデックス作成トラフィックが減らない場合、システムは 503 エラーですべての要求を拒否し始めることがあります。

状態コード: 429 は、インデックスあたりのドキュメント数に対するクォータを超過したことを示します。新しいインデックスを作成するか、またはさらに高い処理能力の限界にアップグレードする必要があります。

**例:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville", 
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________ <a name="SearchDocs"></a>
## ドキュメントの検索 ##

**Search** 操作は、GET 要求として発行され、一致するドキュメントを選択するための条件を示すクエリ パラメーターを指定します。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**要求**

サービス要求には HTTPS が必要です。**Search** 要求は、GET メソッドを使用して作成できます。

要求 URI は、クエリ パラメーターと一致するすべてのドキュメントについて、クエリするインデックスを指定します。

**クエリ パラメーター**

`search=[string]` (省略可能) - 検索するテキスト。`searchFields` を指定しないと、既定ですべての `searchable` フィールドが検索されます。`searchable` フィールドを検索するときは、検索テキスト自体がトークン化されるので、複数の語句を空白で区切ることができます (例: `search=hello world`)。任意の語句と一致させるには、`*` を使用します (これはブール型フィルターのクエリに便利です)。このパラメーターを省略することは、`*` に設定するのと同じ効果を持ちます。検索構文の詳細については、後の「簡単なクエリ構文」を参照してください。

  - **注**: `searchable` フィールドをクエリすると、予想外の結果になることがあります。トークナイザーには、アポストロフィ、数値内のコンマなど、英語テキストで一般的なケースを処理するロジックが含まれています。たとえば、英語ではコンマは大きな数の桁区切り記号として使用されるので、`search=123,456` は、個別の語句 123 および 456 ではなく、1 つの語句 123,456 と一致します。このため、`search` パラメーターで語句を区切るには区切り記号ではなく空白文字を使用することをお勧めします。

`searchMode=any|all` (省略可能、既定値は `any`) - ドキュメントを一致としてカウントするために、任意の検索語句またはすべての検索語句が一致する必要があるかどうか。

`searchFields=[string]` (省略可能) - 指定したテキストを検索するフィールド名のコンマ区切りのリスト。対象フィールドは、`searchable` としてマークされている必要があります。

`$skip=#` (省略可能) - スキップする検索結果の数。100,000 を超えることはできません。ドキュメントを順番にスキャンする必要があり、この制限のために `$skip` を使用できない場合は、代わりに完全に順序付けられているキーに対する `$orderby` と範囲クエリでの `$filter` を使用することを検討してください。

`$top=#` (省略可能) - 取得する検索結果の数。既定値は 50 です。1000 より大きい値を指定し、結果が 1000 より多い場合、最初の 1000 個の結果だけと、次の結果ページへのリンクが返されます ([後の例](#SearchResponse)の `@odata.nextLink` を参照)。

`$count=true|false` (省略可能、既定値は `false`) - 結果の合計数を取得するかどうか。この値を `true` に設定すると、パフォーマンスに影響する場合があります。返されるカウントは概数であることに注意してください。

`$orderby=[string]` (省略可能) - 結果を並べ替えるための式のコンマ区切りのリスト。各式は、フィールド名または `geo.distance()` 関数の呼び出しです。各式に続いて、昇順を示す `asc` または降順を示す `desc` を指定できます。既定値は昇順です。結び付きは、ドキュメントの一致スコアによって切り離されます。`$orderby` を指定しないと、既定の並べ替え順序はドキュメント一致スコアの降順になります。`$orderby` には 32 句の制限があります。

`$select=[string]` (省略可能) - 取得するフィールドのコンマ区切りリスト。指定しないと、スキーマで取得可能とマークされているすべてのフィールドが含まれます。このパラメーターを `*` に設定することによって、明示的にすべてのフィールドを要求することもできます。

`facet=[string]` (0 以上) - ファセットに使用するフィールド。オプションとして、コンマ区切りの `name:value` ペアとして表された、ファセットをカスタマイズするパラメーターを文字列に含めることができます。有効なパラメーターは次のとおりです。

- `count` (ファセット語句の最大数、既定値は 10)。最大値はありませんが、値が大きいとパフォーマンスが低下します。ファセット フィールドに多数の一意の語句が含まれる場合は特にそうです。
  - 例: `facet=category,count:5` はファセット結果の上位 5 カテゴリを取得します。  
  - **注**: `count` パラメーターが一意の語句の数より小さい、結果が正しくない可能性があります。これは、ファセット クエリがシャード間に分散される方法のためです。`count` を大きくすると、一般に、語句の数の精度は向上しますが、パフォーマンスは低下します。 
- `sort` (カウントの*降順*に並べ替える `count`、カウントの*昇順*に並べ替える `-count`、値の*昇順*に並べ替える `value`、値の*降順*に並べ替える `-value` のいずれか)
  - 例: `facet=category,count:3,sort:count` は、各都市名のドキュメント数を降順に並べ替えたファセット結果の上位 3 カテゴリを取得します。たとえば、上位 3 カテゴリが Budget、Motel、Luxury で、Budget が 5 ヒット、Motel が 6 ヒット、Luxury が 4 ヒットである場合、バケットは Motel、Budget、Luxury の順序になります。
  - 例: `facet=rating,sort:-value` では、値の降順で、すべての可能な評価のバケットが生成されます。たとえば、評価が 1 ～ 5 の場合、各評価に一致したドキュメントの数に関係なく、バケットは 5、4、3、2、1 の順序になります。
- `values` (ファセット エントリ値の動的なセットを指定する、パイプで区切られた数値または `Edm.DateTimeOffset` 値)
  - 例: `facet=baseRate,values:10|20` では、ベース レート 0 以上 10 未満、10 以上 20 未満、20 以上の、3 つのバケットが生成されます。
  - 例: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` では、2010 年 2 月より前に改装されたホテルと、2010 年 2 月 1 日以降に改装されたホテルの、2 つのバケットが生成されます。
- `interval` (数値に対する 0 より大きい整数間隔、または日時値に対する `minute`、`hour`、`day`、`week`、`month`、`quarter`、`year`)
  - 例: `facet=baseRate,interval:100` では、サイズ 100 のベース レート範囲に基づくバケットが生成されます。たとえば、ベース レートがすべて $60 ～ $600 の範囲である場合、0 ～ 100、100 ～ 200、200 ～ 300、300 ～ 400、400 ～ 500、500 ～ 600 のバケットが生成されます。
  - 例: `facet=lastRenovationDate,interval:year` では、ホテルが改装された各年に対して 1 つのバケットが生成されます。
- **注**: `count` と `sort` を同じファセット指定で組み合わせることができますが、それらを `interval` または `values` と組み合わせることはできず、`interval` と `values` を一緒に組み合わせることはできません。

`$filter=[string]` (省略可能) - 標準の OData 構文で構造化された検索式。Azure Search がサポートする OData 式の文法のサブセットの詳細については、「[OData 式の構文](#ODataExpressionSyntax)」を参照してください。

`highlight=[string]` (省略可能) - ヒットの強調表示に使用されるコンマで区切られたフィールド名のセット。`searchable` フィールドのみが、ヒットの強調表示に使用できます。

  `highlightPreTag=[string]` (省略可能) - ヒットの強調表示の前に付加する文字列タグ。`highlightPostTag` で設定する必要があります。URL の予約済みの文字は、パーセントでエンコードする必要があります (たとえば、# ではなく %23)。

  `highlightPostTag=[string]` (省略可能) - ヒットの強調表示の後に付加する文字列タグ。`highlightPreTag` で設定する必要があります。URL の予約済みの文字は、パーセントでエンコードする必要があります (たとえば、# ではなく %23)。

`scoringProfile=[string]` (省略可能) - 結果の並べ替えを目的として一致するドキュメントのマッチ スコアを評価するためのスコアリング プロファイルの。

`scoringParameter=[string]` (0 以上) - 名前:値の形式を使用して、スコアリング関数で定義されている各パラメーターの値を示します (例: `referencePointParameter`)。たとえば、スコアリング プロファイルで "mylocation" という名前のパラメーターを持つ関数が定義されている場合、クエリ文字列のオプションは &scoringParameter=mylocation:-122.2,44.8 になります。

`api-version=[string]` (必須)。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

注: この操作の場合、`api-version` はクエリ パラメーターとして指定します。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービス URL に固有の文字列値です。**Search** 要求では、`api-key` に対して管理者キーまたはクエリ キーを指定できます。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

<a name="SearchResponse"></a> **応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }
    
**例:**

その他の例については、「[Azure Search の OData 式の構文](https://msdn.microsoft.com/library/azure/dn798921.aspx)」を参照してください。

1) 日付で降順に並べ替えられたインデックスを検索します。

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-10-20-Preview

注: DateTime フィールドの精度はミリ秒に制限されます。それより小さい値を指定するタイムスタンプを押した場合 (たとえば、タイムスタンプ 10:30:09.7552052 の秒の部分)、戻り値は丸められます (例では 10:30:09.7550000)。

2) ファセット検索で、インデックスを検索し、カテゴリ、レーティング、タグに対するファセットと、特定の範囲の baseRate の項目を取得します。

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-10-20-Preview

3) ユーザーがレーティング 3 とカテゴリ "Motel" をクリックした後、フィルターを使用して前のファセット クエリの結果を絞り込みます。

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-10-20-Preview

4) ファセット検索で、クエリで返される一意の語句に上限を設定します。既定値は 10 ですが、`facet` 属性の `count` パラメーターを使用してこの値を増減できます。

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-10-20-Preview

5) 特定のフィールド内のインデックスを検索します (たとえば、言語固有のフィールド)。

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-10-20-Preview

6) 複数のフィールドにまたがるインデックスを検索します。たとえば、複数の言語の検索可能なフィールドをすべて同じインデックスに格納してクエリできます。英語とフランス語の説明が同じドキュメントに共存している場合、いずれかまたはすべてをクエリ結果で返すことができます。

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-10-20-Preview
	
クエリできるのは一度に 1 つのインデックスだけであることに注意してください。一度に 1 つをクエリするのでない限り、言語ごとに複数のインデックスを作成しないでください。

7) ページング - 項目の 1 番目のページを取得します (ページ サイズは 10)。

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-10-20-Preview

8) ページング - 項目の 2 番目のページを取得します (ページ サイズは 10)。

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-10-20-Preview

9) 特定のフィールドのセットを取得します。

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-10-20-Preview

10) 特定のクエリ式に一致するドキュメントを取得します。

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-10-20-Preview

11) インデックスを検索し、ヒットを強調表示してフラグメントを返します。

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-10-20-Preview
    
12) インデックスを検索し、参照場所から近いものから遠いものの順に並べ替えてドキュメントを返します。

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-10-20-Preview

13) 2 つの距離スコアリング関数を含む "geo" という名前のスコアリング プロファイルがあるものとしてインデックスを検索します。1 つの関数は "currentLocation" という名前のパラメーターを定義し、もう 1 つは "lastLocation" という名前のパラメーターを定義しています。

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-10-20-Preview


<a name="LookupAPI"></a>
##ドキュメントの参照##

**Lookup Document** 操作は、Azure Search からドキュメントを取得します。これは、ユーザーが特定の検索結果をクリックして、そのドキュメントに関する詳細を検索するときに役立ちます。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters] 
    api-key: [admin key]
    
**要求**

サービス要求には HTTPS が必要です。**Lookup Document** 要求は、次のようにして作成できます。

    GET /indexes/[index name]/docs/key?[query parameters] 

または、従来の OData 構文をキー参照に使用することもできます。

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

要求 URI には、取得するインデックスとドキュメントを指定する[インデックス名]と[キー]が含まれます。一度に取得できるドキュメントは 1 つだけです。1 回の要求で複数のドキュメントを取得するには、**Search** を使用します。

**クエリ パラメーター**

`$select=[string]` (省略可能) - 取得するフィールドのコンマ区切りリスト。指定しない場合、または `*` に設定した場合は、スキーマで取得可能とマークされているすべてのフィールドが含まれます。

`api-version=[string]` (必須)。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

注: この操作の場合、`api-version` はクエリ パラメーターとして指定します。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービス URL に固有の文字列値です。**Lookup Document** 要求では、`api-key` に対して管理者キーまたはクエリ キーを指定できます。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` は、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

    {
      field_name: field_value (fields matching the default or specified projection)
    }
    
**例**

キー '2' を持つドキュメントを参照します。

    GET /indexes/hotels/docs/2?api-version=2014-10-20-Preview

OData 構文を使用して、キー '3' を持つドキュメントを参照します。

    GET /indexes('hotels')/docs('3')?api-version=2014-10-20-Preview

<a name="CountDocs"></a>
##Count Documents (Azure Search) (ドキュメントのカウント (Azure Search))##

**Count Documents** 操作は、検索インデックス内のドキュメントの数を取得します。`$count` 構文は、OData プロトコルの一部です。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]
    
**要求**

サービス要求には HTTPS が必要です。**Count Documents** 要求は、GET メソッドを使用して作成できます。

要求 URI の [index name] では、指定したインデックスのドキュメント コレクション内のすべての項目の数を返すようにサービスに指示します。

`api-version` パラメーターは必須です。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。

- `Accept`: この値は `text/plain` に設定する必要があります。
- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービス URL に固有の文字列値です。**Count Documents** 要求では、`api-key` に対して管理者キーまたはクエリ キーを指定できます。
 
要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` はどちらも、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答本文には、プレーン テキスト形式の整数として、カウント値が含まれます。

<a name="Suggestions"></a>
##検索候補##

**Suggestions** 操作は、部分的な検索入力に基づいて検索候補を取得します。通常は、ユーザーが検索語句を入力するときに、先行入力の候補を提供するために検索ボックスで使用されます。

Suggestions 要求はターゲット ドキュメントの検索候補を示すことを目的としているので、複数の候補ドキュメントが同じ検索入力と一致した場合、検索候補のテキストが繰り返される場合があります。`$select` を使用して (ドキュメント キーを含む) 他のドキュメント フィールドを取得し、各検索候補のソースであるドキュメントを示すことができます。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]


**要求**

サービス要求には HTTPS が必要です。**Suggestions** 要求は、GET メソッドを使用して作成できます。

要求 URI では、クエリするインデックスの名前を指定します。また、部分的に入力された検索語句もクエリ文字列に含めます。

**クエリ パラメーター**

`search=[string]` - クエリの検索候補を示すために使用する検索テキスト。3 文字以上 25 文字以下で指定する必要があります。

`highlightPreTag=[string]` (省略可能、既定値 = ``) - a string tag that prepends to search hits. Must be set with `highlightPostTag`)。URL の予約済みの文字は、パーセントでエンコードする必要があります (たとえば、# ではなく %23)。

`highlightPostTag=[string]` (省略可能、既定値 = ``) - a string tag that appends to search hits. Must be set with `highlightPreTag`)。URL の予約済みの文字は、パーセントでエンコードする必要があります (たとえば、# ではなく %23)。

`suggesterName=[string]` - (省略可能) インデックス定義の一部である `suggesters` コレクションで指定されているサジェスターの名前。このオプションを使用しないと、検索候補は `"suggestions": true` でマークされているフィールドに対して動作し、接頭辞一致のみをサポートする、前のバージョンの実装に基づきます。

`fuzzy=[boolean]` (省略可能、既定値 = false) - true に設定すると、検索テキストに置き換えられた文字または存在しない文字がある場合であっても、この API は検索候補を探します。あいまい一致で検索候補を検索すると低速で多くのリソースを消費するため、一部のシナリオではエクスペリエンスがよくなりますが、パフォーマンスは低下します。

`searchFields=[string]` (省略可能) - 指定した検索テキストを検索するフィールド名のコンマ区切りのリスト。対象のフィールドは、検索候補が有効になっている必要があります。

`$top=#` (省略可能、既定値 = 5) - 取得する検索候補の数。1 ～ 100 の数値を指定する必要があります。

`$filter=[string]` (省略可能) - 検索候補と考えられるドキュメントをフィルター処理する式。

`$orderby=[string]` (省略可能) - 結果を並べ替えるための式のコンマ区切りのリスト。各式は、フィールド名または `geo.distance()` 関数の呼び出しです。各式に続いて、昇順を示す `asc` または降順を示す `desc` を指定できます。既定値は昇順です。`$orderby` には 32 句の制限があります。

`$select=[string]` (省略可能) - 取得するフィールドのコンマ区切りリスト。指定しないと、ドキュメント キーと検索候補テキストのみが返されます。

`api-version=[string]` (必須)。有効な値は、`2014-07-31-Preview` または `2014-10-20-Preview` です。要求ごとに使用するバージョンを指定してバージョン固有の動作を取得できますが、ベスト プラクティスとしては、コード全体で同じバージョンを使用します。推奨されるバージョンは、一般的な使用の場合は `2014-07-31-Preview` です。試験的な機能を評価する場合は、`2014-10-20-Preview` を使用します。詳細については、「[Azure Search サービスのバージョン](http://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

注: この操作の場合、`api-version` はクエリ パラメーターとして指定します。

**要求ヘッダー**

以下では、必須と任意の要求ヘッダーについて説明します。

- `api-key`: `api-key` は Search サービスに対する要求の認証に使用されます。これはサービス URL に固有の文字列値です。**Suggestions** 要求では、`api-key` として管理者キーまたはクエリ キーを指定できます。

  要求 URL を作成するにはサービス名も必要です。サービス名と `api-key` はどちらも、Azure プレビュー ポータルのサービス ダッシュボードから取得できます。ページのナビゲーション ヘルプについては、「[Azure Search の使用](search-get-started.md)」を参照してください。

**要求本文**

ありません。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

予測オプションがフィールドの取得に使用された場合、配列の各要素に含められます。

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **例**

部分的な検索入力が「lux」である場合に、5 個の検索候補を取得します。

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2014-10-20-Preview





<!--HONumber=54--> 