---
title: C 用 Azure IoT device SDK | Microsoft Docs
description: C 用 Azure IoT device SDK を使用し、IoT Hub と通信するデバイス アプリを作成する方法について説明します。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: lizross
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: cfbbae0cd7fa54b3403b52d1cced5e81f4df7438
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556023"
---
# <a name="azure-iot-device-sdk-for-c"></a>C 用 Azure IoT device SDK

**Azure IoT device SDK** は、**Azure IoT Hub** サービスとのメッセージの送受信プロセスを簡略化するために設計された一連のライブラリです。 SDK にはさまざまなバリエーションがあり、それぞれが特定のプラットフォームを対象としていますが、この記事では、 **C 用 Azure IoT device SDK** について説明します。

> [!NOTE]
> Embedded C SDK は、制約のあるデバイスの代替手段であり、BYON (bring your own network) アプローチをサポートします。 IoT 開発者はデバイス ソリューションを作成するために、任意の MQTT クライアント、TLS、ソケットを自由に使用できます。 [Embedded C SDK の詳細については、こちらを参照してください](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

C 用 Azure IoT デバイス SDK は、移植性を最大限まで高めるために ANSI C (C99) で記述されています。 この機能により、複数のプラットフォームとデバイス、特にディスクとメモリの量を最小限に抑えることが優先される環境でライブラリを操作しやすくなります。

この SDK は幅広いプラットフォームでテストされています (詳細は、[Azure Certified for IoT デバイス カタログ](https://devicecatalog.azure.com/)に関するページを参照してください)。 この記事には、Windows プラットフォームで実行されるサンプル コードのチュートリアルが含まれますが、この記事で説明するコードはサポートされるプラットフォーム全体で同じです。

次のビデオでは、C 用 Azure IoT SDK の概要が紹介されています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

この記事では、C 用 Azure IoT device SDK のアーキテクチャについて紹介します。また、デバイスのライブラリを初期化する方法、IoT Hub にデータを送信する方法、IoT Hub からメッセージを受信する方法を示します。 この記事の情報は、SDK を使用し始めるにあたり十分な内容ですが、ライブラリに関する追加情報を入手できる場所も紹介します。

## <a name="sdk-architecture"></a>SDK のアーキテクチャ

[**C 用 Azure IoT device SDK**](https://github.com/Azure/azure-iot-sdk-c) は GitHub リポジトリから入手でき、API の詳細は [C API リファレンス](/azure/iot-hub/iot-c-sdk-ref/)に関するページで確認できます。

最新バージョンのライブラリは、このリポジトリの **メイン** ブランチにあります。

  ![リポジトリのメイン ブランチのスクリーンショット](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* SDK の中心となる実装は **iothub\_client** フォルダー内にあり、このフォルダーには SDK 内の最下位の API 層の実装 (**IoTHubClient**) が格納されています。 **IoTHubClient** ライブラリには、IoT Hub とのメッセージの送受信のために、未加工メッセージングを実装する API が含まれています。 このライブラリを使用する場合、メッセージのシリアル化はユーザー自身が実装する必要がありますが、IoT Hub と通信するためのその他の詳細は自動で処理されます。

* **IoTHubClient** ライブラリは、次に示すその他のオープン ソース ライブラリに依存しています。

  * [Azure C 共有ユーティリティ](https://github.com/Azure/azure-c-shared-utility) ライブラリ。複数の Azure 関連 C SDK で必要となる基本タスク (文字列、リストの操作、IO など) に共通する機能を提供します。

  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) ライブラリ。リソースに制約のあるデバイス用に最適化された AMQP のクライアント側の実装です。

  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) ライブラリ。MQTT プロトコルを実装する汎用ライブラリで、リソースに制約のあるデバイス用に最適化されています。

これらのライブラリの使用については、サンプル コードを確認した方が簡単に理解できます。 次のセクションでは、SDK に含まれているいくつかのサンプル アプリケーションについて説明します。 このチュートリアルは、SDK のアーキテクチャの層のさまざまな機能や、API の動作のしくみの概要を理解するうえで役立ちます。

## <a name="before-you-run-the-samples"></a>サンプルを実行する前に

C 用 Azure IoT device SDK のサンプルを実行する前に、Azure サブスクリプションで [IoT Hub サービスのインスタンスを作成](iot-hub-create-through-portal.md)する必要があります。 そのうえで、次のタスクを実行してください。

* 開発環境を準備する
* デバイスの資格情報を取得する

### <a name="prepare-your-development-environment"></a>開発環境を準備する

パッケージは一般的なプラットフォーム (Windows 用 NuGet、Debian と Ubuntu 用 apt_get など) に提供され、利用可能な場合にサンプルはこれらのパッケージを使用します。 場合によっては、デバイス向けに、またはデバイス上で SDK をコンパイルする必要があります。 SDK をコンパイルする必要がある場合は、GitHub リポジトリで「[Prepare your development environment (開発環境の準備)](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」を参照してください。

サンプル アプリケーション コードを取得するには、GitHub から SDK のコピーをダウンロードします。 [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)の **メイン** ブランチからソースのコピーを入手します。


### <a name="obtain-the-device-credentials"></a>デバイスの資格情報を取得する

これでサンプル ソース コートが手に入ったので、次にデバイスの資格情報のセットを取得します。 デバイスを IoT ハブにアクセスできるようにするには、まず、そのデバイスを IoT Hub ID レジストリに追加する必要があります。 デバイスを追加すると、そのデバイスから IoT ハブに接続するために必要な、デバイスの資格情報のセットが得られます。 次のセクションで確認するサンプル アプリケーションでは、これらの資格情報は **デバイスの接続文字列** の形式であると想定しています。

IoT ハブの管理に役立つオープン ソース ツールがいくつかあります。

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) という Windows アプリケーション。

* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) と呼ばれるクロスプラットフォームの Visual Studio Code 拡張機能。

* [Azure CLI 向けの IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)と呼ばれるクロスプラットフォーム Python CLI。

このチュートリアルでは、グラフィカル *デバイス エクスプローラー* ツールを使います。 VS Code で開発する場合は、*VS Code 用の Azure IoT Tools* を使用できます。 CLI ツールを使いたい場合は、"*Azure CLI 2.0 向けの IoT 拡張機能*" ツールを使うこともできます。

デバイス エクスプローラー ツールは、Azure IoT サービス ライブラリを使用して、デバイスの追加など、IoT Hub のさまざまな機能を実行します。 デバイス エクスプローラー ツールを使用してデバイスを追加すると、デバイス用の接続文字列が表示されます。 この接続文字列は、サンプル アプリケーションを実行するために必要です。

デバイス エクスプローラー ツールに慣れていない方のために、次の手順で、デバイス エクスプローラー ツールを使用してデバイスを追加し、デバイスの接続文字列を取得する方法について説明します。

1. デバイス エクスプローラー ツールをインストールするには、「[How to use Device Explorer for IoT Hub devices (IoT Hub デバイス向けにデバイス エクスプローラーを使用する方法)](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/tools/)」を参照してください。

1. プログラムを実行すると、次のインターフェイスが表示されます。

   ![Device Explorer ツインのスクリーンショット](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. **IoT Hub の接続文字列** を最初のフィールドに入力し、 **[更新]** をクリックします。 この手順により、IoT Hub と通信できるようにツールが構成されます。 

**接続文字列** は、 **[IoT Hub サービス]**  >  **[設定]**  >  **[共有アクセス ポリシー]**  >  **[iothubowner]** で確認できます。

1. IoT Hub の接続文字列を構成したら、 **[管理]** タブをクリックします。

   ![Device Explorer ツイン/管理のスクリーンショット](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

このタブで、IoT Hub に登録されたデバイスを管理します。

1. **[作成]** をクリックしてデバイスを作成します。 ダイアログが、一連のキー (プライマリおよびセカンダリ) が入力された状態で表示されます。 **デバイス ID** を入力し、 **[作成]** をクリックします。

   ![デバイス作成のスクリーンショット](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. デバイスが作成されたら、デバイスの一覧が更新され、今作成したデバイスを含むすべての登録済みデバイスが表示されます。 新しいデバイスを右クリックすると、次のメニューが表示されます。

   ![Device Explorer ツインの右クリックの結果](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. **[Copy connection string for selected device (選択したデバイスの接続文字列のコピー)]** を選択すると、デバイスの接続文字列がクリップボードにコピーされます。 デバイス接続文字列のコピーを保存しておきます。 次のセクションで説明するサンプル アプリケーションを実行するときにこれが必要になります。

前の手順が完了すると、コードを実行する準備が整います。 多くのサンプルでは、メインのソース ファイルの上部に、接続文字列を入力するための定数が使用されています。 たとえば、**iothub_client\_samples\_iothub_convenience_sample** アプリケーションの対応する行は次のとおりです。

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>IoTHubClient ライブラリを使用する

[azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) リポジトリ内の **iothub\_client** フォルダーに **samples** フォルダーがあり、**iothub\_client\_sample\_mqtt** というアプリケーションが格納されています。

**iothub_client\_samples\_iothub_convenience_sample** アプリケーションの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![Visual Studio のソリューション エクスプローラー](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Visual Studio で、プロジェクトのターゲットを最新バージョンに変更することを要求するプロンプトが表示された場合は、そのプロンプトを受け入れてください。

このソリューションには単一のプロジェクトが含まれています。 このソリューションには 4 つの NuGet パッケージがインストールされています。

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

SDK を使った作業には必ず **Microsoft.Azure.C.SharedUtility** パッケージが必要となります。 このサンプルでは MQTT プロトコルを使用するため、**Microsoft.Azure.umqtt** パッケージと **Microsoft.Azure.IoTHub.MqttTransport** パッケージを含める必要があります (AMQP と HTTPS にも同等のパッケージが存在します)。 サンプルでは **IoTHubClient** ライブラリが使用されているため、ソリューション内に **Microsoft.Azure.IoTHub.IoTHubClient** パッケージも含める必要があります。

サンプル アプリケーションの実装は、**iothub_client\_samples\_iothub_convenience_sample** ソース ファイル内にあります。

次の手順では、このサンプル アプリケーションを使用して、**IoTHubClient** ライブラリの使用に必要な内容を説明します。

### <a name="initialize-the-library"></a>ライブラリを初期化する

> [!NOTE]
> ライブラリの使用を開始する前に、プラットフォーム固有の初期化の実行が必要になる場合があります。 たとえば、Linux 上で AMQP を使用する計画がある場合は、OpenSSL ライブラリを初期化する必要があります。 [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)のサンプルはクライアントの起動時にユーティリティ関数 **platform\_init** を呼び出し、終了する前に **platform\_deinit** 関数を呼び出します。 これらの関数は、platform.h ヘッダー ファイルで宣言されます。 ターゲットとするプラットフォームについて[リポジトリ](https://github.com/Azure/azure-iot-sdk-c)でこれらの関数の定義を確認し、クライアントにいずれかのプラットフォーム固有の初期化コードを含める必要があるかどうかを判断する必要があります。

ライブラリで作業を開始するには、最初に、IoT Hub クライアントのハンドルを割り当てる必要があります。

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

デバイス エクスプローラー ツールから取得したデバイスの接続文字列のコピーをこの関数に渡します。 また、使用する通信プロトコルを指定します。 この例では MQTT を使用しますが、AMQP と HTTPS も使用できます。

有効な **IOTHUB\_CLIENT\_HANDLE** がある場合は、その API の呼び出しを開始して、IoT Hub との間でメッセージを送受信できます。

### <a name="send-messages"></a>メッセージを送信する

このサンプル アプリケーションによって、メッセージを IoT ハブに送信するループがセットアップされます。 次のスニペットが必要です。

- メッセージを作成する。
- プロパティをメッセージに追加する。
- メッセージを送信する。

まず、次のようにメッセージを作成します。

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

メッセージを送信するたびに、データが送信されるときに呼び出されるコールバック関数への参照を指定します。 この例では、コールバック関数は **SendConfirmationCallback** です。 次のスニペットは、このコールバック関数を示しています。

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

メッセージの処理が終了したときに呼び出される **IoTHubMessage\_Destroy** 関数に注意してください。 この関数は、メッセージを作成したときに割り当てられたリソースを解放します。

### <a name="receive-messages"></a>メッセージを受信する

メッセージの受信は非同期操作です。 最初に、デバイスがメッセージを受信したときに呼び出すコールバックを登録します。

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

最後のパラメーターは任意のものを指す void ポインターです。 このサンプルのポインターは整数を指していますが、より複雑なデータ構造を指すこともできます。 このパラメーターにより、このコールバック関数を、この関数の呼び出し元と共有した状態で操作できます。

デバイスがメッセージを受信すると、登録済みのコールバック関数が呼び出されます。 このコールバック関数は次のものを受信します。

* メッセージのメッセージ ID と関連付け ID。
* メッセージのコンテンツ。
* メッセージの任意のカスタム プロパティ。

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

**IoTHubMessage\_GetByteArray** 関数を使用してメッセージを取得します。この例では文字列です。

### <a name="uninitialize-the-library"></a>ライブラリの初期化を解除する

イベントの送信とメッセージの受信が完了すると、IoT ライブラリの初期化を解除することができます。 これを行うには、次の関数呼び出しを発行します。

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

この呼び出しにより、以前に割り当てられていたリソースが、**IoTHubClient\_CreateFromConnectionString** 関数によって解放されます。

ご覧のように、**IoTHubClient** ライブラリを使用すると、簡単にメッセージを送受信できます。 使用するプロトコルなど (開発者の観点からは単純な構成オプションです)、IoT Hub との通信に関する詳細情報は、ライブラリで処理されます。

## <a name="next-steps"></a>次の手順

この記事では、**C 用 Azure IoT device SDK** のライブラリの使用方法の基本を説明しました。SDK の内容、そのアーキテクチャ、Windows サンプルの実行を開始する方法を理解するうえで十分な情報を提供しました。 次の記事では、 [IoTHubClient ライブラリに関する詳細](iot-hub-device-sdk-c-iothubclient.md)を説明することで、引き続き SDK について記述します。

IoT Hub 用の開発の詳細については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)
