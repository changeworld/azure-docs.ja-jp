---
title: デバイス プロビジョニング サービスを使用したデバイスの自動管理
titleSuffix: Azure Digital Twins
description: Device Provisioning Service (DPS) を使用して、Azure Digital Twins で IoT デバイスをプロビジョニングおよび廃止する自動プロセスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad1351b7c9a649a553ce54422b99a13c286437d6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107297"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Device Provisioning Service (DPS) を使用して Azure Digital Twins でデバイスを自動管理する

この記事では、Azure Digital Twins と [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、Device Provisioning Service を使用して、Azure Digital Twins で IoT Hub デバイスを "**_プロビジョニング_**" および "**_廃止_**" するプロセスを自動化できます。 

"_プロビジョニング_" および "_廃止_" ステージの詳細と、すべてのエンタープライズ IoT プロジェクトに共通する一般的なデバイス管理ステージの詳細については、IoT Hub のデバイス管理ドキュメントで「[*デバイスのライフサイクル*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)」のセクションを参照してください。

## <a name="prerequisites"></a>前提条件

プロビジョニングを設定する前に、次の設定を行う必要があります。
* **Azure Digital Twins インスタンス**。 [ *「ハウツー: インスタンスと認証を設定する」*](how-to-set-up-instance-portal.md)の手順に従って、Azure デジタル ツイン インスタンスを作成します。 Azure portal ([手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) でインスタンスの **_ホスト名_** を収集します。
* **IoT ハブ** です。 手順については、この [ IoT Hub のクイック スタート](../iot-hub/quickstart-send-telemetry-cli.md)の「*IoT Hub の作成*」のセクションを参照してください。
* IoT Hub のデータに基づいてデジタル ツイン情報を更新する [**Azure 関数**](../azure-functions/functions-overview.md)。 [ *「ハウツー: IoT ハブ データを取り込み、この Azure 関数を作成する」*](how-to-ingest-iot-hub-data.md)の手順に従います。 この記事で使用する関数 **_名_** を収集します。

このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含む **デバイス シミュレーター** も使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins and IoT Hub provisioning integration sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 サンプルのリンクに移動し、タイトルの下にある *[Download ZIP]* ボタンを選択して、お使いのマシンにサンプル プロジェクトを取得します。 ダウンロードしたフォルダーを解凍します。

お使いのコンピューターに [**Node.js**](https://nodejs.org/download) がインストールされている必要があります。 デバイス シミュレーターは **Node.js** バージョン 10.0.x 以降に基づいています。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次の図は、Azure Digital Twins と Device Provisioning Service を使用したこのソリューションのアーキテクチャを示しています。 これにはデバイスのプロビジョニングと廃止の両方のフローが示されています。

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="エンドツーエンドのシナリオでのデバイスと複数の Azure サービスの図。サーモスタット デバイスと DPS との間でデータが送受信されます。データは DPS から IoT Hub にも送信されます。また、&quot;Allocation&quot; というラベルの付いた Azure 関数を通じて Azure Digital Twins にも送信されます。手動の &quot;デバイスの削除&quot; アクションからは、データが IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins の順に送信されます。" lightbox="media/how-to-provision-using-dps/flows.png":::

この記事は、次の 2 つのセクションに分かれています。
* [*Device Provisioning Service を使用してデバイスを自動プロビジョニングする*](#auto-provision-device-using-device-provisioning-service)
* [*IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する*](#auto-retire-device-using-iot-hub-lifecycle-events)

アーキテクチャの各ステップの詳細については、この記事で後述する個々のセクションを参照してください。

## <a name="auto-provision-device-using-device-provisioning-service"></a>Device Provisioning Service を使用してデバイスを自動プロビジョニングする

このセクションでは、Device Provisioning Service を Azure Digital Twins に接続し、以下のパスを使用してデバイスを自動プロビジョニングします。 これは、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="プロビジョニングのフローの図 -- ソリューション アーキテクチャ図の抜粋。フローのセクションが数字でラベル付けされています。サーモスタット デバイスと DPS との間でデータが送受信されます (1 はデバイスから DPS へ、5 は DPS からデバイスへ)。データは DPS から IoT Hub にも送信されます (4)。また、&quot;Allocation&quot; というラベルの付いた Azure 関数 (2) を通じて Azure Digital Twins (3) にも送信されます。" lightbox="media/how-to-provision-using-dps/provision.png":::

プロセス フローの説明は次のとおりです。
1. デバイスから DPS エンドポイントへの通信によって、同一性を証明する識別情報が渡されます。
2. DPS によって、登録 ID とキーをデバイスの登録リストに照らして検証することでデバイスの同一性が検証され、割り当てを行う [Azure 関数](../azure-functions/functions-overview.md)が呼び出されます。
3. その Azure 関数により、Azure Digital Twins 内にデバイスの新しい[ツイン](concepts-twins-graph.md)が作成されます。 ツインは、デバイスの **登録 ID** と同じ名前になります。
4. DPS によってデバイスが IoT ハブに登録され、デバイスの望ましいツイン状態が設定されます。
5. IoT ハブからデバイス ID 情報と IoT ハブの接続情報がデバイスに返されます。 これで、デバイスから IoT ハブに接続できるようになります。

以下のセクションでは、このデバイス自動プロビジョニング フローを設定する手順について説明します。

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service の作成

デバイス プロビジョニング サービスを使用して新しいデバイスをプロビジョニングすると、登録 ID と同じ名前でそのデバイスの新しいツインがAzure Digital Twins 内に作成されます。

Device Provisioning Service のインスタンスを作成します。これが IoT デバイスのプロビジョニングに使用されます。 以下の Azure CLI の手順を使用するか、Azure portal を使用することができます: 「[*クイック スタート:Azure portal で IoT Hub Device Provisioning Service を設定する*](../iot-dps/quick-setup-auto-provision.md)」。

次の Azure CLI コマンドを実行すると、デバイス プロビジョニング サービスが作成されます。 デバイスプロビジョニングサービスの名前、リソースグループ、およびリージョンを指定する必要があります。 デバイス プロビジョニング サービスがサポートされているリージョンを確認するには、「[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)」ページを参照してください。
このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、Azure CLI が[コンピューターにインストールされている](/cli/azure/install-azure-cli)場合はローカルで実行できます。

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>デバイス プロビジョニング サービスで使用する関数を追加する

「[前提条件](#prerequisites)」セクションで作成した関数アプリプロジェクト内で、デバイス プロビジョニング サービスと共に使用する新しい関数を作成します。 この関数は、Device Provisioning Service によって、新しいデバイスをプロビジョニングする[カスタム割り当てポリシー](../iot-dps/how-to-use-custom-allocation-policies.md)内で使用されます。

まず、コンピューター上の Visual Studio で 関数アプリのプロジェクトを開き、次の手順に従います。

#### <a name="step-1-add-a-new-function"></a>ステップ1: 新しい関数を追加する 

Visual Studio の関数アプリ プロジェクトに、種類が *HTTP トリガー* の新しい関数を追加します。

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="関数アプリ プロジェクトに Http トリガー型の Azure 関数を追加する Visual Studio ビューのスクリーンショット。" lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>手順 2: 関数コードを入力する

新しい NuGet パッケージをプロジェクトに追加します: [Microsoft.Azure.Devices.Provisioning.Servic](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)。 コード内で使用されているパッケージが既にプロジェクトに含まれていない場合は、さらにパッケージをプロジェクトに追加することが必要になる場合があります。

新しく作成された関数コード ファイルで、次のコードnい貼り付けて、関数の名前を *DpsAdtAllocationFunc.cs* に変更し、ファイルを保存します。

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>手順 3: 関数アプリを Azure に発行する

*DpsAdtAllocationFunc.cs* 関数を使用してプロジェクトを Azure の関数アプリに発行します。

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>デバイス プロビジョニング登録の作成

次に、**カスタム割り当て関数** を使用して Device Provisioning Service 内に登録を作成する必要があります。 手順に従って、デバイス プロビジョニングのサービス ドキュメントの「カスタム割り当てポリシー」の記事の「 [*登録の作成*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) 」セクションを参照してください。

このフローを実行するときは、次のオプションを選択して、作成した関数に登録をリンクしてください。

* **デバイスをハブに割り当てる方法を選択してください**: カスタム (Azure 関数)。
* **このグループを割り当てることができる IoT ハブを選択します:** IoT ハブの名前を選択するか、[*新しい IoT ハブをリンク*] ボタンを選択し、ドロップダウンから IoT ハブを選択します。

次に、[*新しい関数の選択*] ボタンをクリックして、関数アプリを登録グループにリンクします。 次に、以下の値を入力します。

* **サブスクリプション**: Azure サブスクリプションが自動的に設定されます。 適切なサブスクリプションであることを確認します。
* **関数アプリ**:ご利用の関数アプリの名前を選択します。
* **関数**: DpsAdtAllocationFunc を選択します。

詳細を保存してください。                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="[カスタム (Azure 関数を使用)] とセクション [デバイスをハブに割り当てる方法を選択してください] および [このグループを割り当てることができる IoT ハブを選択する] で IoT ハブを選択する、カスタム登録グループ詳細のウィンドウのスクリーンショット。また、サブスクリプション、ドロップダウンから関数アプリを選択し、[DpsAdtAllocationFunc] を選択します。" lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

登録を作成した後、登録の **主キー** は、この記事のデバイス シミュレーターを構成するために後で使用されます。

### <a name="set-up-the-device-simulator"></a>デバイス シミュレーターの設定

このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含むデバイス シミュレーターを使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins と IoT Hub の統合のサンプル](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 まだサンプルをダウンロードしていない場合は、サンプルのリンクに移動し、タイトルの下にある *[ZIP のダウンロード]* ボタンを選択して、今すぐサンプル プロジェクトを取得します。 ダウンロードしたフォルダーを解凍します。

#### <a name="upload-the-model"></a>モデルのアップロード

デバイス シミュレーターは、この ID を持つモデルを使用するサーモスタットタイプのデバイスです。`dtmi:contosocom:DigitalTwins:Thermostat;1` 。 この種類のデバイスのツインを作成する前に、このモデルを Azure Digital Twins にアップロードする必要があります。

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

モデルの詳細については、「[*ハウツー: モデルを管理する*](how-to-manage-model.md#upload-models)」を参照してください。

#### <a name="configure-and-run-the-simulator"></a>シミュレーターを構成して実行する

コマンド ウィンドウで、ダウンロードしたサンプル *Azure Digital Twins* と、前の手順で解凍した IoT Hub 統合に移動した後、*デバイスシミュレーター* ディレクトリに移動します。 次に、次のコマンドを使用して、プロジェクトの依存関係をインストールします。

```cmd
npm install
```

次に、デバイス シミュレーター ディレクトリで、.env という名前の新しいファイルに .env.template ファイルをコピーし、次の値を収集して設定を入力します。

* PROVISIONING_IDSCOPE: この値を取得するには、 [Azure portal](https://portal.azure.com/)でデバイス プロビジョニング サービスに移動し、メニュー オプションの [*概要*] を選択して、フィールド [*ID スコープ*] を探します。

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="[デバイス プロビジョニングの概要] ページの Azure portal ビューのスクリーンショット。 ID スコープ値をコピーします。" lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: デバイスの登録 ID を選択できます。
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: これは、前に設定した登録の主キーです。 この値を再度取得するには、Azure portal で デバイス プロビジョニング サービスに移動し、 *[登録の管理]* を選択してから、前に作成した登録グループを選択して、*主キー* をコピーします。

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="デバイス プロビジョニング サービスの [登録の管理] ページの Azure portal ビューのスクリーンショット。 SAS 主キーの値をコピーします。" lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

ここで、上記の値を使用して、.env ファイルの設定を更新します。

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

ファイルを保存して閉じます。

### <a name="start-running-the-device-simulator"></a>デバイス シミュレーターの実行を開始

引き続き、コマンド ウィンドウの *device-simulator* ディレクトリで、次のコマンドを使用してデバイス シミュレーターを開始します。

```cmd
node .\adt_custom_register.js
```

デバイスが登録されて IoT Hub に接続され、メッセージの送信が開始されるのがわかります。
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="デバイスの登録とメッセージの送信を示すコマンド ウィンドウのスクリーンショット" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>検証

この記事でフローを設定した結果として、デバイスは Azure Digital Twins に自動的に登録されます。 次の [Azure Digital Twins CLI](how-to-use-cli.md) コマンドを使用して、作成した Azure Digital Twins インスタンス内のデバイスのツインを検索します。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Azure Digital Twins インスタンス内にデバイスのツインがあることを確認します。
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="新しく作成されたツインを示すコマンド ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する

このセクションでは、次のパスを使用してデバイスを自動的に廃止するために、IoT Hub ライフサイクル イベントを Azure Digital Twins に接続します。 これは、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="デバイス廃止のフローの図 -- ソリューション アーキテクチャ図の抜粋。フローのセクションが数字でラベル付けされています。この図では、サーモスタット デバイスは Azure サービスに接続していない状態で示されています。手動の &quot;デバイスの削除&quot; アクションからは、データが IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3) の順に送信されます。" lightbox="media/how-to-provision-using-dps/retire.png":::

プロセス フローの説明は次のとおりです。
1. 外部または手動のプロセスによって、IoT Hub 内のデバイスの削除がトリガーされます。
2. IoT Hub によってデバイスが削除され、[デバイスのライフサイクル](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) イベントが生成されます。それが[イベント ハブ](../event-hubs/event-hubs-about.md)にルーティングされます。
3. Azure 関数によって、Azure Digital Twins 内のデバイスのツインが削除されます。

以下のセクションでは、このデバイス自動廃止フローを設定する手順について説明します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

次に、Azure [イベント ハブ](../event-hubs/event-hubs-about.md)を作成して IoT Hub ライフサイクル イベントを受け取ります。 

「[*イベント ハブを作成する*](../event-hubs/event-hubs-create.md)」クイックスタートに記載されている手順に従います。 イベントハブに *lifecycleevents* という名前を付けます。 このイベント ハブ名は、次のセクションで IoT Hub ルートと Azure 関数を設定するときに使用します。

次のスクリーンショットは、イベント ハブの作成を示しています。
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="[Azure portal] ウィンドウのスクリーンショット。 lifecycleevents という名前のイベント ハブを作成します。" lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>イベント ハブの SAS ポリシーを作成する

次に、 [Shared Access Signature (SAS) ポリシー](../event-hubs/authorize-access-shared-access-signature.md) を作成して、関数アプリでイベント ハブを構成する必要があります。
これを行うには、次の手順を実行します。
1. Azure portal で作成したばかりのイベント ハブに移動し、左側のメニューオプションで **[共有アクセス ポリシー]** を選択します。
2. **[追加]** を選択します。 開いた [*SAS ポリシーの追加*] ウィンドウで、任意のポリシー名を入力し、[*リッスン*] チェックボックスを選択します。
3. **［作成］** を選択します
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="イベント ハブの SAS ポリシーを追加する Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>関数アプリを使用してイベント ハブを構成する

次に、「[前提条件](#prerequisites)」セクションで設定した Azure 関数アプリを、新しいイベント ハブで動作するように構成します。 これを行うには、イベント ハブの接続文字列を使用して、関数アプリ内に環境変数を設定します。

1. 先ほど作成したポリシーを開き、 **[接続文字列-主キー]** の値をコピーします。

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="接続文字列-主キーをコピーする Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. 次の Azure CLI コマンドを使用して、関数アプリの設定に変数として接続文字列を追加します。 このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、Azure CLI が[コンピューターにインストールされている](/cli/azure/install-azure-cli)場合はローカルで実行できます。

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub ライフサイクル イベントを使用してインベントリから削除する関数を追加する

「[前提条件](#prerequisites)」セクションで作成した関数アプリ プロジェクト内で、IoT Hub ライフサイクル イベントを使用して既存のデバイスをインベントリから削除する新しい関数を作成します。

ライフサイクル イベントの詳細については、IoT Hub の「[*非テレメトリ イベント*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。 Azure Functions で Event Hubs を使用する方法の詳細については、"[*Azure Functions に対する Azure Event Hubs トリガー*](../azure-functions/functions-bindings-event-hubs-trigger.md)" に関するページを参照してください。

まず、コンピューター上の Visual Studio で 関数アプリのプロジェクトを開き、次の手順に従います。

#### <a name="step-1-add-a-new-function"></a>ステップ1: 新しい関数を追加する
     
Visual Studio の関数アプリ プロジェクトに、*イベント ハブ トリガー* 型の新しい関数を追加します。

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="関数アプリ プロジェクトにイベント ハブ トリガー型の Azure 関数を追加する Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>手順 2: 関数コードを入力する

新しく作成された関数コード ファイルで、次のコードnい貼り付けて、関数の名前を `DeleteDeviceInTwinFunc.cs` に変更し、ファイルを保存します。

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>手順 3: 関数アプリを Azure に発行する

*DeleteDeviceInTwinFunc.cs* 関数を使用してプロジェクトを Azure の関数アプリに発行します。

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>ライフサイクル イベントの IoT Hub ルートの作成

次に、デバイスのライフサイクル イベントをルーティングする IoT Hub ルートを設定します。 この場合、具体的には `if (opType == "deleteDeviceIdentity")` によって識別されるデバイス削除イベントをリッスンします。 これにより、デジタル ツイン項目の削除がトリガーされ、デバイスとそのデジタル ツインの廃止が最終処理されます。

まず、IoT ハブでイベント ハブ エンドポイントを作成する必要があります。 次に、IoT ハブにルートを追加して、ライフサイクルイベントをこのイベント ハブ エンドポイントに送信します。
イベント ハブ エンドポイントを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/)で、「[前提条件](#prerequisites)」セクションで作成した IoT ハブに移動し、左側のメニューオプションで **[メッセージ ルーティング]** を選択します。
2. **[カスタム エンドポイント]** タブを選択します。
3. **[+ 追加]** を選択し、 **[イベント ハブ]** を選択して、イベント ハブ型のエンドポイントを追加します。

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="イベント ハブ カスタム エンドポイントを追加するための Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. 開いた [*イベント ハブ エンドポイントを追加*] ウィンドウで次の値を選択します。
    * **エンドポイント名**: エンドポイント名を選択します。
    * **イベント ハブの名前空間**: ドロップダウン リストからイベント ハブの名前空間を選択します。
    * **イベント ハブ インスタンス**: 前の手順で作成したイベント ハブ名を選択します。
5. **［作成］** を選択します このウィンドウを開いたままにして、次の手順でルートを追加します。

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="イベント ハブ エンドポイントを追加するための Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

次に、前の手順で作成したエンドポイントに接続するルートを追加し、削除イベントを送信するルーティング クエリを追加します。 これらの手順に従って、ルートを作成します。

1. [*ルート*] タブに移動し、 **[追加]** を選択してルートを追加します。

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="イベントを送信するためのルートを追加する Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. 開いた [*ルートの追加*] ページで、次の値を選択します。

   * **名前**: ルートの名前を選択します。 
   * **エンドポイント**: ドロップダウン リストから、前の手順で作成したイベント ハブ エンドポイントを選択します。
   * **データソース**: *デバイスのライフサイクル イベント* を選択します。
   * **ルーティング クエリ**:「`opType='deleteDeviceIdentity'`」と入力します。 このクエリでは、削除イベントのみを送信するようにデバイスのライフサイクル イベントを制限します。

3. **[保存]** を選択します。

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="[Azure portal] ウィンドウのスクリーンショット。ライフサイクル イベントを送信するためのルートを追加します。" lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

このフローを終了すると、デバイスを廃止するための、エンドツーエンドのすべてのものが設定されます。

### <a name="validate"></a>検証

廃止のプロセスをトリガーするには、デバイスを IoT Hub から手動で削除する必要があります。

これは、[Azure CLI コマンド](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) または Azure portal で行うことができます。 次の手順に従って、Azure portal 内のデバイスを削除します。

1. IoT ハブに移動し、左側のメニューオプションで **[IoT デバイス]** を選択します。 
2. [この記事の前半](#auto-provision-device-using-device-provisioning-service)で選択したデバイス登録 ID を持つデバイスが表示されます。 または、Azure Digital Twins にツインがある場合は、削除するその他のデバイスを選択して、デバイスが削除された後にツインが自動的に削除されることを確認できます。
3. デバイスを選択し、 **[削除]** を選択します。

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="IoT デバイスからデバイス ツインを削除する Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Azure Digital Twins に反映された変更が表示されるまでに数分かかる場合があります。

次の [Azure Digital Twins CLI](how-to-use-cli.md) コマンドを使用して、Azure Digital Twins インスタンス内のデバイスのツインが削除されたことを確認します。

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

デバイスのツインが Azure Digital Twins インスタンス内に見つからなくなったことがわかります。

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="ツインが見つからないことを示すコマンド ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースがもう必要ない場合は、次の手順に従って削除します。

Azure Cloud Shell またはローカルの Azure CLI から [az group delete](/cli/azure/group#az-group-delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 リソース グループが削除され、Azure Digital Twins インスタンス、IoT ハブとハブ デバイスの登録、Event Grid トピックとそれに関連するサブスクリプション、イベント ハブ名前空間と両方の Azure Functions アプリが、ストレージなどの関連するリソースを含めて削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

次に、ダウンロードしたプロジェクトのサンプル フォルダーをローカル コンピューターから削除します。

## <a name="next-steps"></a>次のステップ

デバイスに対して作成したデジタル ツインは、Azure Digital Twins にフラット階層として格納されますが、モデル情報と組織の複数レベルの階層を使用して強化することができます。 この概念の詳細については、以下を参照してください。

* "[*概念: デジタル ツインとツイン グラフ*](concepts-twins-graph.md)"

Azure 関数で HTTP 要求を使用する方法の詳細については次を参照してください。

* [*Azure Functions 用の Azure Http 要求トリガー*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Azure Digital Twins に既に格納されているモデルとグラフ データを使用して、この情報を自動的に提供するカスタム ロジックを作成できます。 ツイン グラフの情報の管理、アップグレード、および取得の詳細については、以下を参照してください。

* [*方法: デジタル ツインを管理する*](how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)
