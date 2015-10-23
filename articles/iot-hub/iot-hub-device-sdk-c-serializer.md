<properties
	pageTitle="C 用 Azure IoT デバイス SDK – シリアライザーの詳細 | Microsoft Azure"
	description="C 用 Azure IoT device SDK に含まれるシリアライザー ライブラリの詳細"
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="andrewmc"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# C 用 Microsoft Azure IoT デバイス SDK – シリアライザーの詳細

本シリーズの[最初の記事](iot-hub-device-sdk-c-intro.md)では、**C 用 Azure IoT デバイス SDK** を紹介しました。それに続く記事では、[**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md) を詳細に説明しました。今回の記事では、残りのコンポーネントである**シリアライザー** ライブラリについて詳細に説明し、SDK の対応範囲をすべて説明します。

入門記事では、**シリアライザー** ライブラリを使用して、IoT Hub にイベントを送信し、IoT Hub からメッセージを受信する方法について説明しました。この記事では、**シリアライザー** マクロ言語によるデータのモデル化方法を詳細に説明することで、さらにその内容を深めていきます。また、ライブラリがメッセージをシリアル化する方法 (場合によっては、シリアル化の動作を制御する方法) の詳細についても説明します。また、作成するモデルのサイズを決定するいくつかの変更可能なパラメーターについても説明します。

最後に、メッセージとプロパティの処理など、以前の記事で説明したトピックにも、もう一度触れます。ただし、読み進めていくとわかるように、これらの機能は、**シリアライザー** ライブラリを使用する場合でも、**IoTHubClient** ライブラリを使用する場合と同じように動作します。

次に示す内容は、すべて**シリアライザー** SDK サンプルに基づいています。よって、C 用 Azure IoT デバイス SDK に含まれている **simplesample\_amqp** アプリケーションと **simplesample\_http** アプリケーションを確認すると、理解に役立ちます。

## モデリング言語

このシリーズの[入門記事](iot-hub-device-sdk-c-intro.md)では、**simplesample\_amqp** アプリケーションで提供された例を使用して、**C 用 Azure IoT デバイス SDK** のモデリング言語を紹介しました。

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

見るとわかるように、モデリング言語は、C マクロに基づいています。常に、定義は **BEGIN\_NAMESPACE** で始まり、**END\_NAMESPACE** で終了します。通常は、名前空間には、会社か、またはこの例のように作業しているプロジェクトに関連した名前を付けます。

名前空間の内部で行われるのは、モデル定義です。この例では、風速計の 1 つのモデルを使用します。この場合もモデルに任意の名前を付けることができますが、通常は、デバイスまたは IoT Hub と交換するデータの種類に関連した名前を付けます。

モデルには、IoT Hub に入力できるイベントの定義 (*データ*) と IoT Hub から受信できるメッセージ (*アクション*) の定義が含まれます。例からわかるように、イベントには、型と名前があります。アクションには、名前と省略可能なパラメーター (それぞれ型を持つ) があります。

このサンプルで示していないのは、SDK がサポートする追加のデータ型です。これについては、次に説明します。

> IoT Hub では、デバイスが IoT Hub に送信するデータは*イベント*と呼ばれ、モデリング言語では、そのデータは*データ* (**WITH\_DATA** を使用して定義) と呼ばれることに注意してください。同じように、IoT Hub では、デバイスに送信するデータは*メッセージ*と呼ばれ、モデリング言語では、そのデータは*アクション* (**WITH\_ACTION** を使用して定義) と呼ばれることに注意してください。この記事では、これらの用語が区別されずに使われる場合があります。

### サポートされているデータ型

次のデータの型は、**シリアライザー** ライブラリを使用して作成されたモデルでサポートされます。

| 型 | 説明 |
|-------------------------|----------------------------------------|
| double | 倍精度浮動小数点数 |
| int | 32 ビット整数 |
| float | 単精度浮動小数点数 |
| long | long integer |
| int8\_t | 8 ビット整数 |
| int16\_t | 16 ビット整数型 |
| int32\_t | 32 ビット整数 |
| int64\_t | 64 ビット整数 |
| bool | ブール値 |
| ascii\_char\_ptr | ASCII 文字列 |
| EDM\_DATE\_TIME\_OFFSET | 日時オフセット |
| EDM\_GUID | GUID |
| EDM\_BINARY | binary |
| DECLARE\_STRUCT | 複合データ型 |

