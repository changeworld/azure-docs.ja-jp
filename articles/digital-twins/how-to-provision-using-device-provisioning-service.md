---
title: デバイス プロビジョニング サービスを使用したデバイスの自動管理
titleSuffix: Azure Digital Twins
description: Device Provisioning Service (DPS) を使用して、Azure Digital Twins で IoT デバイスをプロビジョニングおよび廃止する自動プロセスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7078c136ebb072b3834af07e69fbd7d3c3d82a2b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771657"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Device Provisioning Service (DPS) を使用して Azure Digital Twins でデバイスを自動管理する

この記事では、Azure Digital Twins と [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) を統合する方法について説明します。

この記事で説明されているソリューションを使用すると、Device Provisioning Service を使用して、Azure Digital Twins で IoT Hub デバイスを "**_プロビジョニング_**" および "**_廃止_**" するプロセスを自動化できます。 

"_プロビジョニング_" および "_廃止_" ステージの詳細と、すべてのエンタープライズ IoT プロジェクトに共通する一般的なデバイス管理ステージの詳細については、IoT Hub のデバイス管理ドキュメントで「[デバイスのライフサイクル](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)」のセクションを参照してください。

## <a name="prerequisites"></a>前提条件

プロビジョニングを設定する前に、次のリソースを設定する必要があります。
* **Azure Digital Twins インスタンス**。 [インスタンスと認証の設定](how-to-set-up-instance-portal.md)に関する記事の手順に従って、Azure Digital Twins のインスタンスを作成します。 Azure portal ([手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) でインスタンスの **_ホスト名_** を収集します。
* **IoT ハブ** です。 手順については、「[IoT Hub のクイックスタート](../iot-hub/quickstart-send-telemetry-cli.md)」の「IoT Hub の作成」セクションを参照してください。
* IoT Hub のデータに基づいてデジタル ツイン情報を更新する [Azure 関数](../azure-functions/functions-overview.md)。 [IoT ハブ データの取り込み](how-to-ingest-iot-hub-data.md)に関する記事の手順に従って、この Azure 関数を作成します。 この記事で使用する関数 **_名_** を収集します。

<<<<<<< HEAD
このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含む **デバイス シミュレーター** も使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins and IoT Hub provisioning integration sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 サンプルのリンクに移動し、タイトルの下にある *[Download ZIP]* ボタンを選択して、お使いのマシンにサンプル プロジェクトを取得します。 ダウンロードしたフォルダーを解凍します。
=======
このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含む **デバイス シミュレーター** も使用します。 デバイス シミュレーターは次の場所にあります: [Azure Digital Twins と IoT Hub の統合のサンプル](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 サンプルのリンクに移動し、タイトルの下にある **[Browse Code]\(コードの参照\)** ボタンを選択して、お使いのマシン上でサンプル プロジェクトを取得します。 このボタンにより、サンプル用の GitHub リポジトリに移動します。 **[Code]\(コード\)** ボタンと、 **[Download ZIP]\(ZIP のダウンロード\)** を選択することによって、.ZIP ファイルとしてダウンロードできます。 
>>>>>>> repo_sync_working_branch

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="zip としてダウンロードする手順を強調表示した GitHub の digital-twins-iothub-integration リポジトリを示すスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

ダウンロードしたフォルダーを解凍します。

お使いのコンピューターに [Node.js](https://nodejs.org/download) がインストールされている必要があります。 デバイス シミュレーターは **Node.js** バージョン 10.0.x 以降に基づいています。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このソリューションには、デバイス プロビジョニング サービスを使用して、Azure Digital Twins でデバイスをプロビジョニングおよび廃止する手順が含まれています。

ソリューションにデバイスを割り当てるためには、サーモスタット デバイスと DPS の間でデータが流れます。 その後、データは DPS から IoT Hub に流れ、Azure 関数を介して Azure Digital Twins に流れます。

デバイスを廃止するためには、手動削除のデバイスからのデータが、IoT Hub、Event Hubs、Azure 関数を介して Azure Digital Twins に流れます。

次の図は、このアーキテクチャを示しています。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="データ フローが示されたエンドツーエンド シナリオでのデバイスと複数の Azure サービスの図。" lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

この記事は、それぞれこの完全なアーキテクチャの一部に焦点を当てた 2 つのセクションに分かれています。
* [Device Provisioning Service を使用してデバイスを自動プロビジョニングする](#auto-provision-device-using-device-provisioning-service)
* [IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>Device Provisioning Service を使用してデバイスを自動プロビジョニングする

このセクションでは、Device Provisioning Service を Azure Digital Twins に接続し、以下のパスを使用してデバイスを自動プロビジョニングします。 この図は、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="プロビジョニング フローの図 - サーモスタットから Azure Digital Twins へのデータに続くソリューション アーキテクチャ図の抜粋。" lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

プロセス フローの説明は次のとおりです。
1. デバイスから DPS エンドポイントへの通信によって、同一性を証明する識別情報が渡されます。
2. DPS によって、登録 ID とキーをデバイスの登録リストに照らして検証することでデバイスの同一性が検証され、割り当てを行う [Azure 関数](../azure-functions/functions-overview.md)が呼び出されます。
3. その Azure 関数により、Azure Digital Twins 内にデバイスの新しい[ツイン](concepts-twins-graph.md)が作成されます。 ツインは、デバイスの **登録 ID** と同じ名前になります。
4. DPS によってデバイスが IoT ハブに登録され、デバイスの望ましいツイン状態が設定されます。
5. IoT ハブからデバイス ID 情報と IoT ハブの接続情報がデバイスに返されます。 これで、デバイスから IoT ハブに接続できるようになります。

以下のセクションでは、このデバイス自動プロビジョニング フローを設定する手順について説明します。

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service の作成

デバイス プロビジョニング サービスを使用して新しいデバイスをプロビジョニングすると、登録 ID と同じ名前でそのデバイスの新しいツインがAzure Digital Twins 内に作成されます。

Device Provisioning Service のインスタンスを作成します。これが IoT デバイスのプロビジョニングに使用されます。 以下の Azure CLI の手順を使用するか、「[Azure Portal で IoT Hub Device Provisioning Service を設定する](../iot-dps/quick-setup-auto-provision.md)」に従って Azure portal を使用することができます。

次の Azure CLI コマンドを実行すると、デバイス プロビジョニング サービスが作成されます。 デバイスプロビジョニングサービスの名前、リソースグループ、およびリージョンを指定する必要があります。 デバイス プロビジョニング サービスがサポートされているリージョンを確認するには、「[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)」ページを参照してください。
このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、[Azure CLI がマシンにインストールされている](/cli/azure/install-azure-cli)場合はローカルで実行できます。

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>デバイス プロビジョニング サービスで使用する関数を追加する

[「前提条件」セクション](#prerequisites)で作成した関数アプリ プロジェクト内に、デバイス プロビジョニング サービスと共に使用する新しい関数を作成します。 この関数は、Device Provisioning Service によって、新しいデバイスをプロビジョニングする[カスタム割り当てポリシー](../iot-dps/how-to-use-custom-allocation-policies.md)内で使用されます。

まず、コンピューター上の Visual Studio で 関数アプリのプロジェクトを開き、次の手順に従います。

1. 最初に、Visual Studio の関数アプリ プロジェクトに、種類が "*HTTP トリガー*" の新しい関数を作成します。 この関数を作成する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)」を参照してください。

2. 新しい NuGet パッケージをプロジェクトに追加します: [Microsoft.Azure.Devices.Provisioning.Servic](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)。 コード内で使用されているパッケージが既にプロジェクトに含まれていない場合は、さらにパッケージをプロジェクトに追加することが必要になる場合があります。

3. 新しく作成された関数コード ファイルで、次のコードnい貼り付けて、関数の名前を *DpsAdtAllocationFunc.cs* に変更し、ファイルを保存します。

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. *DpsAdtAllocationFunc.cs* 関数が含まれるプロジェクトを Azure の関数アプリに発行します。 プロジェクトを発行する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

> [!IMPORTANT]
> 「[前提条件](#prerequisites)」セクションで関数アプリを初めて作成する場合、その関数のアクセス ロールが既に割り当てられていて、Azure Digital Twins インスタンスにアクセスするためのアプリケーション設定が構成されている可能性があります。 これらは、関数アプリ全体に対して 1 回実行する必要があるため、続行する前にアプリで完了したことを確認してください。 手順については、"*アプリ認証コードの記述*" に関する記事の「[発行されたアプリを構成する](how-to-authenticate-client.md#configure-published-app)」セクションを参照してください。

### <a name="create-device-provisioning-enrollment"></a>デバイス プロビジョニング登録の作成

次に、**カスタム割り当て関数** を使用して Device Provisioning Service 内に登録を作成する必要があります。 登録を作成するには、Device Provisioning Service のドキュメントのカスタム割り当てポリシーに関する記事の「[登録を作成する](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment)」セクションの手順のようにします。

このフローを実行するときは、次のオプションを選択して、作成した関数に登録をリンクします。

* **デバイスをハブに割り当てる方法を選択してください**: カスタム (Azure 関数)。
* **このグループを割り当てることができる IoT ハブを選択します:** IoT ハブの名前を選択するか、[*新しい IoT ハブをリンク*] ボタンを選択し、ドロップダウンから IoT ハブを選択します。

次に、[*新しい関数の選択*] ボタンをクリックして、関数アプリを登録グループにリンクします。 次に、以下の値を入力します。

* **サブスクリプション**: Azure サブスクリプションが自動的に設定されます。 適切なサブスクリプションであることを確認します。
* **関数アプリ**:ご利用の関数アプリの名前を選択します。
* **関数**: DpsAdtAllocationFunc を選択します。

詳細を保存してください。                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Azure portal の [カスタム登録グループの詳細] ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

登録を作成した後、登録の **主キー** は、この記事のデバイス シミュレーターを構成するために後で使用されます。

### <a name="set-up-the-device-simulator"></a>デバイス シミュレーターの設定

このサンプルでは、Device Provisioning Service を使用したプロビジョニングを含むデバイス シミュレーターを使用します。 デバイス シミュレーターは、[「前提条件」セクション](#prerequisites)でダウンロードした[Azure Digital Twins と IoT Hub の統合のサンプル](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)にあります。

#### <a name="upload-the-model"></a>モデルのアップロード

デバイス シミュレーターは、この ID を持つモデルを使用するサーモスタットタイプのデバイスです。`dtmi:contosocom:DigitalTwins:Thermostat;1` 。 この種類のデバイスのツインを作成する前に、このモデルを Azure Digital Twins にアップロードする必要があります。

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

モデルの詳細については、[モデルの管理](how-to-manage-model.md#upload-models)に関する記事を参照してください。

#### <a name="configure-and-run-the-simulator"></a>シミュレーターを構成して実行する

コマンド ウィンドウで、ダウンロードしたサンプル *Azure Digital Twins* と、前の手順で解凍した IoT Hub 統合に移動した後、*デバイスシミュレーター* ディレクトリに移動します。 次に、次のコマンドを使用して、プロジェクトの依存関係をインストールします。

```cmd
npm install
```

次に、デバイス シミュレーター ディレクトリで、.env という名前の新しいファイルに .env.template ファイルをコピーし、次の値を収集して設定を入力します。

* PROVISIONING_IDSCOPE: この値を取得するには、 [Azure portal](https://portal.azure.com/)でデバイス プロビジョニング サービスに移動し、メニュー オプションの [*概要*] を選択して、フィールド [*ID スコープ*] を探します。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="[デバイス プロビジョニングの概要] ページの Azure portal ビューのスクリーンショット。ID スコープ値が強調表示されています。" lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: デバイスの登録 ID を選択できます。
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: この環境変数は、前に設定した登録の主キーです。 この値を再度取得するには、Azure portal で デバイス プロビジョニング サービスに移動し、 *[登録の管理]* を選択してから、前に作成した登録グループを選択して、*主キー* をコピーします。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="デバイス プロビジョニング サービスの [登録の管理] ページの Azure portal ビューのスクリーンショット。 SAS 主キーの値が強調表示されています。" lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

ここで、上記の値を使用して、.env ファイルの設定を更新します。

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

ファイルを保存して閉じます。

### <a name="start-running-the-device-simulator"></a>デバイス シミュレーターの実行を開始

引き続き、コマンド ウィンドウの *device-simulator* ディレクトリで、次のコマンドを使用してデバイス シミュレーターを開始します。

```cmd
node .\adt_custom_register.js
```

デバイスが登録されて IoT Hub に接続され、メッセージの送信が開始されるのがわかります。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="デバイスの登録とメッセージの送信を示すコマンド ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>検証

この記事で設定したフローの結果として、デバイスが Azure Digital Twins に自動的に登録されます。 次の [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) コマンドを使用して、作成した Azure Digital Twins インスタンス内のデバイスのツインを検索します。

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Azure Digital Twins インスタンス内にデバイスのツインがあることを確認します。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="新しく作成されたツインを示すコマンド ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub ライフサイクル イベントを使用してデバイスを自動的に廃止する

このセクションでは、次のパスを使用してデバイスを自動的に廃止するために、IoT Hub ライフサイクル イベントを Azure Digital Twins に接続します。 この図は、[先ほど](#solution-architecture)示したアーキテクチャの全体図からの抜粋です。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="デバイス廃止フローの図 -- デバイス削除から Azure Digital Twins へのデータに続くソリューション アーキテクチャ図の抜粋。" lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

プロセス フローの説明は次のとおりです。
1. 外部または手動のプロセスによって、IoT Hub 内のデバイスの削除がトリガーされます。
2. IoT Hub によってデバイスが削除され、[デバイスのライフサイクル](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) イベントが生成されます。それが[イベント ハブ](../event-hubs/event-hubs-about.md)にルーティングされます。
3. Azure 関数によって、Azure Digital Twins 内のデバイスのツインが削除されます。

以下のセクションでは、このデバイス自動廃止フローを設定する手順について説明します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

次に、Azure [イベント ハブ](../event-hubs/event-hubs-about.md)を作成して IoT Hub ライフサイクル イベントを受け取ります。 

「[イベント ハブを作成する](../event-hubs/event-hubs-create.md)」クイックスタートに記載されている手順に従います。 イベントハブに *lifecycleevents* という名前を付けます。 このイベント ハブ名は、次のセクションで IoT Hub ルートと Azure 関数を設定するときに使用します。

次のスクリーンショットは、イベント ハブの作成を示しています。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="[Azure portal] ウィンドウのスクリーンショット。lifecycleevents という名前のイベント ハブを作成する方法が示されています。" lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>イベント ハブの SAS ポリシーを作成する

次に、 [Shared Access Signature (SAS) ポリシー](../event-hubs/authorize-access-shared-access-signature.md) を作成して、関数アプリでイベント ハブを構成する必要があります。
SAS ポリシーを作成するには:
1. Azure portal で作成したイベント ハブに移動し、左側のメニュー オプションで **[共有アクセス ポリシー]** を選択します。
2. **[追加]** を選択します。 開いた [*SAS ポリシーの追加*] ウィンドウで、任意のポリシー名を入力し、[*リッスン*] チェックボックスを選択します。
3. **［作成］** を選択します
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="イベント ハブの SAS ポリシーの追加方法が示されている Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>関数アプリを使用してイベント ハブを構成する

次に、[「前提条件」セクション](#prerequisites)で設定した Azure 関数アプリを、新しいイベント ハブで動作するように構成します。 イベント ハブの接続文字列を使用して、関数アプリ内に環境変数を設定することにより、関数を構成します。

1. 作成したポリシーを開き、 **[接続文字列 – 主キー]** の値をコピーします。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="接続文字列-主キーをコピーする方法が示されている Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. 次の Azure CLI コマンドを使用して、関数アプリの設定に変数として接続文字列を追加します。 このコマンドは、[Cloud Shell](https://shell.azure.com) で実行するか、[Azure CLI がマシンにインストールされている](/cli/azure/install-azure-cli)場合はローカルで実行できます。

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub ライフサイクル イベントを使用してインベントリから削除する関数を追加する

[「前提条件」セクション](#prerequisites)で作成した関数アプリ プロジェクト内に、IoT Hub ライフサイクル イベントを使用して既存のデバイスを削除する新しい関数を作成します。

ライフサイクル イベントの詳細については、IoT Hub の「[非テレメトリ イベント](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。 Azure Functions で Event Hubs を使用する方法の詳細については、"[Azure Functions に対する Azure Event Hubs トリガー](../azure-functions/functions-bindings-event-hubs-trigger.md)" に関するページを参照してください。

まず、コンピューター上の Visual Studio で 関数アプリのプロジェクトを開き、次の手順に従います。

1. 最初に、Visual Studio の関数アプリ プロジェクトに、種類が "*イベント ハブ トリガー*" の新しい関数を作成します。 この関数を作成する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)」を参照してください。

2. 新しい NuGet パッケージをプロジェクトに追加します: [Microsoft.Azure.Devices.Provisioning.Servic](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)。 コード内で使用されているパッケージが既にプロジェクトに含まれていない場合は、さらにパッケージをプロジェクトに追加することが必要になる場合があります。

3. 新しく作成された関数コード ファイルに次のコードを貼り付けて、関数の名前を *DeleteDeviceInTwinFunc.cs* に変更し、ファイルを保存します。

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. *DeleteDeviceInTwinFunc.cs* 関数が含まれるプロジェクトを Azure の関数アプリに発行します。 プロジェクトを発行する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

> [!IMPORTANT]
> 「[前提条件](#prerequisites)」セクションで関数アプリを初めて作成する場合、その関数のアクセス ロールが既に割り当てられていて、Azure Digital Twins インスタンスにアクセスするためのアプリケーション設定が構成されている可能性があります。 これらは、関数アプリ全体に対して 1 回実行する必要があるため、続行する前にアプリで完了したことを確認してください。 手順については、"*アプリ認証コードの記述*" に関する記事の「[発行されたアプリを構成する](how-to-authenticate-client.md#configure-published-app)」セクションを参照してください。

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>ライフサイクル イベントの IoT Hub ルートの作成

次に、デバイスのライフサイクル イベントをルーティングする IoT Hub ルートを設定します。 この場合、具体的には `if (opType == "deleteDeviceIdentity")` によって識別されるデバイス削除イベントをリッスンします。 このイベントにより、デジタル ツイン項目の削除がトリガーされ、デバイスとそのデジタル ツインの廃止プロセスが完了します。

まず、IoT ハブでイベント ハブ エンドポイントを作成する必要があります。 次に、IoT ハブにルートを追加して、ライフサイクルイベントをこのイベント ハブ エンドポイントに送信します。
イベント ハブ エンドポイントを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/)で、[「前提条件」セクション](#prerequisites)で作成した IoT ハブに移動し、左側のメニュー オプションで **[メッセージ ルーティング]** を選択します。
2. **[カスタム エンドポイント]** タブを選択します。
3. **[+ 追加]** を選択し、 **[イベント ハブ]** を選択して、イベント ハブ型のエンドポイントを追加します。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="イベント ハブ カスタム エンドポイントを追加する方法が示されている Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. 開いた [*イベント ハブ エンドポイントを追加*] ウィンドウで次の値を選択します。
    * **エンドポイント名**: エンドポイント名を選択します。
    * **イベント ハブの名前空間**: ドロップダウン リストからイベント ハブの名前空間を選択します。
    * **イベント ハブ インスタンス**: 前の手順で作成したイベント ハブ名を選択します。
5. **［作成］** を選択します このウィンドウを開いたままにして、次の手順でルートを追加します。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="イベント ハブ エンドポイントを追加する方法が示されている Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

次に、前の手順で作成したエンドポイントに接続するルートを追加し、削除イベントを送信するルーティング クエリを追加します。 これらの手順に従って、ルートを作成します。

1. [*ルート*] タブに移動し、 **[追加]** を選択してルートを追加します。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="イベントを送信するためのルートを追加する方法が示されている Visual Studio ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. 開いた [*ルートの追加*] ページで、次の値を選択します。

   * **名前**: ルートの名前を選択します。 
   * **エンドポイント**: ドロップダウン リストから、前の手順で作成したイベント ハブ エンドポイントを選択します。
   * **データソース**: *デバイスのライフサイクル イベント* を選択します。
   * **ルーティング クエリ**:「`opType='deleteDeviceIdentity'`」と入力します。 このクエリでは、削除イベントのみを送信するようにデバイスのライフサイクル イベントを制限します。

3. **[保存]** を選択します。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="ライフサイクル イベントを送信するためのルートを追加する方法が示されている Azure portal ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

このフローを終了すると、デバイスを廃止するための、エンドツーエンドのすべてのものが設定されます。

### <a name="validate"></a>検証

廃止のプロセスをトリガーするには、デバイスを IoT Hub から手動で削除する必要があります。

[Azure CLI コマンド](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete)を使用するか、Azure portal で、IoT Hub からデバイスを手動で削除できます。 次の手順に従って、Azure portal 内のデバイスを削除します。

1. IoT ハブに移動し、左側のメニューオプションで **[IoT デバイス]** を選択します。 
2. [この記事の前半](#auto-provision-device-using-device-provisioning-service)で選択したデバイス登録 ID を持つデバイスが表示されます。 また、Azure Digital Twins にツインがある場合は、削除するその他のデバイスを選択して、デバイスが削除された後にツインが自動的に削除されることを確認できます。
3. デバイスを選択し、 **[削除]** を選択します。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="IoT デバイスからデバイス ツインを削除する方法が示されている Azure portal のスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Azure Digital Twins に反映された変更が表示されるまでに数分かかる場合があります。

次の [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) コマンドを使用して、Azure Digital Twins インスタンス内のデバイスのツインが削除されたことを確認します。

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

デバイスのツインが Azure Digital Twins インスタンス内に見つからなくなったことがわかります。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="ツインが見つからないことを示すコマンド ウィンドウのスクリーンショット。" lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースがもう必要ない場合は、次の手順に従って削除します。

Azure Cloud Shell またはローカルの Azure CLI から [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) コマンドを使用すると、リソース グループ内の Azure リソースをすべて削除できます。 このコマンドにより、リソース グループが削除され、Azure Digital Twins インスタンス、IoT ハブとハブ デバイスの登録、Event Grid トピックとそれに関連するサブスクリプション、イベント ハブ名前空間と両方の Azure Functions アプリが、ストレージなどの関連するリソースを含めて削除されます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

次に、ダウンロードしたプロジェクトのサンプル フォルダーをローカル コンピューターから削除します。

## <a name="next-steps"></a>次のステップ

デバイスに対して作成したデジタル ツインは、Azure Digital Twins にフラット階層として格納されますが、モデル情報と組織の複数レベルの階層を使用して強化することができます。 この概念の詳細については、以下を参照してください。

* [デジタル ツインとツイン グラフ](concepts-twins-graph.md)

Azure 関数で HTTP 要求を使用する方法の詳細については次を参照してください。

* [Azure Functions 用の Azure Http 要求トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)

Azure Digital Twins に既に格納されているモデルとグラフ データを使用して、この情報を自動的に提供するカスタム ロジックを作成できます。 ツイン グラフの情報の管理、アップグレード、および取得について詳しくは、以下の方法ガイドをご覧ください。

<<<<<<< HEAD
* [*方法: デジタル ツインを管理する*](how-to-manage-twin.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)
=======
* [デジタル ツインを管理する](how-to-manage-twin.md)
* [ツイン グラフにクエリを実行する](how-to-query-graph.md)
>>>>>>> repo_sync_working_branch
