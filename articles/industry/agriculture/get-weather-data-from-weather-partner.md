---
title: 気象パートナーから気象データを取得する
description: この記事では、パートナーから気象データを取得する方法について説明します。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 7666ee1a81c2ed93ee5e246b3ec79f056f9d63ab
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187780"
---
# <a name="get-weather-data-from-weather-partners"></a>気象パートナーから気象データを取得する

Azure FarmBeats を利用すると、Docker ベースのコネクタ フレームワークを使用して、気象データ プロバイダーから気象データを取り込むことができます。 気象データ プロバイダーでは、このフレームワークを使用して、FarmBeats と統合できる Docker を実装します。 現在、次の気象データ プロバイダーがサポートされています。

  ![DTN](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [DTN](https://www.dtn.com/dtn-content-integration/)

気象データを使用して、実用的な分析情報を生成し、FarmBeats で AI/ML のモデルを構築することができます。

## <a name="before-you-start"></a>開始する前に

気象データを取得するには、FarmBeats がインストールされていることを確認します。 **気象の統合は、バージョン1.2.11 以降でサポートされています**。 Azure FarmBeats をインストールするには、[FarmBeats のインストール](https://aka.ms/farmbeatsinstalldocumentation)に関する記事を参照してください。

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats との気象の統合を有効にする

FarmBeats データ ハブで気象データの取得を開始するには、次の手順に従います。

1. FarmBeats データ ハブ Swagger (https://farmbeatswebsite-api.azurewebsites.net/swagger) にアクセスします。

2. /Partner API に移動し、次の入力ペイロードを使用して POST 要求を行います。

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   たとえば、DTN から気象データを取得するには、次のペイロードを使用します。 好みに応じて、名前と説明を変更できます。

   > [!NOTE]
   > 以下の手順では API キーが必要です。DTN サブスクリプションの API キーについては、DTN にお問い合わせください。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Partner オブジェクトの詳細については、「[付録](get-weather-data-from-weather-partner.md#appendix)」を参照してください

   前述の手順により、お客様の FarmBeats 環境で Docker が実行できるようにリソースがプロビジョニングされます。  

   上記のリソースをプロビジョニングするには、約 10 分から 15 分かかります。

3. 手順 2 で作成した /Partner オブジェクトの状態を確認します。 これを行うには、/Partner API で GET 要求を行い、パートナー オブジェクトの**状態**を確認します。 FarmBeats がパートナーを正常にプロビジョニングすると、状態が **Active** に設定されます。

4. /JobType API に移動し、同一のもので GET 要求を行います。 手順 2 の Partner 追加プロセスの一部として作成された気象ジョブを確認します。 気象ジョブの **pipelineName** フィールドは、"partner-name_partner-type_job-name" の形式になります。

5. これで、FarmBeats インスタンスにアクティブな気象データ パートナーが設定されたので、特定の場所 (緯度/経度) と日付範囲の気象データを要求するジョブを実行することができます。 JobType には、気象ジョブの実行に必要なパラメーターの詳細が含まれます。

   たとえば、DTN の場合、次の JobType が作成されます。
   
   - get_dtn_daily_observations (ある場所と期間に対する日単位の観測結果を取得する)
   - get_dtn_daily_forecasts (ある場所と期間に対する日単位の予測を取得する)
   - get_dtn_hourly_observations (ある場所と期間に対する時間単位の観測結果を取得する)
   - get_dtn_hourly_forecasts (ある場所と期間に対する時間単位の予測を取得する)

6. **ID** と JobType のパラメーターをメモしておきます。

7. /Jobs API に移動し、/Jobs で次の入力ペイロードを使用して POST 要求を行います。

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   たとえば、**get_dtn_daily_observations** を実行するには、次のペイロードを使用します。

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 前述の手順により、パートナー Docker で定義されている気象ジョブが実行され、気象データが FarmBeats に取り込まれます。 ジョブの状態を確認するには、/Jobs で GET 要求を行い、応答の **currentState** を探します。 完了すると、currentState が **Succeeded** に設定されます。

## <a name="query-ingested-weather-data"></a>取り込まれた気象データに対してクエリを実行する

気象ジョブが完了したら、FarmBeats Datahub REST API を使用して、取り込まれた気象データに対してクエリを実行し、モデルや実用的な分析情報を作成できます。

### <a name="query-using-rest-api"></a>REST API を使用してクエリを実行する

FarmBeats REST API を使用して気象データに対してクエリを実行するには、次の手順に従います。

1. FarmBeats データ ハブ Swagger (https://yourdatahub.azurewebsites.net/swagger) で、/WeatherDataLocation API に移動し、GET 要求を行います。 応答には、ジョブの実行の一部として指定された場所 (緯度/経度) に対して作成された/WeatherDataLocation オブジェクトが含まれます。 オブジェクトの **ID** と **weatherDataModelId** をメモしておきます。

2. 手順 1 でメモした **weatherDataModelId** に対して /WeatherDataModel API で GET/{id} を行います。 "気象データ モデル" には、取り込まれた気象データに関するすべてのメタデータと詳細が含まれています。 たとえば、**気象データ モデル** オブジェクト内の**気象測定**には、サポートされている気象情報とその種類および単位に関する詳細が含まれています。 たとえば、次のように入力します。

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   気象データ モデルの GET/{id} 呼び出しからの応答をメモしておきます。

3. **Telemetry** API に移動し、次の入力ペイロードを使用して POST 要求を行います。

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 指定した時間範囲で使用できる気象データを含む応答は、次のようになります。

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

前の例では、応答には、2 つのタイムスタンプのデータ、メジャー名 ("Temperature")、および 2 つのタイムスタンプ内の報告された気象データの値が含まれています。 報告された値の種類と単位を解釈するには、関連する気象データ モデル (上記の手順 2 で説明) を参照する必要があります。

## <a name="troubleshoot-job-failures"></a>ジョブの失敗のトラブルシューティング

ジョブの失敗をトラブルシューティングするには、ジョブのログを確認します。 同様の手順については、[こちらの手順](troubleshoot-azure-farmbeats.md#weather-data-job-failures)に従ってください。


## <a name="appendix"></a>付録

|        Partner   |  詳細   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker イメージの名前。 たとえば、docker.io/mydockerimage (hub.docker.com 内のイメージ) または myazureacr.azurecr.io/mydockerimage (Azure Container Registry 内のイメージ) などです。 レジストリが指定されていない場合、既定値は hub.docker.com です      |
|          DockerDetails - imageTag             |         Docker イメージのタグ名。 既定値は "latest" です     |
|  DockerDetails - credentials      |  プライベート Docker にアクセスするための資格情報。 これは、パートナーが顧客に提供します   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM の SKU。 使用可能なすべての Linux 仮想マシンについては、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。 有効な値は次のとおりです。"Small'、'ExtraLarge'、'Large'、'A8'、'A9'、'Medium'、'A5'、'A6'、'A7'、'STANDARD_D1'、'STANDARD_D2'、'STANDARD_D3'、'STANDARD_D4'、'STANDARD_D11'、'STANDARD_D12'、'STANDARD_D13'、'STANDARD_D14'、'A10'、'A11'、'STANDARD_D1_V2'、'STANDARD_D2_V2'、'STANDARD_D3_V2'、'STANDARD_D4_V2'、'STANDARD_D11_V2'、'STANDARD_D12_V2'、'STANDARD_D13_V2'、'STANDARD_D14_V2'、'STANDARD_G1'、'STANDARD_G2'、'STANDARD_G3'、'STANDARD_G4'、'STANDARD_G5'、'STANDARD_D5_V2'、'BASIC_A1'、'BASIC_A2'、'BASIC_A3'、'BASIC_A4'、'STANDARD_A1'、'STANDARD_A2'、'STANDARD_A3'、'STANDARD_A4'、'STANDARD_A5'、'STANDARD_A6'、'STANDARD_A7'、'STANDARD_A8'、'STANDARD_A9'、'STANDARD_A10'、'STANDARD_A11'、'STANDARD_D15_V2'、'STANDARD_F1'、'STANDARD_F2'、'STANDARD_F4'、'STANDARD_F8'、'STANDARD_F16'、'STANDARD_NV6'、'STANDARD_NV12'、'STANDARD_NV24'、'STANDARD_NC6'、'STANDARD_NC12'、'STANDARD_NC24'、'STANDARD_NC24r'、'STANDARD_H8'、'STANDARD_H8m'、'STANDARD_H16'、'STANDARD_H16m'、'STANDARD_H16mr'、'STANDARD_H16r'、'STANDARD_A1_V2'、'STANDARD_A2_V2'、'STANDARD_A4_V2'、'STANDARD_A8_V2'、'STANDARD_A2m_V2'、'STANDARD_A4m_V2'、'STANDARD_A8m_V2'、'STANDARD_M64ms'、'STANDARD_M128s'、'STANDARD_D2_V3'。 **既定値は "standard_d2_v2" です**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  いいえ。 バッチ プールの専用コンピューター ノードの数。 既定値は 1 です。 |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch ノード エージェント SKU ID。 現在サポートされているのは、"batch.node.ubuntu 18.04" バッチ ノード エージェントのみです。    |
| DockerDetails - partnerCredentials | Docker でのパートナー API 呼び出し用の資格情報。 パートナーは、サポートされている認証メカニズムに基づいて、この情報を顧客に提供する必要があります。例: ユーザー名/パスワードまたは API キー。 |
| partnerType | "Weather" (FarmBeats のその他のパートナーの種類は "Sensor" と "Imagery" です)  |
|  name   |   FarmBeats システムでのパートナーの任意の名前   |
|  description |  説明   |

## <a name="next-steps"></a>次のステップ

これで、Azure FarmBeats インスタンスからのセンサー データにクエリを実行しました。 次に、ファームの[マップを生成する](generate-maps-in-azure-farmbeats.md#generate-maps)方法について説明します。