最後のデータ型から説明していきます。**DECLARE\_STRUCT** を使用すると、複合データ型を定義できます。複合データ型とは、その他のプリミティブ型を単にグループ化したものです。これにより、次のようなモデルを定義できます。

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

このモデルには、型 **TestType** のデータ イベントが 1 つだけ含まれています。また **TestType** は、複数のメンバーを含む複合型です。これらのメンバー全体が、**シリアライザー** モデリング言語によってサポートされているプリミティブ型を示します。

上のようなモデルを使用して、次のように IoT Hub に送信するためのコードを記述できます。

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

基本的に、**Test** 構造体の各メンバーに値を割り当ててから、**SendAsync** を呼び出して **Test** データ イベントをクラウドに送信します。**SendAsync** は、IoT Hub に 1 つのデータ イベントを送信するヘルパー関数です。

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
	unsigned char* destination;
	size_t destinationSize;
	if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) == 
	{
		// null terminate the string
		char* destinationAsString = (char*)malloc(destinationSize + 1);
		if (destinationAsString != NULL)
		{
			memcpy(destinationAsString, destination, destinationSize);
			destinationAsString[destinationSize] = '\0';
			IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
			if (messageHandle != NULL)
			{
				IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);	

				IoTHubMessage_Destroy(messageHandle);
			}
			free(destinationAsString);
		}
		free(destination);
	}
}
```

この関数は、指定されたデータ イベントをシリアル化し、**IoTHubClient\_SendEventAsync** を使用して、そのデータ イベントを IoT Hub に送信します。これは、以前の記事で詳細に説明したコードと同じコードです (**SendAsync** は、単に都合のよい関数にロジックをカプセル化するだけです)。

上のスニペットで使用されるもう 1 つのヘルパー関数は **GetDateTimeOffset** です。次のように、この関数は指定された時刻を型 **EDM\_DATE\_TIME\_OFFSET** の値に変換します。

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
	struct tm newTime;
	gmtime_s(&newTime, &time);
	EDM_DATE_TIME_OFFSET dateTimeOffset;
	dateTimeOffset.dateTime = newTime;
	dateTimeOffset.fractionalSecond = 0;
	dateTimeOffset.hasFractionalSecond = 0;
	dateTimeOffset.hasTimeZone = 0;
	dateTimeOffset.timeZoneHour = 0;
	dateTimeOffset.timeZoneMinute = 0;
	return dateTimeOffset;
}
```

上のコードを実行すると、次のメッセージが IoT Hub に送信されます。

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

シリアル化により JSON に変換されることに注意してしてください。JSON は、**シリアライザー** ライブラリによって生成される形式です。また、シリアル化された JSON オブジェクトの各メンバーとこのモデルで定義した **TestType** のメンバーが一致することにも注意してください。値も、コードで使用した値と正確に一致します。ただし、バイナリ データが Base64 でエンコードされていることに注意してください。"AQID" は、{0x01、0x02、0x03} の Base64 エンコードです。

この例からは、**シリアライザー** ライブラリを使用する利点がわかります。これにより、アプリケーションでシリアル化を明示的に処理しなくても、クラウドに JSON を送信することができます。必要なのは、このモデルにデータ イベントの値を設定し、クラウドにそれらのイベントを送信するためにシンプルな API を呼び出すことだけです。

上の情報を使用して、複合型など、サポートするデータ型の範囲が含まれるモデルを定義できます (必要な場合は、複合型の中に別の複合型を含めることもできます)。ただし、上の例によって生成されたシリアル化された JSON は、重要なポイントを提起します。それは、**シリアライザー** ライブラリを使用したデータの送信*方法*によって、JSON の形式が決定されるということです。このポイントについては、次で説明します。

## シリアル化の詳細

前のセクションでは、**シリアライザー** ライブラリによって生成される出力の例を紹介しました。このセクションでは、ライブラリがデータをシリアル化する方法およびシリアル化 API を使用してその動作を制御する方法について重点的に説明します。

シリアル化についての説明を進めるために、サーモスタットに基づく新しいモデルを使用します。その前に、扱うシナリオについての背景についていくつか説明します。

