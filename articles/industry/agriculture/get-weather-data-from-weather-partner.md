---
title: 気象パートナーから気象データを取得する
description: この記事では、パートナーから気象データを取得する方法について説明します。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265071"
---
# <a name="get-weather-data-from-weather-partners"></a>気象パートナーから気象データを取得する

Azure FarmBeats を利用すると、Docker ベースのコネクタ フレームワークを使用して、気象データ プロバイダーから気象データを取り込むことができます。 気象データ プロバイダーでは、このフレームワークを使用して、FarmBeats と統合できる Docker を実装します。 現在、次の気象データ プロバイダーがサポートされています。

[Azure Open Datasets からの NOAA データ](https://azure.microsoft.com/services/open-datasets/)

気象データを使用して、実用的な分析情報を生成し、FarmBeats で AI/ML のモデルを構築することができます。

## <a name="before-you-start"></a>開始する前に

気象データを取得するには、FarmBeats がインストールされていることを確認します。 **気象の統合は、バージョン1.2.11 以降でサポートされています**。 Azure FarmBeats をインストールするには、[FarmBeats のインストール](https://aka.ms/farmbeatsinstalldocumentation)に関する記事を参照してください。

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats との気象の統合を有効にする

FarmBeats データ ハブで気象データの取得を開始するには、次の手順に従います。

1. FarmBeats データ ハブ Swagger (https://yourdatahub.azurewebsites.net/swagger) にアクセスします。

2. /Partner API に移動し、次の入力ペイロードを使用して POST 要求を行います。

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
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

   たとえば、Azure Open Datasets によって NOAA から気象データを取得するには、次のペイロードを使用します。 好みに応じて、名前と説明を変更できます。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Partner オブジェクトの詳細については、「[付録](get-weather-data-from-weather-partner.md#appendix)」を参照してください

   前述の手順により、お客様の FarmBeats 環境で Docker が実行できるようにリソースがプロビジョニングされます。  

   上記のリソースをプロビジョニングするには、約 10 分から 15 分かかります。

3. 手順 2 で作成した /Partner オブジェクトの状態を確認します。 これを行うには、/Partner API で GET 要求を行い、パートナー オブジェクトの**状態**を確認します。 FarmBeats がパートナーを正常にプロビジョニングすると、状態が **Active** に設定されます。

4. /JobType API に移動し、同一のもので GET 要求を行います。 手順 2 の Partner 追加プロセスの一部として作成された気象ジョブを確認します。 気象ジョブの **pipelineName** フィールドは、"partner-name_partner-type_job-name" の形式になります。

5. これで、FarmBeats インスタンスにアクティブな気象データ パートナーが設定されたので、特定の場所 (緯度/経度) と日付範囲の気象データを要求するジョブを実行することができます。 JobType には、気象ジョブの実行に必要なパラメーターの詳細が含まれます。

   たとえば、Azure Open Datasets の NOAA データの場合、次の JobType が作成されます。

   - get_weather_data (ISD/過去の気象データを取得する)
   - get_weather_forecast_data (GFS/気象予報データを取得する)

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

   たとえば、**get_weather_data** を実行するには、次のペイロードを使用します。

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 前述の手順により、パートナー Docker で定義されている気象ジョブが実行され、気象データが FarmBeats に取り込まれます。 ジョブの状態を確認するには、/Jobs で GET 要求を行い、応答の **currentState** を探します。 完了すると、currentState が **Succeeded** に設定されます。

## <a name="query-ingested-weather-data"></a>取り込まれた気象データに対してクエリを実行する

気象ジョブが完了したら、取り込まれた気象データに対してクエリを実行して、モデルや実用的な分析情報を作成できます。 FarmBeats から気象データにアクセスしてクエリを実行するには、2 つの方法があります。

- API と
- Time Series Insights (TSI)。

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

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights (TSI) を使用してクエリを実行する

FarmBeats では [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) を使用して、IoT スケールでデータの取り込み、格納、クエリの実行、視覚化を行います。このデータは、豊富なコンテキスト情報が付加され、時系列に合わせて最適化されています。

気象データは EventHub で受信された後、FarmBeats リソース グループ内の TSI 環境にプッシュされます。 その後、TSI から直接、データにクエリを実行することができます。 詳細については、[TSI のドキュメント](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)を参照してください。

TSI でデータを視覚化するには、以下の手順に従います。

1. **[Azure portal]**  >  **[FarmBeats DataHub resource group]\(FarmBeats DataHub リソース グループ\)** の順に移動し、 **[Time Series Insights]** 環境 (tsi-xxxx) > **[データ アクセス ポリシー]** を選択します。 閲覧者または共同作成者のアクセス権を持つユーザーを追加します。

2. **[Time Series Insights]** 環境 (tsi-xxxx) の **[概要]** ページに移動し、 **[Time Series Insights エクスプローラーの URL]** を選択します。 これで、取り込まれた気象データを視覚化できるようになりました。

TSI では、気象データの格納、クエリの実行および視覚化だけでなく、Power BI ダッシュボードとの統合も可能です。 詳細については、「[Power BI の Time Series Insights からのデータを視覚化する](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)」を参照してください。

## <a name="appendix"></a>付録

|        Partner   |  詳細   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker イメージの名前。 たとえば、docker.io/azurefarmbeats/farmbeats-noaa (hub.docker.com 内のイメージ) または myazureacr.azurecr.io/mydockerimage (Azure Container Registry 内のイメージ) などです。 レジストリが指定されていない場合、既定値は hub.docker.com です      |
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
