<properties 
	pageTitle="データセットの作成" 
	description="Azure Data Factory のデータセットを理解し、その作成方法について学習します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="spelluru"/>

# データセット

## 説明
データセットはデータを論理的に説明するものです。記述されるデータは、単純なバイト、CSV ファイルのような半構造化データから、リレーショナル テーブルやモデルまでさまざまです。データにアクセスするためのメカニズム (アドレス、プロトコル、認証方式) はリンクされたサービスで定義され、データセット定義で参照されます。

## 構文

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**構文の詳細**

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| 名前 | データセットの名前 | あり | 該当なし |
| Structure | <p>データセットのスキーマ</p><p>詳細については、「[データセット構造](#Structure)」を参照してください。</p> | 番号 | 該当なし |
| 型 | データセットの型 | あり | 該当なし |
| typeProperties | <p>選択した種類に対応するプロパティ</p><p>サポートされている型とそのプロパティについては、「[データセットの型](#Type)」セクションを参照してください。</p> | あり | 該当なし |
| 外部 | データセットを Data Factory パイプラインにより明示的に生成するかどうかを指定するブール型のフラグ | いいえ | false | 
| Availability | <p>データセット生成の処理時間枠またはスライシング モデルを定義します。</p><p>詳細については、「[データセットの可用性](#Availability)」トピックを参照してください。</p><p>データセット スライシング モデルの詳細については、「[スケジュールと実行](data-factory-scheduling-and-execution.md)」という記事を参照してください。</p> | あり | 該当なし
| policy | データセット スライスで満たさなければならない基準または条件を定義します。<p>詳細については、「[データセット ポリシー](#Policy)」トピックを参照してください。</p> | いいえ | 該当なし |

### 例

以下は **Azure SQL データベース**に「**MyTable**」という名前のテーブルを表示するデータセットの例です。Azure SQL データベースの接続文字列はこのデータセットで参照される **AzureSqlLinkedService** で定義されます。このデータセットは毎日スライスされます。

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>データセット構造

**構造**セクションでは、データセットのスキーマをアサートします。列の集合と列の種類が含まれます。各列には次のプロパティが含まれます。

プロパティ | 説明 | 必須 | 既定値  
-------- | ----------- | -------- | -------
name | 列の名前です。 | いいえ | 該当なし 
type | 列のデータ型です。 | いいえ | 該当なし 

### 例

次の例では、データセットに slicetimestamp、projectname、pageviews の 3 つの列があります。

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> データセットの型

サポートされるデータ ソースとデータセットの型が並んでいます。データセットの型と構成の詳細については、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事の中のコネクタに関するトピックを参照してください。

## <a name="Availability"></a> データセットの可用性

データセットの「可用性」セクションにより、データセット生成の処理時間枠またはスライシング モデルが定義されます。データセットのスライシングと依存性のモデルについては、「データセット スライス」に関するトピックを参照してください。

| プロパティ | 説明 | 必須 | 既定値 |
| -------- | ----------- | -------- | ------- |
| frequency | データセット スライス生成の時間単位を指定します。<p>**サポートされている頻度**: 分、時間、日、週、月</p> | あり | 該当なし |
| interval | 頻度の乗数を指定します。<p>「頻度 x 間隔」により、スライスが生成される頻度が決定されます。</p><p>データセットを時間あたりでスライスする場合、**[頻度]** を **[時間]** に、**[間隔]** を**[1]** に設定します。</p><p>**注記:** 頻度に分を指定する場合、間隔を 15 未満にすることをお勧めします。</p> | あり | 該当なし |
| style | スライスを間隔の始め/終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>頻度を月に設定し、スタイルを EndOfInterval に設定する場合、スライスは月の最終日に生成されます。スタイルを StartOfInterval に設定する場合、スライスは月の最初の日に生成されます。<.p><p>頻度を日に設定し、スタイルを EndOfInterval に設定する場合、スライスは 1 日の最後の時間に生成されます。</p>頻度を時間に設定し、スタイルを EndOfInterval に設定する場合、スライスは 1 時間の終わりに生成されます。たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。</p> | いいえ | EndOfInterval |
| anchorDateTime | データセット スライスの境界を計算するためにスケジューラにより使用される時間の絶対位置を定義します。 <p>**注:** AnchorDateTime に頻度より詳細な日付部分が含まれている場合、その詳細な部分は無視されます。たとえば、**間隔**が**時間**単位の場合 (頻度: 時間、間隔: 1)、**AnchorDateTime** に**分と秒**が含まれる場合、AnchorDateTime の**分と秒**部分は無視されます。 </p>| いいえ | 01/01/0001 |
| Offset | すべてのデータセット スライスの開始と終了がシフトされる時間帯です。 <p>**注:** anchorDateTime とオフセットの両方が指定されている場合、結果的にシフトが結合されます。</p> | いいえ | 該当なし |

### anchorDateTime 例

**例:** 2007-04-19T08:00:00 に開始する 23 時間のデータセット スライス

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDataTime":"2007-04-19T08:00:00"	
	}


### offset 例

既定の真夜中の代わりに朝の 6 時に開始する毎日のスライス。

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

この場合、SliceStart は 6 時間シフトし、午前 6 になります。

12 か月スケジュールの場合 (頻度 = 月、間隔 = 12)、「オフセット: 60.00:00:00」は毎年の 3 月 2 日または 3 日 (スタイルが StartOfInterval の場合、年の初めから 60 日後であり、うるう年かどうかにより 2 日または 3 日になる) を意味します。



## <a name="Policy"></a>データセット ポリシー

データセットのポリシー セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。

### 検証ポリシー

| ポリシー名 | 説明 | 適用先 | 必須 | 既定値 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Azure BLOB のデータが最小サイズ要件 (メガバイト単位) を満たすことを検証します。 | Azure BLOB | いいえ | 該当なし |
|minimumRows | Azure SQL データベースまたは Azure テーブルのデータが最小行数が含まれていることを検証します。 | <ul><li>Azure SQL データベース</li><li>Azure テーブル</li></ul> | いいえ | 該当なし

#### 例

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### ExternalData ポリシー

外部データセットは Data Factory のパイプライン実行で生成されないデータセットです。データセットが「外部」としてマークされている場合、データセット スライス可用性の動作を変更するように ExternalData ポリシーを定義できます。

| 名前 | 説明 | 必須 | 既定値 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 特定のスライスの外部データの可用性チェックを遅らせる時間。たとえば、データを時間単位で利用する場合、外部データが実際に利用できることと、該当するスライスが「準備完了」であることの確認を dataDelay で遅らせることができます。<p>現在時刻にのみ適用されます。現在午後 1 時で、この値が 10 分の場合、検証は午後 1 時 10 分に始まります。</p><p>この設定は過去のスライスには適用されません。スライスの終了時刻と dataDelay を足したものが「現在」より前になるスライスは遅延なく処理されます。</p> | いいえ | 0 |
| retryInterval | エラーと次の再試行の間の待機時間です。現在の時間に適用されます。前の試行に失敗した場合、最後の試行からこの時間が経過するまで待機します。<p>現在午後 1時で最初の試行を開始します。最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は「1:00pm + 1 分 (チェック時間) + 1 分 (再試行間隔) = 1:02pm」になります。</p><p>過去のスライスの場合、遅延はありません。再試行はすぐに実行されます。</p> | いいえ | 00:01:00 (1 分) | 
| retryTimeout | 各再試行のタイムアウト。<p>これが 10 分に設定されている場合、検証を 10 分以内に完了する必要があります。検証に 10 分以上がかかる場合、再試行はタイムアウトします。</p><p>検証のすべての試行がタイムアウトした場合、スライスに「TimedOut」のマークが付きます。</p> | いいえ | 00:10:00 (10 分) |
| maximumRetry | 外部データの可用性の確認回数です。許容される最大値は 10 です。 | いいえ | 3 | 











  

<!---HONumber=August15_HO6-->