---
title: Azure IoT Hub クエリ言語について | Microsoft Docs
description: 開発者ガイド - デバイス/モジュール ツインとジョブに関する情報を IoT Hub から取得するための、SQL のような IoT Hub クエリ言語の説明。
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 321d70a04e3c524e578a01e8531d63733d088c3f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444186"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>デバイス ツイン、モジュール ツイン、ジョブ、メッセージ ルーティングの IoT Hub クエリ言語

IoT Hub には SQL に似た強力な言語が備わっており、[デバイス ツイン][lnk-twins]や[ジョブ][lnk-jobs]、および[メッセージ ルーティング][lnk-devguide-messaging-routes]に関する情報を取得できます。 この記事で取り扱う内容は次のとおりです。

* IoT Hub のクエリ言語の主な機能の説明
* 言語の詳しい説明

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>デバイス ツインとモジュール ツインのクエリ
[デバイス ツイン][lnk-twins]とモジュール ツインには、任意の JSON オブジェクトをタグおよびプロパティとして含めることができます。 IoT Hub では、すべてのツイン情報を含む 1 つの JSON ドキュメントとしてデバイス ツインとモジュール ツインにクエリを実行できます。
たとえば、IoT Hub のデバイス ツインに、次の構造があるとします (モジュール ツインも同様で、追加の moduleId があるだけです)。

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>デバイス ツイン クエリ

IoT Hub はデバイス ツインを **devices** という名前のドキュメント コレクションとして公開します。
したがって、次のクエリでは、デバイス ツインのセット全体が取得されます。

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT Hub SDK][lnk-hub-sdks] では、大量の結果をページングできます。

IoT Hub は、任意の条件でフィルター処理してデバイス ツインを取得できます。 たとえば、**location.region** タグが **US** に設定されているデバイス ツインを受け取るには、次のクエリを使います。

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

ブール演算子と算術比較もサポートされています。 たとえば、US に存在し、1 分以上の間隔でテレメトリを送信するように構成されているデバイス ツインを取得するには、次のクエリを使います。

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

必要に応じて、**IN** や **NIN** (含まれない) 演算子とともに配列定数も使用できます。 たとえば、WiFi または有線接続を報告するデバイス ツインを取得するには、次のクエリを使います。

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

特定のプロパティを含むすべてのデバイス ツインを識別する必要がある場合があります。 IoT Hub では、この目的で関数 `is_defined()` がサポートされています。 たとえば、`connectivity` プロパティが定義されているデバイス ツインを取得するには、次のクエリを使います。

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

フィルター処理機能の詳細なリファレンスは、「[WHERE 句][lnk-query-where]」セクションをご覧ください。

グループ化と集計もサポートされています。 たとえば、各テレメトリ構成状態のデバイスの数を調べるには、次のクエリを使います。

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

このグループ化クエリは、次の例のような結果を返します。

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

この例では、3 つのデバイスが正常な構成を報告し、2 つは構成を適用中であり、1 つがエラーを報告しています。

プロジェクション クエリを使用すると、開発者は必要なプロパティのみを返すことができます。 たとえば、すべての切断されているデバイスの最後のアクティビティ時間を取得するには、次のクエリを使用します。

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>モジュール ツイン クエリ

モジュール ツインに対するクエリはデバイス ツインに対するクエリと似ていますが、("from devices" の代わりに) 異なるコレクション/名前空間を使用してクエリを実行できます

```sql
SELECT * FROM devices.modules
```

デバイスと devices.modules コレクション間の結合は許可されません。 デバイス間でモジュール ツインのクエリを実行する場合は、タグに基づいて行います。 このクエリでは、スキャン中状態のすべてのデバイスのすべてのモジュール ツインが返されます。

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

