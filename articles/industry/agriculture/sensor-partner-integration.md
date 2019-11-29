---
title: センサー パートナーの統合
description: センサー パートナーの統合について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1e819c94732e1cbc2de39e6400f8305b7df5aca1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927650"
---
# <a name="sensor-partner-integration"></a>センサー パートナーの統合

この記事では、Azure FarmBeats **Translator** コンポーネントについて説明します。これを使うと、センサー パートナーとの統合が可能になります。

このコンポーネントを使用すると、パートナーは API を活用して FarmBeats と統合されたセンサーを開発し、FarmBeats データ ハブにお客様のデバイス データとテレメトリを送信できます。 データは FarmBeats Accelerator を使用して視覚化されます。 データは、データ融合や、機械言語/人工知能モデルの構築に使用できます。

## <a name="link-farmbeats-account"></a>FarmBeats アカウントのリンク

お客様がデバイス/センサーを購入してデプロイすると、デバイス パートナーの SaaS ポータル (サービスとしてのソフトウェア) でデバイス データとテレメトリにアクセスできるようになります。 デバイス パートナーは、アカウントを Azure 上の FarmBeats インスタンスとリンクできるようにしておく必要があります。 顧客/システム インテグレーターによる入力には、次の資格情報が必要です。

   - 表示名 (ユーザーがこの統合の名前を定義するためのオプション フィールド)
   - API エンドポイント
   - テナント ID
   - クライアント ID
   - クライアント シークレット
   - EventHub 接続文字列
   - 開始日

   > [!NOTE]
   > 開始日は、履歴データのフィード (ユーザーが指定した日付以降のデータ) を有効にします。

## <a name="unlink-farmbeats"></a>FarmBeats のリンク解除

お客様は、既存の FarmBeats 統合のリンクを解除することができます。 FarmBeats のリンクを解除しても、お客様のデータ ハブで作成されたデバイス/センサーのメタデータは削除されません。 リンクを解除すると次のことが行われます。

   - テレメトリ フローが停止されます。
   - デバイス パートナーの統合資格情報が削除されます。

## <a name="edit-farmbeats-integration"></a>FarmBeats 統合の編集

クライアント シークレットまたは接続文字列が変更された場合、顧客は FarmBeats 統合設定を編集できます。 この場合、顧客は次のフィールドのみを編集できます。

   - 表示名 (該当する場合)
   - クライアント シークレット (クリア テキストではなく、“2x8***********” 形式または表示/非表示機能を使って表示されます)
   - 接続文字列 (クリア テキストではなく、“2x8***********” 形式または表示/非表示機能を使って表示されます)

## <a name="view-last-telemetry-sent"></a>最後に送信されたテレメトリの表示

最後に**送信されたテレメトリ**のタイムスタンプを表示できます。 これは、最新のテレメトリが FarmBeats に正常に送信された時間です。

## <a name="translator-development"></a>Translator の開発

**REST API ベースの統合**

FarmBeats のセンサー データ統合機能は、REST API 経由で公開されます。 機能には、メタデータの定義、デバイス/センサーのプロビジョニング、デバイス、センサーの管理などがあります。

**テレメトリ インジェスト**

テレメトリ データは、処理のために Azure Event Hub に発行される正規のメッセージにマップされます。 Azure EventHub は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) を取り込むことができるようにするサービスです。

**API の開発**

API には、Swagger の技術ドキュメントが含まれています。 API とそれに対応する要求/応答の詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

**認証**

FarmBeats は、Microsoft Azure の Active Directory 認証を活用しています。 Azure App Service によって、組み込みの認証と承認がサポートされます。

詳細については、「 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)」を参照してください。

FarmBeats データ ハブではベアラー認証を使用します。この認証では、次の資格情報が必要になります。
   - クライアント ID
   - クライアント シークレット
   - テナント ID

呼び出し元は、これらの資格情報を使用してアクセス トークンを要求できます。トークンは、後続の API 要求で、次のようにヘッダー セクションに含めて送信する必要があります。

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

次に示すのは、後続の FarmBeats API 呼び出しに使用できるアクセス トークンを提供するサンプル Python コードです。 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 要求ヘッダー**