ここでモデル化するのは、気温と湿度を測定するサーモスタットです。データの各部分は、さまざまな方法で IoT Hub に送信されます。既定では、サーモスタットは温度のイベントを 2 分ごとに受信します。また湿度のイベントを 15 分ごとに受信します。どちらかのイベントが受信されるときに、そのイベントには、対応する温度または湿度の測定時刻を表すタイムスタンプが含まれている必要があります。

このシナリオでは、2 つの異なる方法でデータをモデル化し、シリアル化された出力に対するモデル化の影響について説明します。

### モデル 1

ここでは、上のシナリオをサポートするモデルの最初のバージョンを示します。

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

モデルには、**Temperature** と **Humidity** という 2 つのデータ イベントが含まれることに注意してください。これまでの例とは異なり、各イベントの型は、**DECLARE\_STRUCT** を使用して定義された構造体です。**TemperatureEvent** には温度の測定値とタイムスタンプが含まれており、**HumidityEvent** には湿度の測定値とタイムスタンプが含まれています。このモデルにより、自然な方法で上のシナリオのデータをモデル化できます。イベントをクラウドに送信するとき、温度/タイムスタンプのペア、または湿度/タイムスタンプのペアを送信します。

次のようなコードを使用して、クラウドに温度のイベントを送信できます。

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

サンプル コードにハード コーディングされた気温および湿度の値を使用しますが、サーモスタットの対応するセンサーをサンプリングしてこれらの値を実際に取得することを想像してください。

上のコードでは、以前に紹介した **GetDateTimeOffset** ヘルパーを使用します。後で理由を説明しますが、このコードでは、明示的にイベントのシリアル化とイベントの送信を分離します。上のコードでは、バッファーに温度のイベントをシリアル化します。また、**sendMessage** は、IoT Hub にイベントを送信するヘルパー関数 (**simplesample\_amqp** に含まれている) です。

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

このコードは、前のセクションで説明した **SendAsync** ヘルパーのサブセットであるため、ここでは改めて説明しません。

前のコードを実行して Temperature イベントを送信する場合、このシリアル化されたイベントの形式は、IoT Hub に送信されます。

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

送信するのは **TemperatureEvent** の温度で、この構造体には、**Temperature** メンバーと **Time** メンバーが含まれています。これは、シリアル化されたデータに直接反映されます。ここまで、特に問題はありません。

同様に、次のコードで湿度のイベントを送信できます。

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

IoT Hub に送信されるシリアル化された形式は、次のようになります。

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

これも、想定どおりの処理です。

上のモデルでは、簡単に他のイベントを追加できることがわかります。**DECLARE\_STRUCT** を使用して追加の構造体を定義し、**WITH\_DATA** を使用して、モデルに対応するイベントを含めるだけです。

ただし、今度は別の構造体を使用して同じデータが含まれるように、モデルを変更します。

### モデル 2

上のモデルに代わる、この別のモデルについて考えてみます。

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

このモデルでは、**DECLARE\_STRUCT** マクロを削除したうえで、モデリング言語の単純型を使用して、このシナリオのデータ項目の定義だけを行います。

少しの間だけ、**Time** イベントは無視します。それを無視した場合、**Temperature** を受信するコードは次のとおりです。

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードでは、次のシリアル化されたイベントを IoT Hub に送信します。

```
{"Temperature":75}
```

Humidity イベントを送信するためのコードは、次のようになります。

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードは、イベントを IoT Hub に送信します。

```
{"Humidity":45}
```

ここまでも、想定どおりの処理です。しかし、SERIALIZE マクロの使用方法を変更した場合は、どうなるでしょうか。

この **SERIALIZE** マクロは、複数のデータ イベントを引数として取得できます。これにより、1 回の呼び出しで、**Temperature** および **Humidity** イベントを一緒にシリアル化し、IoT Hub に送信できます。

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

このコードを実行すると、2 つのデータ イベントが IoT Hub に送信されると考える人もいるでしょう。

[

{"Temperature":75},

{"Humidity":45}

]

つまり、このコードは **Temperature** と **Humidity** を別々に送信することと同じであり、便宜上、同じ呼び出しで両方のイベントを **SERIALIZE** に渡しているにすぎないという考え方です。しかし、そうではありません。上のコードは、次の 1 つのデータ イベントを IoT Hub に送信します。

{"Temperature":75, "Humidity":45}

