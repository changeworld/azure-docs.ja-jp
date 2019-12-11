---
title: テレメトリの履歴データの取り込み
description: テレメトリの履歴データを取り込む方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27aec53fd2e92e19f1c749e833217fb8b5deae57
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672572"
---
# <a name="ingest-historical-telemetry-data"></a>過去のテレメトリ データの取り込み

この記事では、センサーの履歴データを Azure FarmBeats に取り込む方法について説明します。

デバイスやセンサーなどのリソース用にモノのインターネット (IoT) から履歴データを取り込むことは、FarmBeats では一般的なシナリオです。 デバイスとセンサーのメタデータを作成してから、その履歴データを正規形式で FarmBeats に取り込みます。

## <a name="before-you-begin"></a>開始する前に

この記事を読み進める前に、FarmBeats をインストールし、IoT から履歴データを収集してあることを確認してください。
次の手順で説明するように、パートナー アクセスを有効にする必要もあります。

## <a name="enable-partner-access"></a>パートナー アクセスを有効にする

Azure FarmBeats インスタンスへのパートナー統合を有効にする必要があります。 この手順では、デバイス パートナーとして Azure FarmBeats にアクセスできるクライアントを作成し、以降の手順で必要となる以下の値を指定します。

- API エンドポイント - これはデータ ハブの URL です (https://<datahub>.azurewebsites.net など)
- テナント ID
- クライアント ID
- クライアント シークレット
- EventHub 接続文字列

これらを生成するには、次の手順に従います。

>[!NOTE]
> 次の手順を実行するには、管理者である必要があります。

1. この[スクリプト](https://aka.ms/farmbeatspartnerscript)をダウンロードし、ローカル ドライブに展開します。 この ZIP ファイル内には 2 つのファイルがあります。
2. [Azure portal](https://portal.azure.com/) にサインインし、Cloud Shell を開きます (このオプションは、ポータルの右上のバーで利用できます)。  

    ![プロジェクト (Farm Beats)](./media/for-tutorials/navigation-bar-1.png)

3. 環境が **[PowerShell]** に設定されていることを確認します。

    ![プロジェクト (Farm Beats)](./media/for-tutorials/power-shell-new-1.png)

4. Cloud Shell に、(上記の手順 1 で) ダウンロードした 2 つのファイルをアップロードします。  

    ![プロジェクト (Farm Beats)](./media/for-tutorials/power-shell-two-1.png)

5. ファイルがアップロードされたディレクトリに移動します。

   >[!NOTE]
   > 既定では、ファイルはホーム ディレクトリ /home/username/ にアップロードされます。
6. 次のコマンドを使用して、スクリプトを実行します。  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. 画面の指示に従って、手順を完了します。

## <a name="create-devicesensor-metadata"></a>デバイスまたはセンサーのメタデータを作成する

 必要な資格情報が得られたので、FarmBeats API シリーズを使用してメタデータを作成することで、デバイスとセンサーを定義できます。

 FarmBeats データ ハブには、デバイスまたはセンサーのメタデータの作成と管理を可能にする次の API が用意されています。   

- /**DeviceModel** - デバイス モデルは、製造元、デバイスの種類 (ゲートウェイまたはノード) など、デバイスのメタデータに対応します。  
- /**Device** - デバイスは、ファーム内に存在する物理デバイスに対応します。  
- /**SensorModel** - センサー モデルはセンサーのメタデータに対応します。たとえば、製造元、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) などです。
- /**Sensor** - センサーは、値を記録する物理的なセンサーに対応します。 センサーは通常、デバイス ID を持つデバイスに接続されます。  


|        デバイス モデル   |  検索候補   |
| ------- | -------             |
|     Type (Node、Gateway)        |          1 つ星      |
|          Manufacturer            |         2 つ星     |
|  ProductCode                    |  デバイスの製品コードまたはモデルの名前や番号。 例: EnviroMonitor#6800。  |
|            Port          |     ポートの名前と種類 (デジタルまたはアナログ)
|     名前                 |  リソースを識別するための名前。 例: モデル名または製品名。
      説明     | そのモデルについてのわかりやすい説明を入力します
|    properties          |    製造元から提供されるその他のプロパティ   |
|    **デバイス**             |                      |
|   DeviceModelId     |     関連付けられているデバイス モデルの ID  |
|  HardwareId          | MAC アドレスなど、デバイスの一意の ID
|  ReportingInterval        |   レポートの間隔 (秒)
|  Location            |  デバイスの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)   
|ParentDeviceId       |    このデバイスが接続されている親デバイスの ID。 たとえば、ノードがゲートウェイに接続されているとします。 ノードには ParentDeviceId というゲートウェイが作成されます。  |
|    名前            | リソースを識別するための名前。 デバイス パートナーは、パートナー側のデバイス名と一致する名前を送信する必要があります。 パートナー デバイス名がユーザー定義である場合、同じユーザー定義名を FarmBeats に反映させる必要があります。|
|     説明       |      わかりやすい説明を入力します  |
|     properties    |  製造元から提供されるその他のプロパティ
|     **センサー モデル**        |          |
|       Type (Analog、Digital)          |      センサーの種類 (アナログまたはデジタル)       |
|          Manufacturer            |       センサーの製造元     |
|     ProductCode| 製品コードまたはモデルの名前や番号。 例: RS-CO2-N01。 |
|       SensorMeasures > Name       | センサーのメジャーの名前。 小文字のみがサポートされます。 異なる深さのメジャーの場合は深さを指定します。 例: soil_moisture_15cm。 この名前は、テレメトリ データと一致している必要があります。  |
|          SensorMeasures > DataType       |テレメトリのデータ型。 現在、倍精度浮動小数点型がサポートされています。|
|    SensorMeasures > Type    |センサーのテレメトリ データの測定の種類。 システム定義の種類は次のとおりです。AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 さらに追加するには、/Extendedtype API を参照してください。|
|        SensorMeasures > Unit              | センサーのテレメトリ データの単位。 システム定義の単位は次のとおりです。NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour。さらに追加する場合は、/ExtendedType API を参照してください。|
|    SensorMeasures > aggregationType    |  値は、none、average、maximum、minimum、または StandardDeviation を指定できます。  |
|          名前            | リソースを識別するための名前。 例: モデル名または製品名。  |
|    説明        | そのモデルについてのわかりやすい説明を入力します  |
|   properties       |  製造元から提供されるその他のプロパティ  |
|    **Sensor**      |          |
| HardwareId          |   製造元によって設定された、センサーの一意の ID |
|  SensorModelId     |    関連付けられているセンサー モデルの ID   |
| location          |  センサーの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)|
|   Port > name        |  センサーが接続されている、デバイス上のポートの名前と種類。 これは、デバイス モデルで定義されているのと同じ名前にする必要があります。 |
|    DeviceID  |    センサーが接続されているデバイスの ID     |
| 名前            |   リソースを識別するための名前。 例: センサー名または製品名や、モデル番号または製品コード。|
|    説明      | わかりやすい説明を入力します |
|    properties        |製造元から提供されるその他のプロパティ |

オブジェクトの詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

**メタデータを作成するための API 要求**

API 要求を行うには、HTTP (POST) メソッド、API サービスの URL、(クエリ、データの送信、要求の作成または削除の対象となる) リソースの URI を結合し、1 つ以上の HTTP 要求ヘッダーを追加します。 API サービスの URL は、API エンドポイント、つまりデータ ハブの URL (https://<yourdatahub>.azurewebsites.net) です。  

**認証**:

FarmBeats データ ハブではベアラー認証を使用します。これには、前のセクションで生成した次の資格情報が必要になります。

- クライアント ID
- クライアント シークレット
- テナント ID  

呼び出し元は、上記の資格情報を使用してアクセス トークンを要求できます。トークンは、次のように、後続の API 要求のヘッダー セクションで送信する必要があります。

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**HTTP 要求ヘッダー**:

FarmBeats データ ハブへの API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。

- Content-Type: application/json
- Authorization:Bearer <Access-Token>
- Accept: application/json

**メタデータを作成するための入力ペイロード**:

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Device

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
センサー

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
以下の要求例では、デバイスを作成します (これには、要求本文と共にペイロードとして入力 JSON が含まれています)。  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> API からは、作成されたインスタンスごとに一意の ID が返されます。 対応するテレメトリ メッセージを送信するには、これらの ID を保持する必要があります。

**テレメトリを送信する**

FarmBeats でデバイスとセンサーを作成したので、関連付けられたテレメトリ メッセージを送信できるようになりました。  

**テレメトリ クライアントを作成する**

テレメトリを Azure Event Hubs に送信して、処理する必要があります。 Azure Event Hubs は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) を取り込むことができるようにするサービスです。 テレメトリ データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成する必要があります。 テレメトリの送信の詳細については、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send) に関するページを参照してください。

**クライアントとしてテレメトリ メッセージを送信する**

EventHub クライアントとして接続を確立したら、メッセージを JSON としてイベント ハブに送信できます。  
センサーの履歴データの形式を、Azure FarmBeats が理解できる正規形式に変換します。 正規のメッセージ形式は次のとおりです。  

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

対応するデバイスとセンサーを追加した後、前のセクションで説明したように、テレメトリ メッセージでデバイス ID とセンサー ID を取得します。

テレメトリ メッセージの例:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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


## <a name="next-steps"></a>次の手順

REST API ベースの統合の詳細については、「[REST API](references-for-farmbeats.md#rest-api)」を参照してください。