FarmBeats データ ハブへの API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。


**ヘッダー** | **説明と例**
--- | ---
Content-Type | 要求形式 (Content-type: application/<format>)。FarmBeats データ ハブの API の形式は json です。 Content-Type, application/json
Authorization | API 呼び出しを行うために必要なアクセス トークンを指定します。Authorization:Bearer <Access-Token>
Accept | 応答形式。 FarmBeats データ ハブ API シリーズの場合、形式は json です。Accept: application/json

**API 要求**

REST (Representational State Transfer) API 要求を行うには、HTTP (GET、POST、または PUT) メソッド、API サービスへの URL、リソース (クエリ、データ送信、更新、削除の対象となるリソース) の URI (Uniform Resource Identifier)、および 1 つ以上の HTTP 要求ヘッダーを組み合わせます。 API サービスの URL は、お客様が提供する API エンドポイントです。 次に例を示します。 https://\<yourdatahub-website-name>.azurewebsites.net

必要に応じて、GET 呼び出しにクエリ パラメーターを追加して、応答のデータのフィルター処理、サイズ制限、並べ替えを行うことができます。

以下の要求例では、デバイスの一覧を取得します。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大部分の GET、POST、PUT 呼び出しには、JSON 要求本文が必要です。

次の要求例ではデバイスを作成します (このサンプルには、入力 json と要求本文が含まれています)。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>データ形式