このことは奇妙に思えるかもしれません。その理由は、モデルでは次のように **Temperature** と **Humidity** が 2 つの*別々の*イベントとして定義されているためです。

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

さらに重要なことに、**Temperature** と **Humidity** が同じ構造体にあるこれらのイベントはモデル化されていません。

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

このモデルを使用すれば、同じシリアル化されたメッセージで **Temperature** と **Humidity** を送信する方法を簡単に理解できます。ただし、モデル 2 を使用して両方のデータ イベントを **SERIALIZE** に渡す場合にこの方法が機能する理由がわかりにくく感じるかもしれません。

この動作は、**シリアライザー** ライブラリが仮定している条件を理解すると、わかりやすくなります。このことを理解するために、モデルに話を戻しましょう。

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

モデルをオブジェクト指向の用語を使って考えてみます。ここで、物理デバイス (サーモスタット) をモデル化し、そのデバイスには、**Temperature** と **Humidity** のような属性があるとします。

必要に応じて、次のようなコードを使用してモデル全体の状態を送信できます。

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Temperature、Humidity、Time の値が設定されていると仮定すると、次のようなイベントが IoT Hub に送信されます。

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

ただし、モデルの*一部*のプロパティのみをクラウドに送信する場合も多いでしょう (このことは、特に、モデルに多数のデータ イベントが含まれている場合に当てはまります)。そのような場合には、前の例のようにデータ イベントのサブセットのみを送信すると便利です。

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

これにより、モデル 1 のように **Temperature** および **Time** メンバーを含む **TemperatureEvent** を定義した場合とまったく同じシリアル化されたイベントが生成されます。ただし、この場合は、異なるモデル (モデル 2) を使用して、まったく同じシリアル化されたイベントを生成できました。これは、**SERIALIZE** を別の方法で呼び出したためです。

ここでの要点は、複数のデータ イベントを **SERIALIZE** に渡す場合、各イベントは、1 つの JSON オブジェクトのプロパティとして見なされるということです。

最適なアプローチは、モデルについての考え方によって変わります。"イベント" をクラウドに送信し、定義された一連のプロパティを各イベントに含めるという考え方であれば、最初のアプローチが非常に役立ちます。その場合は、**DECLARE\_STRUCT** を使用して各イベントの構造体を定義し、**WITH\_DATA** マクロを使用してモデルに構造体を含めます。その後、上の最初の例で行ったように各イベントを送信します。このアプローチでは、**SERIALIZER** に 1 つのデータ イベントだけを渡します。

オブジェクト指向でモデルを考える場合は、2 番目のアプローチが適切である可能性があります。この場合、**WITH\_DATA** を使用して定義した要素は、オブジェクトの "プロパティ" です。この場合、クラウドに送信する "オブジェクトの" 状態の量に応じて、希望するイベントのサブセットはすべて **SERIALIZE** に渡すことができます。

どちらのアプローチも正しいとか、間違っているとかということはありません。**シリアライザー** ライブラリのしくみに注意し、ニーズに最適なモデリング アプローチを選択します。

## メッセージの処理

これまで、この記事では IoT Hub への送信についてのみ説明し、メッセージの受信については取り上げてきませんでした。その理由は、[以前の記事](iot-hub-device-sdk-c-intro.md)でメッセージの受信について理解する必要がある内容をほとんど説明したためです。メッセージを処理するには、メッセージのコールバック関数を登録するということを、その記事から思い出してください。

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

そのうえで、メッセージを受信したときに呼び出されるコールバック関数を記述します。

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

**IoTHubMessage** のこの実装では、モデル内の各アクションの特定の関数を呼び出します。たとえば、モデルで次のアクションが定義されているとします。

```
WITH_ACTION(SetAirResistance, int, Position)
```

この場合、次のシグネチャを持つ関数を定義する必要があります。

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** は、デバイスにそのメッセージが送信されると呼び出されます。

次のようなメッセージのシリアル化されたバージョンについては、まだ説明していませんでした。つまり、**SetAirResistance** メッセージをデバイスに送信する場合、どのような形式になるのかという点です。

デバイスにメッセージを送信する場合、Azure IoT サービス SDK を使用します。ただし、特定のアクションを呼び出すために送信する文字列は知っておく必要があります。メッセージを送信する一般的な形式は次のようになります。

```
{"Name" : "", "Parameters" : "" }
```