このクエリでは、指定したデバイスのサブセット上のみでスキャン中状態のすべてのモジュール ツインが返されます。

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>C# の例
クエリ機能は **RegistryManager** クラスの [C# service SDK][lnk-hub-sdks] で公開されます。
簡単なクエリの使用例を次に示します。

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

**query** オブジェクトは、ページ サイズ (最大 100) を使用してインスタンス化されます。 **GetNextAsTwinAsync** メソッドを複数回呼び出すと、複数のページが取得されます。

query オブジェクトは、クエリに必要な逆シリアル化オプションに応じて、複数の **Next** 値を公開します。 たとえば、デバイス ツインまたはジョブ オブジェクト、プロジェクションを使用する場合のプレーンな JSON などです。

### <a name="nodejs-example"></a>Node.js の使用例
クエリ機能は **Registry** オブジェクトの [Azure IoT service SDK for Node.js][lnk-hub-sdks] で公開されます。
簡単なクエリの使用例を次に示します。

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

**query** オブジェクトは、ページ サイズ (最大 100) を使用してインスタンス化されます。 **nextAsTwin** メソッドを複数回呼び出すと、複数のページが取得されます。

query オブジェクトは、クエリに必要な逆シリアル化オプションに応じて、複数の **Next** 値を公開します。 たとえば、デバイス ツインまたはジョブ オブジェクト、プロジェクションを使用する場合のプレーンな JSON などです。

### <a name="limitations"></a>制限事項

> [!IMPORTANT]
> クエリの結果には、デバイス ツインの最新の値に関連し、数分の遅れが生じることがあります。 ID を使用して個々のデバイス ツインにクエリを実行する場合は、デバイス ツインの取得 API を使用します。 この API には常に最新の値が含まれ、調整の上限は高くなっています。

現時点では、比較はプリミティブ型間 (オブジェクトなし) でのみサポートされます。たとえば、`... WHERE properties.desired.config = properties.reported.config` は、これらのプロパティがプリミティブ値を持つ場合にのみサポートされます。

## <a name="get-started-with-jobs-queries"></a>ジョブのクエリの概要

[ジョブ][lnk-jobs]には、一連のデバイスで演算子を実行する機能が備わっています。 各デバイス ツインにはジョブに関する情報が含まれます。これは、**jobs**.という名前のコレクションの一部です。
論理的には、次のようになります。

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

現在のところ、このコレクションには IoT Hub のクエリ言語の **devices.jobs** でクエリを実行できます。

> [!IMPORTANT]
> 現時点では、デバイス ツインにクエリを実行した場合、ジョブのプロパティは返されません。 つまり、クエリに 'FROM devices' が含まれる場合です。 ジョブのプロパティに直接アクセスするには、`FROM devices.jobs` を使用してクエリを実行する必要があります。
>
>

たとえば、1 つのデバイスに影響を与えるすべてのジョブ (過去のジョブおよびスケジュールが設定されたジョブ) を取得するには、次のクエリを使用することができます。

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

このクエリによって、返されたそれぞれのジョブのデバイス固有の状態 (および、ダイレクト メソッドの応答) がどのように適用されるのかをご確認ください。
さらに、**devices.jobs** コレクションのオブジェクトのすべてのプロパティに対して任意のブール条件でフィルター処理をすることもできます。
たとえば、特定のデバイスに対して 2016 年 9 月以降に作成されて完了したデバイス ツイン更新ジョブをすべて取得するには、次のクエリを使います。

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

また、1 つのジョブによるデバイスごとの結果を取得することもできます。

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>制限事項
現在のところ、**devices.jobs** に対するクエリはサポートされていません。

* プロジェクション。`SELECT *` のみ実行可能
* 条件。ジョブのプロパティ以外にデバイス ツインを参照するもの (上述のセクションを参照)
* 集計の実行。カウント、平均、グループ化など

## <a name="device-to-cloud-message-routes-query-expressions"></a>デバイスからクラウドへのメッセージ ルートのクエリ式

[デバイスからクラウドへのルート][lnk-devguide-messaging-routes]を使用して、デバイスからクラウドへのメッセージをさまざまなエンドポイントに送信するように IoT Hub を構成することができます。 送信は、個々のメッセージに対して評価される式に基づいて行われます。

ルートの[条件][lnk-query-expressions]では、ツインおよびジョブ クエリの条件と同じ IoT Hub クエリ言語を使用します。 ルート条件は、メッセージ ヘッダーと本文で評価されます。 ルーティング クエリ式には、メッセージ ヘッダーのみ、メッセージ本文のみ、またはその両方が含まれることがあります。 IoT Hub では、メッセージをルーティングするために、ヘッダーとメッセージ本文に対して特定のスキーマが想定されています。 次のセクションでは、IoT Hub が適切にルーティングするために必要なことについて説明します。

### <a name="routing-on-message-headers"></a>メッセージ ヘッダーでのルーティング

IoT Hub は、メッセージのルーティングのために、メッセージ ヘッダーの次の JSON 表現を想定しています。

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

メッセージのシステム プロパティには、`'$'` シンボルが付きます。
ユーザー プロパティは、常にその名前でアクセスされます。 ユーザー プロパティの名前が、システム プロパティと一致する場合 (`$contentType` など)、とユーザー プロパティは、`$contentType` 式で取得されます。
システム プロパティは、常にかっこ `{}` を使用してアクセスできます。たとえば、`{$contentType}` 式を使用してシステム プロパティ `contentType` にアクセスできます。 かっこで囲まれたプロパティ名は、常に対応するシステム プロパティを取得します。

プロパティ名では大文字と小文字は区別されません。

> [!NOTE]
> メッセージのプロパティはすべて文字列です。 [開発者ガイド][lnk-devguide-messaging-format]に記載されているように、システムのプロパティは現在クエリで使用できません。
>

たとえば、`messageType` プロパティを使用する場合、すべてのテレメトリを 1 つのエンドポイントにルーティングし、すべての警告を別のエンドポイントにルーティングすることを望むかもしれません。 次の式を記述してテレメトリをルーティングします。

```sql
messageType = 'telemetry'
```

また、次の式を記述して警告メッセージをルーティングします。

```sql
messageType = 'alert'
```

ブール式と関数もサポートされています。 この機能により、例えば次のように重大度レベルを区別できます。

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

サポートされている演算子と関数の完全な一覧については、「[式と条件][lnk-query-expressions]」セクションを参照してください。

### <a name="routing-on-message-bodies"></a>メッセージ本文でのルーティング

IoT Hub は、メッセージ本文が、UTF-8、UTF-16、または UTF-32 でエンコードされた適切な形式の JSON である場合のみ、メッセージ本文のコンテンツに基づいてルーティングできます。 メッセージのコンテンツの種類を `application/json` に設定します。 コンテンツのエンコードを、メッセージ ヘッダーでサポートされているいずれかの UTF エンコードに設定します。 いずれかのヘッダーが指定されていない場合、IoT Hub は、メッセージに対して、本文を含むクエリ式を評価しようとしません。 メッセージが JSON メッセージでない場合、またはメッセージでコンテンツの種類とコンテンツのエンコーディングを指定しない場合でも、メッセージ ルーティングを使用して、メッセージ ヘッダーに基づいてメッセージをルーティングできます。

次の例では、正しい形式でエンコードされた JSON 本文を持つメッセージを作成する方法を示します。

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

クエリ式で `$body` を使用して、メッセージをルーティングできます。 クエリ式で、簡単な本文参照、本文配列参照、または複数の本文参照を使用できます。 クエリ式で、本文参照とメッセージ ヘッダー参照を組み合わせることもできます。 たとえば、以下はすべて有効なクエリ式です。

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>IoT Hub クエリの基礎
IoT Hub のすべてのクエリは、SELECT 句と FROM 句、およびオプションの WHERE 句と GROUP BY で構成されます。 各クエリは JSON ドキュメントのコレクション (デバイス ツインなど) で実行されます。 FROM 句は (**devices** や **devices.jobs**) で繰り返されるドキュメント コレクションを示します。 次に、WHERE 句でフィルター処理が適用されます。 集計により、この手順の結果は GROUP BY 句の指定に従ってグループ化されます。 グループごとに、SELECT 句で指定された行が生成されます。

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM 句
**FROM <from_specification>** 句には、デバイス ツインのクエリを実行するための **FROM devices** とデバイスの詳細ごとにジョブのクエリを実行するための **FROM devices.jobs** の 2 つの値のみを使用できます。

## <a name="where-clause"></a>WHERE 句
**WHERE <filter_condition>** 句はオプションです。 WHERE 句は、FROM コレクションの JSON ドキュメントを結果に含めるために満たす必要がある条件を指定します。 結果に含めるためには、指定された条件についてすべての JSON ドキュメントが "true" と評価される必要があります。

使用できる条件は、「[式と条件][lnk-query-expressions]」セクションに記載されています。

## <a name="select-clause"></a>SELECT 句
**SELECT <select_list>** は必須であり、クエリで取得する値の種類を指定します。 これは、新しい JSON オブジェクトを生成するために使用する JSON 値を指定します。
FROM コレクションのフィルター処理されたサブセット (さらにオプションでグループ化されたもの) の各要素に対して、プロジェクション フェーズでは新しい JSON オブジェクトが生成されます。 このオブジェクトは、SELECT 句で指定した値で構築されます。

SELECT 句の文法を次に示します。

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**attribute_name** は、FROM コレクションの JSON ドキュメントのプロパティを参照します。 SELECT 句の例は、「[ツインのクエリの概要][lnk-query-getstarted]」セクションに記載されています。

現在のところ、**SELECT** * 以外の選択句は、デバイス ツインの集計クエリでのみサポートされています。

## <a name="group-by-clause"></a>GROUP BY 句
**GROUP BY <group_specification>** 句はオプションの手順であり、WHERE 句で指定したフィルターの後か、SELECT で指定したプロジェクションの前に実行します。 属性の値に基づいてドキュメントをグループ化します。 これらのグループを使用すると、SELECT 句で指定した方法で、集計された値が生成されます。

次は GROUP BY を使用したクエリの例です。

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

GROUP BY の正式な構文は次のとおりです。

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**attribute_name** は、FROM コレクションの JSON ドキュメントのプロパティを参照します。

現在のところ、GROUP BY 句はデバイス ツインに対するクエリの場合にのみサポートされています。

## <a name="expressions-and-conditions"></a>式と条件
*式* とはおおまかに次のように言い表すことができます。

* JSON 型 (ブール値、数値、文字列、配列、オブジェクトなど) のインスタンスに対して評価を行う
* 組み込みの演算子と関数を使用するデバイス JSON ドキュメントと定数からくるデータの操作として定義される

*条件*はブール値に対して評価する式のことです。 ブール値の **true** 以外の定数は、**false** と見なされます。 このルールには、**null**、**未定義**、任意のオブジェクトまたは配列インスタンス、任意の文字列、およびブール値の **false** が含まれます。

式の構文は次のとおりです。

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

式の構文で使われている各シンボルの意味については、次の表をご覧ください。

| シンボル | 定義 |
| --- | --- |
| attribute_name | **FROM** コレクションの JSON ドキュメントのプロパティ。 |
| binary_operator | 「[演算子](#operators)」セクションに記載されている 2 項演算子。 |
| function_name| 「[関数](#functions)」セクションに記載されている関数。 |
| decimal_literal |10 進表記で表される浮動小数点数。 |
| hexadecimal_literal |文字列 ‘0x’ の後に 16 進数の文字列を続けて表された数値。 |
| string_literal |文字列リテラルは、0 個以上の Unicode 文字のシーケンスまたはエスケープ シーケンスによって表される Unicode 文字列です。 文字列リテラルは、単一引用符または二重引用符で囲みます。 使用できるエスケープ: 4 つの 16 進数字によって定義された Unicode 文字の `\'`、`\"`、`\\`、`\uXXXX`。 |

### <a name="operators"></a>演算子
次の演算子がサポートされています。

| ファミリ | 演算子 |
| --- | --- |
| 算術 |+, -, *, /, % |
| 論理 |AND、OR、NOT |
| 比較 |=、!=、<、>、<=、>=、<> |

### <a name="functions"></a>Functions
ツインとジョブのクエリにおいて、サポートされている唯一の関数は次のとおりです。

| 関数 | 説明 |
| -------- | ----------- |
| IS_DEFINED(property) | プロパティに値が代入されているかどうかを示すブール値を返します (`null` を含む)。 |

ルートの条件では、次の計算関数がサポートされています。

| 関数 | 説明 |
| -------- | ----------- |
| ABS(x) | 指定された数値式の絶対値 (正の値) を返します。 |
| EXP(x) | 指定された数値式の指数値を返します (e^x)。 |
| POWER(x,y) | 指定されたべき乗の指定された式の値を返します (x^y)。|
| SQUARE(x) | 指定された数値の 2 乗を返します。 |
| CEILING(x) | 指定された数値式以上の最小の整数値を返します。 |
| FLOOR(x) | 指定された数値式未満の最大の整数を返します。 |
| SIGN(x) | 指定された数値式の正 (+1)、ゼロ (0)、または負 (-1) の符号を返します。|
| SQRT(x) | 指定された数値の平方根を返します。 |

ルートの条件では、次の型の確認とキャスト関数がサポートされています。

| 関数 | 説明 |
| -------- | ----------- |
| AS_NUMBER | 入力文字列を数値に変換します。 入力が数値の場合は `noop`、文字列が数値を表していない場合は `Undefined` です。|
| IS_ARRAY | 指定した式の型が配列であるかどうかを示すブール値を返します。 |
| IS_BOOL | 指定した式の型がブール値であるかどうかを示すブール値を返します。 |
| IS_DEFINED | プロパティに値が代入されているかどうかを示すブール値を返します。 |
| IS_NULL | 指定した式の型が null であるかどうかを示すブール値を返します。 |
| IS_NUMBER | 指定した式の型が数値であるかどうかを示すブール値を返します。 |
| IS_OBJECT | 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。 |
| IS_PRIMITIVE | 指定した式の型がプリミティブ (文字列、ブール値、数値、または `null`) であるかどうかを示すブール値を返します。 |
| IS_STRING | 指定した式の型が文字列であるかどうかを示すブール値を返します。 |

ルートの条件では、次の文字列関数がサポートされています。

| 関数 | 説明 |
| -------- | ----------- |
| CONCAT(x, y, …) | 2 つ以上の文字列値を連結した結果である文字列を返します。 |
| LENGTH(x) | 指定された文字列式の文字数を返します。|
| LOWER(x) | 文字列式の大文字データを小文字に変換して返します。 |
| UPPER(x) | 文字列式の小文字データを大文字に変換して返します。 |
| SUBSTRING(string, start [, length]) | 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。 |
| INDEX_OF(string, fragment) | 1 つ目に指定された文字列式内で 2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。|
| STARTS_WITH(x, y) | 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。 |
| ENDS_WITH(x, y) | 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。 |
| CONTAINS(x,y) | 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。 |

## <a name="next-steps"></a>次の手順
[Azure IoT Hub SDK][lnk-hub-sdks] を使用して、アプリでクエリを実行する方法を説明します。

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