JSON (JavaScript Object Notation) は、任意のデータ構造をテキストによって簡単に表現できるようにする、言語に依存しない一般的なデータ形式です。 詳細については、[json.org](http://json.org) を参照してください。

## <a name="metadata-specifications"></a>メタデータの仕様

FarmBeats データ ハブには、デバイス パートナーがデバイスとセンサーのメタデータを作成して管理できるようにする次の API が用意されています。  

- /**DeviceModel** - デバイス モデルは、製造元、デバイスの種類 (ゲートウェイまたはノード) など、デバイスのメタデータに対応するものです。  
- /**Device** - デバイスは、ファーム内に存在する物理デバイスに対応するものです。
- /**SensorModel** - センサー モデルはセンサーのメタデータに対応するものです。たとえば、製造元、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) などです。
- /**Sensor** - センサーは、値を記録する物理的なセンサーに対応するものです。 センサーは通常、デバイス ID を持つデバイスに接続されます。

  デバイス モデル| DeviceModel は、製造元、デバイスの種類 (ゲートウェイまたはノード) など、デバイスのメタデータに対応します。
  --- | ---
  Type (Node、Gateway)  | 1 つ星 |
  Manufacturer  | 2 つ星 |
  ProductCode  | デバイスの製品コードまたはモデルの名前や番号 (例: EnviroMonitor#6800)。 |
  Port  | ポートの名前と種類 (デジタルまたはアナログ)  |
  名前  | リソースを識別する名前 (例: モデル名または製品名)。 |
  説明  | そのモデルについてのわかりやすい説明を入力します |
  properties  | 製造元から提供されるその他のプロパティ |
  **デバイス** | **デバイスは、ファーム内に存在する物理デバイスに対応するものです。各デバイスには一意のデバイス ID があります。** |
DeviceModelId  |関連付けられているデバイス モデルの ID |
HardwareId   |MAC アドレスなど、デバイスの一意の ID  |
reportingInterval |レポートの間隔 (秒) |
Location    |デバイスの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位) |
ParentDeviceId | このデバイスが接続されている親デバイスの ID。 次に例を示します。 ゲートウェイに接続されているノード。ノードは parentDeviceID というゲートウェイを持ちます。 |
  名前  | リソースを識別する名前。  デバイス パートナーは、デバイス パートナー側のデバイス名と一致する名前を送信する必要があります。 デバイス名がデバイス パートナー側でユーザー定義である場合、同じユーザー定義名を FarmBeats に反映させる必要があります。  |
  説明  | わかりやすい説明を入力します  |
  properties  |製造元から提供されるその他のプロパティ  |
  **センサー モデル** | SensorModel はセンサーのメタデータに対応するものです。たとえば、製造元、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) などです。 |
  Type (analog、digital)  |アナログまたはデジタル センサーを表します|
  manufacturer  | 製造元の名前 |
  ProductCode  | 製品コードまたはモデルの名前や番号 (例: RS-CO2-N01)。  |
  SensorMeasures > Name  | センサーのメジャーの名前。 小文字のみがサポートされます。 異なる深さのメジャーについては、深さを指定します (例: soil_moisture_15cm) この名前は、テレメトリ データと一致している必要があります。 |
  sensorMeasures > DataType  | テレメトリのデータ型。 現在、倍精度浮動小数点型がサポートされています  |
  sensorMeasures > Type  | センサーのテレメトリ データの測定の種類。 システム定義の種類は次のとおりです。AmbientTemperature (周辺温度)、CO2 (二酸化炭素)、Depth (深さ)、ElectricalConductivity (導電率)、LeafWetness (木の葉の湿り気)、Length (長さ)、LiquidLevel (液面)、Nitrate (硝酸塩)、O2 (酸素)、PH (pH)、Phosphate (リン酸塩)、PointInTime (特定の時点)、Potassium (カリウム)、Pressure (圧力)、RainGauge (雨量)、RelativeHumidity (相対湿度)、Salinity (塩分濃度)、SoilMoisture (土壌の水分)、SoilTemperature (地温)、SolarRadiation (太陽放射)、State (状態)、TimeDuration (期間)、UVRadiation (紫外線放射)、UVIndex (紫外線指数)、Volume (容積)、WindDirection (風向)、WindRun (風程)、WindSpeed (風速)、Evapotranspiration (蒸散)、PAR (光合成有効放射量)。 さらに追加するには、/Extendedtype API を参照してください。
  sensorMeasures > Unit | センサーのテレメトリ データの単位。 システム定義の単位は次のとおりです。NoUnit (単位なし)、Celsius (摂氏)、Fahrenheit (華氏)、Kelvin (ケルビン)、Rankine (ランキン温度)、Pascal (パスカル)、Mercury (マーキュリー)、PSI、MilliMeter (ミリメートル)、CentiMeter (センチメートル)、Meter (メートル)、Inch (インチ)、Feet (フィート)、Mile (マイル)、KiloMeter (キロメートル)、MilesPerHour (マイル/時)、MilesPerSecond (マイル/秒)、KMPerHour (キロメートル/時)、KMPerSecond (キロメートル/秒)、MetersPerHour (メートル/時)、MetersPerSecond (メートル/秒)、Degree (度)、WattsPerSquareMeter (ワット/平方メートル)、KiloWattsPerSquareMeter (キロワット/平方メートル)、MilliWattsPerSquareCentiMeter (ミリワット/平方センチメートル)、MilliJoulesPerSquareCentiMeter (ミリジュール/平方センチメートル)、VolumetricWaterContent (体積含水量)、Percentage (百分率)、PartsPerMillion (百万分の一)、MicroMol (マイクロモル)、MicroMolesPerLiter (マイクロモル/リットル)、SiemensPerSquareMeterPerMole (ジーメンス/平方メートル/モル)、MilliSiemensPerCentiMeter (ミリジーメンス/センチメートル)、Centibar (センチバール)、DeciSiemensPerMeter (デシジーメンス/メートル)、KiloPascal (キロパスカル)、VolumetricIonContent (体積イオン含有量)、Liter (リットル)、MilliLiter (ミリリットル)、Seconds (秒)、UnixTimestamp (UNIX タイムスタンプ)、MicroMolPerMeterSquaredPerSecond (マイクロモル/平方メートル/秒)、InchesPerHour (インチ/時間)。さらに追加する場合は、/ExtendedType API を参照してください。
  SensorMeasures > aggregationType  | none (なし)、average (平均)、maximum (最大)、minimum (最小)、または StandardDeviation (標準偏差) のいずれか
  SensorMeasures > depth  | センチメートル単位のセンサーの深度 (例: 地下 10 cm の位置の湿度のメジャー)
  sensorMeasures > description  | そのメジャーについてのわかりやすい説明を入力します
  名前  | リソースを識別する名前 (例: モデル名または製品名)。
  description  | そのモデルについてのわかりやすい説明を入力します
  properties  | 製造元から提供されるその他のプロパティ
  **センサー**  |
  hardwareId  | 製造元によって設定された、センサーの一意の ID
  sensorModelId  | 関連付けられているセンサー モデルの ID
  location  | センサーの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)
  port > name  |センサーが接続されているデバイスのポートの名前と種類。 これは、デバイス モデルで定義されている名前と同じにする必要があります。
  deviceId  | センサーが接続されているデバイスの ID
  名前  | リソースを識別する名前 (例: センサー名または製品名や、モデル番号または製品コード)。
  description  | わかりやすい説明を入力します
  properties  | 製造元から提供されるその他のプロパティ

 各オブジェクトとそのプロパティの詳細については、[swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

 > [!NOTE]
 > API からは、作成されたインスタンスごとに一意の ID が返されます。 デバイスの管理とメタデータの同期のため、Translator はこの ID を保持する必要があります。


**メタデータの同期**

Translator は、メタデータに更新を送信する必要があります。 更新シナリオには、デバイス/センサーの名前の変更、デバイス/センサーの場所の変更などがあります。

Translator には、FarmBeats のユーザー投稿リンクからインストールされた新しいデバイスやセンサーを追加する機能が必要です。 また、デバイス/センサーがユーザーによって更新されている場合は、FarmBeats でも対応するデバイス/センサーを更新する必要があります。 デバイス/センサーを更新する一般的なシナリオには、デバイスの場所の変更、ノードでのセンサーの追加などがあります。


> [!NOTE]
> デバイス/センサーのメタデータでは、削除はサポートされていません。
>
> メタデータを更新するには、デバイス/センサーで /Get/{id} を呼び出し、変更されたプロパティを更新してから /Put/{id} を実行し、ユーザーが設定したプロパティが失われないようにする必要があります。

### <a name="adding-new-typesunit"></a>新しい型/単位の追加

FarmBeats は、センサーのメジャーの種類や単位の新規追加をサポートしています。 /Extendedtype API の詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

## <a name="telemetry-specifications"></a>テレメトリの仕様

テレメトリ データは、処理のために Azure Event Hub に発行される正規のメッセージにマップされます。 Azure EventHub は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) を取り込むことができるようにするサービスです。

## <a name="send-telemetry-data-to-farmbeats"></a>テレメトリ データを FarmBeats に送信する

テレメトリ データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成する必要があります。 テレメトリ データの詳細については、[イベント ハブへのテレメトリの送信](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)に関する記事を参照してください。

指定されたイベント ハブにクライアントとしてテレメトリを送信するサンプル Python コードを次に示します。

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

正規のメッセージ形式は次のとおりです。

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
deviceid、sensordata のように、テレメトリ json 内のすべてのキー名は小文字にする必要があります。

テレメトリ メッセージの例:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooterror-management"></a>トラブルシューティング/エラー管理

**トラブルシューティングのオプション/サポート**

お客様が指定された FarmBeats インスタンスでデバイス データやテレメトリを受信できない場合、デバイス パートナーがサポートやその問題を解決するためのメカニズムを提供する必要があります。

**テレメトリ データの保有期間**

テレメトリ データは、事前に定義された期間にわたって保持する必要があります。これは、エラーまたはデータの損失が発生した場合にテレメトリをデバッグまたは再送信する際に役に立ちます。

**エラー管理/エラー通知**

デバイス パートナー システムのデバイス/センサー メタデータ/データ統合またはテレメトリ データ フローに影響するエラーが発生した場合は、お客様に通知する必要があります。 エラーを解決するためのメカニズムも設計および実装する必要があります。

**接続チェックリスト**

デバイスの製造元/パートナーは、お客様から提供された資格情報が正確であることを確認するために、次のような正常性テスト/チェックリストを持つことができます。

   - 指定された資格情報と合わせてアクセス トークンを受信したかどうかを確認する
   - 受信したアクセス トークンで API 呼び出しが成功するかどうかを確認する
   - EventHub クライアント接続が確立されているかどうかを確認する

## <a name="next-steps"></a>次の手順

REST API の詳細については、[REST API](references-for-farmbeats.md#rest-api) を参照してください。
