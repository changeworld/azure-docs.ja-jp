---
title: DPS を使用したデバイスの自動管理
titleSuffix: Azure Digital Twins
description: Device Provisioning Service (DPS) を使用して、Azure Digital Twins で IoT デバイスをプロビジョニングおよび廃止する自動プロセスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1a7ab90cccd78c3b005487938432a0f955d50738
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380430"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Device Provisioning Service (DPS) を使用して Azure Digital Twins でデバイスを自動管理する

この記事では、Azure Digital Twins と [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、Device Provisioning Service を使用して、Azure Digital Twins で IoT Hub デバイスを "**_プロビジョニング_**" および "**_廃止_**" するプロセスを自動化できます。 

"_プロビジョニング_" および "_廃止_" ステージの詳細と、すべてのエンタープライズ IoT プロジェクトに共通する一般的なデバイス管理ステージの詳細については、IoT Hub のデバイス管理ドキュメントで「[*デバイスのライフサイクル*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)」のセクションを参照してください。

## <a name="prerequisites"></a>前提条件

プロビジョニングを設定する前に、モデルとツインを含む **Azure Digital Twins インスタンス**が必要です。 このインスタンスを、データに基づいてデジタル ツイン情報を更新できるように設定することも必要です。 

これをまだ設定していない場合、作成手順については Azure Digital Twins の "[*チュートリアル: エンドツーエンドのソリューションの接続*](tutorial-end-to-end.md)" に関するページを参照してください。 そのチュートリアルには、モデルとツインを含む Azure Digital Twins インスタンス、接続した Azure [IoT Hub](../iot-hub/about-iot-hub.md)、およびデータ フローを伝達するいくつかの [Azure 関数](../azure-functions/functions-overview.md)を設定する手順が示されています。

インスタンスを設定したときの以下の値が、この記事の後の方で必要になります。 これらの値を再度収集する必要がある場合は、以下のリンクを使用して手順を参照してください。
* Azure Digital Twins インスタンスの "**_ホスト名_**" ([ポータルで見つける](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure Event Hubs の "**_接続文字列_**" ([ポータルで見つける](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含む**デバイス シミュレーター**も使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins と IoT Hub の統合のサンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 サンプルのリンクに移動し、タイトルの下にある *[ZIP のダウンロード]* ボタンを選択して、お使いのマシンにサンプル プロジェクトを取得します。 ダウンロードしたフォルダーを解凍します。

デバイス シミュレーターは **Node.js** バージョン 10.0.x 以降に基づいています。 「[*Prepare your development environment*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md)」(開発環境を準備する) では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次の図は、Azure Digital Twins と Device Provisioning Service を使用したこのソリューションのアーキテクチャを示しています。 これにはデバイスのプロビジョニングと廃止の両方のフローが示されています。

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="エンドツーエンドのシナリオでのデバイスと複数の Azure サービスのビュー。サーモスタット デバイスと DPS との間でデータが送受信されます。データは DPS から IoT Hub にも送信されます。また、"Allocation" というラベルの付いた Azure 関数を通じて Azure Digital Twins にも送信されます。手動の "デバイスの削除" アクションからは、データが IoT Hub、Event Hubs、Azure Functions、Azure Digital Twins の順に送信されます。":::

この記事は、次の 2 つのセクションに分かれています。
* [*Device Provisioning Service を使用してデバイスを自動プロビジョニングする*](#auto-provision-device-using-device-provisioning-service)
* [*IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する*](#auto-retire-device-using-iot-hub-lifecycle-events)

アーキテクチャの各ステップの詳細については、この記事で後述する個々のセクションを参照してください。

## <a name="auto-provision-device-using-device-provisioning-service"></a>Device Provisioning Service を使用してデバイスを自動プロビジョニングする

このセクションでは、Device Provisioning Service を Azure Digital Twins に接続し、以下のパスを使用してデバイスを自動プロビジョニングします。 これは、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="プロビジョニングのフロー -- ソリューション アーキテクチャ図の抜粋。フローのセクションが数字でラベル付けされています。サーモスタット デバイスと DPS との間でデータが送受信されます (1 はデバイスから DPS へ、5 は DPS からデバイスへ)。データは DPS から IoT Hub にも送信されます (4)。また、"Allocation" というラベルの付いた Azure 関数 (2) を通じて Azure Digital Twins (3) にも送信されます。":::

プロセス フローの説明は次のとおりです。
1. デバイスから DPS エンドポイントへの通信によって、同一性を証明する識別情報が渡されます。
2. DPS によって、登録 ID とキーをデバイスの登録リストに照らして検証することでデバイスの同一性が検証され、割り当てを行う [Azure 関数](../azure-functions/functions-overview.md)が呼び出されます。
3. その Azure 関数により、Azure Digital Twins 内にデバイスの新しい[ツイン](concepts-twins-graph.md)が作成されます。
4. DPS によってデバイスが IoT ハブに登録され、デバイスの望ましいツイン状態が設定されます。
5. IoT ハブからデバイス ID 情報と IoT ハブの接続情報がデバイスに返されます。 これで、デバイスから IoT ハブに接続できるようになります。

以下のセクションでは、このデバイス自動プロビジョニング フローを設定する手順について説明します。

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service の作成

Device Provisioning Service を使用して新しいデバイスをプロビジョニングすると、そのデバイスの新しいツインが Azure Digital Twins 内に作成されます。

Device Provisioning Service のインスタンスを作成します。これが IoT デバイスのプロビジョニングに使用されます。 以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal で IoT Hub Device Provisioning Service を設定する*](../iot-dps/quick-setup-auto-provision.md)」。

次の Azure CLI コマンドを実行すると、デバイス プロビジョニング サービスが作成されます。 名前、リソース グループ、およびリージョンを指定する必要があります。 このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、Azure CLI が[コンピューターにインストールされている](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)場合はローカルで実行できます。

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Azure 関数の作成

次に、関数アプリ内に、HTTP 要求によってトリガーされる関数を作成します。 エンドツーエンドのチュートリアルで作成した関数アプリを使用することも ("[*チュートリアル: エンドツーエンドのソリューションの接続*](tutorial-end-to-end.md)")、自分で作成することもできます。

この関数は、Device Provisioning Service によって、新しいデバイスをプロビジョニングする[カスタム割り当てポリシー](../iot-dps/how-to-use-custom-allocation-policies.md)内で使用されます。 Azure Functions で HTTP 要求を使用する方法の詳細については、"[*Azure Functions の Azure HTTP 要求トリガー*](../azure-functions/functions-bindings-http-webhook-trigger.md)" に関するページを参照してください。

関数アプリ プロジェクト内に、新しい関数を追加します。 また、新しい NuGet パッケージをプロジェクト `Microsoft.Azure.Devices.Provisioning.Service` に追加します。

新しく作成された関数コード ファイル内に次のコードを貼り付けます。

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

ファイルを保存し、関数アプリを再発行します。 関数アプリを発行する手順については、エンドツーエンドのチュートリアルの「[*アプリの発行*](tutorial-end-to-end.md#publish-the-app)」のセクションを参照してください。

### <a name="configure-your-function"></a>関数を構成する

次に、先ほど作成した Azure Digital Twins インスタンスへの参照が含まれる環境変数を、前の関数アプリで設定する必要があります。 エンドツーエンドのチュートリアル (「[*チュートリアル: エンドツーエンドのソリューションを接続する*](tutorial-end-to-end.md)」) を使用した場合、その設定は既に構成されていることになります。

次の Azure CLI コマンドを使用して設定を追加します。

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

エンドツーエンドのチュートリアルのセクション「[*関数アプリにアクセス許可を割り当てる*](tutorial-end-to-end.md#assign-permissions-to-the-function-app)」で説明されているように、アクセス許可とマネージド ID ロールの割り当てが、関数アプリに対して正しく構成されていることを確認します。

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli-interactive
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>デバイス プロビジョニング登録の作成

次に、**カスタム割り当て関数**を使用して Device Provisioning Service 内に登録を作成する必要があります。 これを行うには、Device Provisioning Services のカスタム割り当てポリシーに関する記事にある「[*登録を作成する*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment)」および「[*一意のデバイス キーを派生させる*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys)」のセクションに記載されている手順に従います。

そのフローの作業中、作成したばかりの関数に登録をリンクします。そのためには、**デバイスをハブに割り当てる方法を選択する**ステップでその関数を選択します。 登録を作成した後、登録名と、プライマリまたはセカンダリの SAS キーは、この記事のデバイス シミュレーターを構成するために後で使用されます。

### <a name="set-up-the-device-simulator"></a>デバイス シミュレーターの設定

このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含むデバイス シミュレーターを使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins と IoT Hub の統合のサンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 まだサンプルをダウンロードしていない場合は、サンプルのリンクに移動し、タイトルの下にある *[ZIP のダウンロード]* ボタンを選択して、今すぐサンプル プロジェクトを取得します。 ダウンロードしたフォルダーを解凍します。

コマンド ウィンドウを開き、ダウンロードしたフォルダーに移動してから、*device-simulator* ディレクトリに移動します。 次のコマンドを使用して、プロジェクトの依存関係をインストールします。

```cmd
npm install
```

次に、 *.env.template* ファイルを *.env* と呼ばれる新しいファイルにコピーし、次の設定を入力します。

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

ファイルを保存して閉じます。

### <a name="start-running-the-device-simulator"></a>デバイス シミュレーターの実行を開始

引き続き、コマンド ウィンドウの *device-simulator* ディレクトリで、次のコマンドを使用してデバイス シミュレーターを開始します。

```cmd
node .\adt_custom_register.js
```

デバイスが登録されて IoT Hub に接続され、メッセージの送信が開始されるのがわかります。
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="デバイスの登録とメッセージの送信を示すコマンド ウィンドウ":::

### <a name="validate"></a>検証

この記事でフローを設定した結果として、デバイスは Azure Digital Twins に自動的に登録されます。 次の [Azure Digital Twins CLI](how-to-use-cli.md) コマンドを使用して、作成した Azure Digital Twins インスタンス内のデバイスのツインを検索します。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Azure Digital Twins インスタンス内にデバイスのツインがあることを確認します。
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="新しく作成されたツインを示すコマンド ウィンドウ":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する

このセクションでは、次のパスを使用してデバイスを自動的に廃止するために、IoT Hub ライフサイクル イベントを Azure Digital Twins に接続します。 これは、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="デバイス廃止のフロー -- ソリューション アーキテクチャ図の抜粋。フローのセクションが数字でラベル付けされています。この図では、サーモスタット デバイスは Azure サービスに接続していない状態で示されています。手動の "デバイスの削除" アクションからは、データが IoT Hub (1)、Event Hubs (2)、Azure Functions、Azure Digital Twins (3) の順に送信されます。":::

プロセス フローの説明は次のとおりです。
1. 外部または手動のプロセスによって、IoT Hub 内のデバイスの削除がトリガーされます。
2. IoT Hub によってデバイスが削除され、[デバイスのライフサイクル](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) イベントが生成されます。それが[イベント ハブ](../event-hubs/event-hubs-about.md)にルーティングされます。
3. Azure 関数によって、Azure Digital Twins 内のデバイスのツインが削除されます。

以下のセクションでは、このデバイス自動廃止フローを設定する手順について説明します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

次に、IoT Hub ライフサイクル イベントを受信するために使用される Azure [イベント ハブ](../event-hubs/event-hubs-about.md)を作成する必要があります。 

次の情報を使用して、[*イベント ハブの作成*](../event-hubs/event-hubs-create.md)のクイックスタートで説明されている手順を行います。
* エンドツーエンドのチュートリアル (「[*チュートリアル: エンドツーエンドのソリューションを接続する*](tutorial-end-to-end.md)」) を使用している場合は、エンドツーエンドのチュートリアル用に作成したリソース グループを再利用できます。
* イベント ハブに *lifecycleevents* または任意の別の名前を付け、作成した名前空間を覚えておいてください。 この後のセクションでライフサイクル関数と IoT Hub ルートを設定するときに使用します。

### <a name="create-an-azure-function"></a>Azure 関数の作成

次に、関数アプリ内に Event Hubs によってトリガーされる関数を作成します。 エンドツーエンドのチュートリアルで作成した関数アプリを使用することも ("[*チュートリアル: エンドツーエンドのソリューションの接続*](tutorial-end-to-end.md)")、自分で作成することもできます。 

イベント ハブ トリガーに *lifecycleevents* という名前を付け、そのイベント ハブ トリガーを前の手順で作成したイベント ハブに接続します。 別のイベント ハブ名を使用した場合は、以下のトリガー名で一致するように変更します。

この関数により、IoT Hub デバイスのライフサイクル イベントを使用して既存のデバイスが廃止されます。 ライフサイクル イベントの詳細については、IoT Hub の「[*非テレメトリ イベント*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。 Azure Functions で Event Hubs を使用する方法の詳細については、"[*Azure Functions に対する Azure Event Hubs トリガー*](../azure-functions/functions-bindings-event-hubs-trigger.md)" に関するページを参照してください。

発行された関数アプリ内で、"*イベント ハブ トリガー*" 型の新しい関数クラスを追加し、次のコードを貼り付けます。

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

プロジェクトを保存し、関数アプリを再度発行します。 関数アプリを発行する手順については、エンドツーエンドのチュートリアルの「[*アプリの発行*](tutorial-end-to-end.md#publish-the-app)」のセクションを参照してください。

### <a name="configure-your-function"></a>関数を構成する

次に、先ほど作成した Azure Digital Twins インスタンスへの、およびイベント ハブへの参照が含まれる環境変数を、前の関数アプリで設定する必要があります。 エンドツーエンドのチュートリアル (「[*チュートリアル: エンドツーエンドのソリューションを接続する*](./tutorial-end-to-end.md)」) を使用した場合、最初の設定は既に構成されていることになります。

次の Azure CLI コマンドを使用して設定を追加します。 このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、Azure CLI が[コンピューターにインストールされている](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)場合はローカルで実行できます。

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

次に、新しく作成したイベント ハブに接続するための、関数の環境変数を構成する必要があります。

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

エンドツーエンドのチュートリアルのセクション「[*関数アプリにアクセス許可を割り当てる*](tutorial-end-to-end.md#assign-permissions-to-the-function-app)」で説明されているように、アクセス許可とマネージド ID ロールの割り当てが、関数アプリに対して正しく構成されていることを確認します。

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>ライフサイクル イベントの IoT Hub ルートの作成

次に、デバイスのライフサイクル イベントをルーティングする IoT Hub ルートを設定する必要があります。 この場合、具体的には `if (opType == "deleteDeviceIdentity")` によって識別されるデバイス削除イベントをリッスンします。 これにより、デジタル ツイン項目の削除がトリガーされ、デバイスとそのデジタル ツインの廃止が最終処理されます。

IoT Hub ルートを作成する手順は、こちらの記事で説明されています: 「[*IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する*](../iot-hub/iot-hub-devguide-messages-d2c.md)」。 「*非テレメトリ イベント*」のセクションでは、**デバイスのライフサイクル イベント**をルートのデータ ソースとして使用できることを説明しています。

この設定に必要な手順は次のとおりです。
1. カスタムの IoT Hub イベント ハブ エンドポイントを作成します。 このエンドポイントは、[*イベント ハブの作成*](#create-an-event-hub)に関するセクションで作成したイベント ハブをターゲットとする必要があります。
2. *デバイスのライフサイクル イベント*のルートを追加します。 前のステップで作成したエンドポイントを使用します。 ルーティング クエリ `opType='deleteDeviceIdentity'` を追加することにより、削除イベントのみを送信するようにデバイスのライフサイクル イベントを制限できます。
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="ルートを追加する":::

このフローを終了すると、デバイスを廃止するための、エンドツーエンドのすべてのものが設定されます。

### <a name="validate"></a>検証

廃止のプロセスをトリガーするには、デバイスを IoT Hub から手動で削除する必要があります。

[この記事の前半](#auto-provision-device-using-device-provisioning-service)で、IoT Hub 内にデバイスを作成し、対応するデジタル ツインを作成しました。 

次に、IoT Hub にアクセスし、そのデバイスを削除します (これは、[Azure CLI コマンド](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete)または [Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs) で行うことができます)。 

デバイスが Azure Digital Twins から自動的に削除されます。 

次の [Azure Digital Twins CLI](how-to-use-cli.md) コマンドを使用して、Azure Digital Twins インスタンス内のデバイスのツインが削除されたことを確認します。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

デバイスのツインが Azure Digital Twins インスタンス内に見つからなくなったことがわかります。
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="ツインが見つからないことが表示されているコマンド ウィンドウ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースがもう必要ない場合は、次の手順に従って削除します。

Azure Cloud Shell またはローカルの Azure CLI から [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 リソース グループが削除され、Azure Digital Twins インスタンス、IoT ハブとハブ デバイスの登録、Event Grid トピックとそれに関連するサブスクリプション、イベント ハブ名前空間と両方の Azure Functions アプリが、ストレージなどの関連するリソースを含めて削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

```azurecli-interactive
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

次に、ダウンロードしたプロジェクトのサンプル フォルダーをローカル コンピューターから削除します。

## <a name="next-steps"></a>次のステップ

デバイスに対して作成したデジタル ツインは、Azure Digital Twins にフラット階層として格納されますが、モデル情報と組織の複数レベルの階層を使用して強化することができます。 この概念の詳細については、以下を参照してください。

* "[*概念: デジタル ツインとツイン グラフ*](concepts-twins-graph.md)"

Azure Digital Twins に既に格納されているモデルとグラフ データを使用して、この情報を自動的に提供するカスタム ロジックを作成できます。 ツイン グラフの情報の管理、アップグレード、および取得の詳細については、以下を参照してください。

* [*方法: デジタル ツインを管理する*](how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)