2 つのプロパティを含むシリアル化された JSON オブジェクトを送信します。ここで、**Name** はアクション (メッセージも呼ばれる) の名前で、**Parameters** にはそのアクションのパラメーターが含まれています。

たとえば、**SetAirResistance** を呼び出すには、デバイスに次のメッセージを送信できます。

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

アクション名は、モデルで定義されているアクションと正確に一致する必要があります。同様に、パラメーター名も一致する必要があります。また、大文字と小文字が区別されることにも注意してください。**Name** と **Parameters** は常に大文字です。モデル内のアクションの名前とパラメーターの大文字と小文字が一致するようにしてください。この例では、アクションの名前は "SetAirResistance" で、"setairresistance" ではありません。

ここからは、**シリアライザー** ライブラリを使用してイベントを送信し、メッセージを受信する場合に知る必要がある内容をすべて説明します。ただし、その前に、モデルの大きさを制御する構成可能ないくつかのパラメーターについて説明します。

## マクロの構成

**シリアライザー** ライブラリを使用する場合に、注意が必要な SDK の重要部分は次のとおりです。

> .\\c\\common\\tools\\macro\_utils\_h\_generator

このフォルダーには、**macro\_utils\_h\_generator.sln** と呼ばれる Visual Studio ソリューションがあります。

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

このソリューションのプログラムは、\\c\\common\\inc ディレクトリにある **macro\_utils.h** ファイルを生成します。SDK には、既定の macro\_utils.h ファイルが含まれています。ただし、このソリューションでは、いくつかのパラメーターを変更して、これらのパラメーターに基づいて、ヘッダー ファイルを再作成することができます。

考慮する必要がある 2 つの重要なパラメーターは、**nArithmetic** と **nMacroParameters** で、これらは macro\_utils.tt 内の次の 2 行で定義されています。

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

上の値は、SDK に含まれている既定のパラメーターです。各パラメーターには、次のような意味があります。

-   nMacroParameters – 1 つの DECLARE\_MODEL マクロ定義に使用できるパラメーターの数を制御します。

-   nArithmetic – モデルで使用できるメンバーの*合計*数を制御します。

これらのパラメーターは、モデルの規模を管理するため重要です。たとえば、次のモデルの定義について考えてみます。

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

前に述べたように、**DECLARE\_MODEL** は単なる C マクロです。モデルの名前と **WITH\_DATA** ステートメント (さらに別のマクロ) は **DECLARE\_MODEL** のパラメーターです。**nMacroParameters** では、**DECLARE\_MODEL** に含めることのできるパラメーターの数を定義します。実質的に、これによって使用可能なデータ イベントおよびアクション宣言の数が定義されます。124 という既定の制限値の場合、約 60 個のアクションとデータ イベントの組み合わせを使用してモデルを定義できます。この制限を超えようとすると、次のようなコンパイラ エラーが発生します。

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

**NArithmetic** パラメーターは、アプリケーションよりもマクロ言語の内部動作に関連します。ただし、大まかに言えば、このパラメーターはモデルに含めることができるメンバー (**DECLARE\_STRUCT** マクロなど) の*合計*数を制御すると見なすことができます。次のようなコンパイラ エラーが表示されるようになったら **nArithmetic** を増やす必要があります。

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

これらのパラメーターを変更する場合は、macro\_utils.tt ファイル内の値を変更し、macro\_utils\_h\_generator.sln ソリューションを再コンパイルし、コンパイル済みプログラムを実行します。この操作を行うと、新しい macro\_utils.h ファイルが生成され、.\\common\\inc ディレクトリに配置されます。

注意が必要な重要な点は、これらの値を十分に増やすと、コンパイラの制限を超えることができるいうことです。この時点で、考慮する主要なパラメーターは、**nMacroParameters** です。C99 仕様では、マクロ定義内で最低 127 のパラメーターが指定できると規定しています。Microsoft コンパイラはこの仕様に厳密に従っています (よって 127 という制限があります)。そのため、既定値を超えて **nMacroParameters** を増やすことはできません。一方、他のコンパイラには、このことが可能なものがあります (たとえば、GNU コンパイラは、より大きな制限をサポートします)。

ここまでで、**シリアライザー** ライブラリを使用したコードの記述方法について、知っておくべきすべてのことを説明しました。説明を終える前に、疑問となりそうな以前の記事のいくつかのトピックについて、もう一度説明します。

## 下位レベルの API

この記事で詳しく説明したサンプル アプリケーションは、**simplesample\_amqp** です。このサンプルでは、上位レベル ("LL" 以外) API を使用してイベントを送信し、メッセージを受信します。これらの API を使用する場合、バックグラウンド スレッドが実行され、イベントの送信とメッセージの受信の両方を処理します。ただし、必要な場合、下位レベル (LL) API を使用してこのバックグラウンド スレッドを排除し、イベントの送信またはクラウドからのメッセージの受信のタイミングを明示的に制御できます。

[前回の記事](iot-hub-device-sdk-c-iothubclient.md)の説明のとおり、以下のように、上位 API で構成される関数のセットがあります。

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

これらの API は、**simplesample\_amqp** で示されます。

ただし、下位レベル API に類似したセットがあります。

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

ここで重要なのは、下位レベル API が、以前の記事での説明とまったく同じ動作をすることです。イベントの送信とメッセージの受信を処理するバックグラウンド スレッドが必要な場合、最初の API のセットを使用できます。データの送信と IoT Hub からのデータの受信のタイミングを明示的に制御する場合は、2 番目の API のセットを使用します。どちらの API のセットも**シリアライザー** ライブラリを使用して同じように正しく動作します。

**シリアライザー** ライブラリと一緒に下位レベル API を使用する方法の例については、**simplesample\_http** アプリケーションを参照してください。

## 関連トピック

これ以外に、もう一度説明する必要があるトピックとして、プロパティの処理、代替デバイスの資格情報の使用、および構成オプションがあります。これらは、すべて[前回の記事](iot-hub-device-sdk-c-iothubclient.md)で説明したトピックです。ここでの要点は、これらの機能はすべて、**シリアライザー ライブラリ**を使用する場合でも、**IoTHubClient** ライブラリの場合と同じ動作をするということです。たとえば、プロパティをモデルのイベントにアタッチする場合、前に説明した同じ方法で、**IoTHubMessage\_Properties** と **Map**\_ * * AddorUpdate * * を使用します。

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

イベントが**シリアライザー** ライブラリにより生成されたか、または **IoTHubClient** ライブラリを使用して手動で作成されたかは関係ありません。

代替デバイスの資格情報については、**IoTHubClient\_LL\_Create** を使用すると **IoTHubClient\_CreateFromConnectionString** と同じように動作して **IOTHUB\_CLIENT\_HANDLE** が割り当てられます。

最後に、**シリアライザー** ライブラリを使用する場合、**IoTHubClient** ライブラリを使用した場合と同様に、**IoTHubClient\_LL\_SetOption** を使用して構成オプションを設定できます。

**シリアライザー** ライブラリに固有の補助機能の 1 つに、初期化 API があります。ライブラリの使用を開始できるようにするには、**serializer\_init** を呼び出す必要があります。

```
serializer_init(NULL);
```

これは、**IoTHubClient\_CreateFromConnectionString** を呼び出す直前に実行されます。

同様に、ライブラリの操作を終了するには、通常は最後に **serializer\_deinit** を呼び出します。

```
serializer_deinit();
```

それ以外の場合、上に示したその他の機能はすべて、**シリアライザー** ライブラリの場合でも **IoTHubClient** ライブラリの場合と同じように動作します。これらのいずれかのトピックの詳細については、このシリーズの[前回の記事](iot-hub-device-sdk-c-iothubclient.md)を参照してください。

## 次のステップ

この記事では、**C 用 Azure IoT デバイス SDK** に含まれている**シリアライザー** ライブラリの独自の側面について詳細に説明しました。説明した情報により、モデルを使用してイベントを送信したり、IoT Hub からメッセージを受信したりする方法についての理解が深まります。

また、この記事は、**C 用 Azure IoT デバイス SDK** を使用したアプリケーションの開発方法に関する 3 部構成のシリーズの最終回でもあります。この記事を読むことで、API の概要だけでなく、API のしくみについて理解するための十分な情報を得ることができます。さらに詳細な情報が必要な場合は、上で説明されていない SDK のサンプルもいくつか用意されています。さらに、詳細を知るために役立つリソースとして、[SDK ドキュメント](https://github.com/Azure/azure-iot-sdks)も参照してください。

<!---HONumber=Oct15_HO1-->