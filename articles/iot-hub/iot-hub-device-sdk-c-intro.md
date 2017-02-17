---
title: "C 用 Azure IoT device SDK | Microsoft Docs"
description: "C 用 Azure IoT device SDK を使用し、IoT Hub と通信するデバイス アプリを作成する方法について説明します。"
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: obloch
translationtype: Human Translation
ms.sourcegitcommit: ef066a50b71389cb1cdd3bb0f8d342a34a4cc722
ms.openlocfilehash: 0033f1cc036e1eef63f0728be8d5d73c8dbbccf6


---
# <a name="azure-iot-device-sdk-for-c"></a>C 用 Azure IoT device SDK
**Azure IoT device SDK** は、**Azure IoT Hub** サービスとのイベントの送信とメッセージの受信のプロセスを簡略化するために設計された一連のライブラリです。 SDK にはさまざまなバリエーションがあり、それぞれが特定のプラットフォームを対象としていますが、この記事では、 **C 用 Azure IoT device SDK**について説明します。

C 用 Azure IoT デバイス SDK は、移植性を最大限まで高めるために ANSI C (C99) で記述されています。 これにより、さまざまなプラットフォームとデバイス、特にディスクとメモリの量を最小限に抑えることが優先される環境で操作する場合に適しています。  

この SDK は幅広いプラットフォームでテストされています (詳細は、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsuite.com/)に関するページを参照してください)。 この記事には、Windows プラットフォームで実行されるサンプル コードのチュートリアルが含まれますが、この記事で説明するコードはサポートされるプラットフォーム全体でまったく同じであることに留意してください。

この記事では、C 用 Azure IoT デバイス SDK のアーキテクチャについて紹介します。また、デバイスのライブラリを初期化する方法、IoT Hub にイベントを送信する方法や IoT Hub からメッセージを受信する方法の例を示します。 この記事の情報は、SDK を使用し始めるにあたり十分な内容ですが、ライブラリに関する追加情報を入手できる場所も紹介します。

## <a name="sdk-architecture"></a>SDK のアーキテクチャ
[**C 用 Azure IoT device SDK**](https://github.com/Azure/azure-iot-sdk-c) は GitHub リポジトリから入手でき、API の詳細は [C API リファレンス](https://azure.github.io/azure-iot-sdk-c/index.html)に関するページで確認できます。

最新バージョンのライブラリは、このリポジトリの **master** ブランチにあります。

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

このリポジトリには、Azure IoT デバイス SDK のファミリ全体が含まれています。 ただし、この記事では、 *c* フォルダーにある **C 用** Azure IoT device SDK を取り扱います。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* SDK の中心となる実装は **iothub\_client** フォルダー内にあり、このフォルダーには SDK 内の最下位の API 層の実装 (**IoTHubClient**) が格納されています。 **IoTHubClient** ライブラリには、IoT Hub にメッセージを送信するだけでなく IoT Hub からメッセージを受信するために、未加工メッセージングを実装する API が含まれています。 このライブラリを使用する場合、メッセージのシリアル化はユーザー自身が実装する必要がありますが (最終的には以下のシリアライザー サンプルを使用)、IoT Hub と通信するためのその他の詳細は自動で処理されます。
* **serializer** フォルダーには、クライアント ライブラリを使用して Azure IoT Hub に送信する前にデータをシリアル化する方法を示す、ヘルパー関数とサンプルが含まれています。 シリアライザーの使用は必須ではなく、利便性のためだけに提供されていることに注意してください。 **serializer** ライブラリを使用する場合は、まず、IoT Hub から受信するメッセージだけでなく IoT Hub に送信するイベントを指定するモデルを定義します。 いったんモデルを定義すれば、SDK が提供する API にアクセスして、シリアル化の詳細を気にすることなく、イベントやメッセージを簡単に処理できます。
  ライブラリは、いくつかのプロトコル (MQTT、AMQP) を使用してトランスポートを実装するその他のオープン ソース ライブラリに依存しています。
* **IoTHubClient** ライブラリは、次に示すその他のオープン ソース ライブラリに依存しています。
  * [Azure C 共有ユーティリティ](https://github.com/Azure/azure-c-shared-utility) ライブラリ。複数の Azure 関連 C SDK で必要となる基本タスク (文字列、リストの操作、IO など) に共通する機能を提供します。
  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) ライブラリ。リソース制約デバイス用に最適化された AMQP のクライアント側の実装です。
  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) ライブラリ。MQTT プロトコルを実装する汎用ライブラリで、リソース制約デバイス用に最適化されています。

これらはすべて、サンプル コードを確認した方が簡単に理解できます。 次のセクションでは、SDK に含まれているいくつかのサンプル アプリケーションについて説明します。 API の動作のしくみの概要だけでなく、SDK のアーキテクチャの層のさまざまな機能を理解するうえで役立つ内容です。

## <a name="before-running-the-samples"></a>サンプルを実行する前に
C 用 Azure IoT device SDK のサンプルを実行する前に、インスタンスがない場合は、Azure サブスクリプションでサービスのインスタンスを作成し、次の 2 つのタスクを完了する必要があります。

* 開発環境を準備する
* デバイスの資格情報を取得する

Azure サブスクリプションで Azure IoT Hub のインスタンスを作成する必要がある場合は、 [こちら](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md)の手順に従ってください。

SDK に含まれている [readme ファイル](https://github.com/Azure/azure-iot-sdk-c) には、開発環境の準備とデバイスの資格情報の取得についての手順が記載されています。
次のセクションで、これらの手順について追加でコメントしています。

### <a name="preparing-your-development-environment"></a>開発環境の準備
パッケージは一部のプラットフォーム (Windows 用 NuGet、Debian と Ubuntu 用 apt_get など) に提供され、利用可能な場合にサンプルはこれらのパッケージを使用しますが、次の手順では、コードから直接ライブラリとサンプルをビルドする方法について詳しく説明します。

最初に、GitHub から SDK のコピーを入手して、ソースを作成する必要があります。 **GitHub リポジトリ** の [master](https://github.com/Azure/azure-iot-sdk-c)ブランチからソースのコピーを入手します。

ソースのコピーをダウンロードしたら、SDK の記事「 [Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」で説明されている手順を完了する必要があります。

ここでは、準備ガイドで説明されている手順を完了するのに役立ついくつかのヒントを紹介します。

* **CMake **ユーティリティをインストールするとき、**すべてのユーザー**の PATH システム環境変数に **CMake** を追加するオプションを選択します (**現在のユーザー**に追加しても効果は同じです)。
  
  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)
* **開発者コマンド プロンプト for VS2015**を開く前に、Git コマンド ライン ツールをインストールします。 これらのツールをインストールするには、次の手順を実行します。
  
  1. **Microsoft Visual Studio 2015** セットアップ プログラムを起動します (または **[プログラムと機能]** コントロール パネルの **[Microsoft Visual Studio 2015]** を選択して **[変更]** をクリックします)。
  2. インストーラーで **[Git for Windows]** 機能が選択されていることを確認します。ただし、IDE 統合機能を使用するために、**[Visual Studio 向け GitHub 拡張]** オプションもオンにすることができます。
     
        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
  3. セットアップ ウィザードを実行してツールをインストールします。
  4. Git ツールの **bin** ディレクトリを **PATH** システム環境変数に追加します。 Windows では、次のようになります。
     
        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

「 [Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) 」ページで説明されているすべての手順を完了すると、サンプル アプリケーションをコンパイルする準備ができます。

### <a name="obtaining-device-credentials"></a>デバイスの資格情報の取得
これで開発環境がセットアップできましたので、次にデバイスの資格情報のセットを取得します。  デバイスを IoT ハブにアクセスできるようにするには、まず、そのデバイスを IoT Hub ID レジストリに追加する必要があります。 デバイスを追加すると、そのデバイスを IoT Hub に接続するために必要な、デバイスの資格情報のセットが得られます。 次のセクションで確認するサンプル アプリケーションでは、これらの資格情報は **デバイスの接続文字列**の形式であると想定されています。

SDK のオープン ソース リポジトリで提供されるツールがいくつかあり、IoT Hub を管理するために役立ちます。 その&1; つは、*デバイス エクスプローラー*と呼ばれる Windows アプリケーションです。もう&1; つは、*iothub-explorer* と呼ばれる node.js ベースのクロス プラットフォーム CLI ツールです。 これらのツールの詳細については、[こちら](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md)を参照してください。

この記事では、Windows のサンプルの実行を説明するときに、デバイス エクスプローラー ツールを使用しています。 ただし、CLI ツールを使用する場合は、iothub-explorer を使用することもできます。

[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ツールは、Azure IoT サービス ライブラリを使用して、デバイスの追加など、IoT Hub のさまざまな機能を実行します。 デバイス エクスプローラー ツールを使用してデバイスを追加すると、対応する接続文字列が表示されます。 この接続文字列は、サンプル アプリケーションを実行するために必要です。

このプロセスにまだ詳しくない方のために、次の手順で、デバイス エクスプローラー ツールを使用してデバイスを追加し、デバイスの接続文字列を取得する方法について説明します。

デバイス エクスプローラー ツールの Windows インストーラーは、[SDK リリース ページ](https://github.com/Azure/azure-iot-sdks/releases)にあります。 ただし、**Microsoft Visual Studio 2015** で **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** を開いてソリューションをビルドすることにより、コードから直接ツールを実行することもできます。

プログラムを実行すると、次のインターフェイスが表示されます。

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

**IoT Hub の接続文字列**を最初のフィールドに入力し、**[更新]** をクリックします。 これにより、IoT Hub と通信できるようにツールが構成されます。

IoT Hub の接続文字列を構成したら、 **[Management (管理)]** タブをクリックします。

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

ここで、IoT Hub に登録されたデバイスを管理します。

**[作成]** をクリックしてデバイスを作成することができます。 ダイアログ ボックスが、一連のキー (プライマリおよびセカンダリ) が入力された状態で表示されます。 ここで必要なのは、**[デバイス ID]** を入力して、**[作成]** をクリックすることだけです。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

デバイスを作成したら、今作成したデバイスを含めたすべての登録済みデバイスで、デバイスの一覧が更新されます。 新しいデバイスを右クリックすると、次のメニューが表示されます。

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

**[Copy connection string for selected device (選択したデバイスの接続文字列のコピー)]** オプションを選択すると、デバイス接続文字列がクリップボードにコピーされます。 デバイス接続文字列のコピーを保存しておきます。 次のセクションで説明するサンプル アプリケーションを実行するときにこれが必要になります。

前の手順が完了すると、コードを実行する準備が整います。 どちらのサンプルにも、メインのソース ファイルの上部に、接続文字列を入力するための定数が使用されています。 たとえば、**iothub\_client\_sample\_amqp** アプリケーションの対応する行は次のとおりです。

```
static const char* connectionString = "[device connection string]";
```

ここに使用しているデバイスの接続文字列を入力し、ソリューションを再コンパイルすると、サンプルを実行することができます。

## <a name="iothubclient"></a>IoTHubClient
[azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) リポジトリ内の **iothub\_client** フォルダーに **samples** フォルダーがあり、**iothub\_client\_sample\_amqp** というアプリケーションが格納されています。

**iothub\_client\_sample\_ampq** アプリケーションの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

このソリューションには単一のプロジェクトが含まれています。 このソリューションには&4; つの NuGet パッケージがインストールされていることに注意してください。

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.AmqpTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.uamqp

SDK を使った作業には必ず **Microsoft.Azure.C.SharedUtility** パッケージが必要となります。 このサンプルは AMQP に依存しているため、**Microsoft.Azure.uamqp** パッケージと **Microsoft.Azure.IoTHub.AmqpTransport **パッケージを含める必要があります (HTTP と MQTT にも同等のパッケージが存在します)。 サンプルでは **IoTHubClient** ライブラリが使用されているため、ソリューション内に **Microsoft.Azure.IoTHub.IoTHubClient** パッケージも含める必要があります。

サンプル アプリケーションの実装は、**iothub\_client\_sample\_amqp.c** ソース ファイル内にあります。

このサンプル アプリケーションを使用して、 **IoTHubClient** ライブラリの使用に必要な内容を説明します。

### <a name="initializing-the-library"></a>ライブラリの初期化
> [!NOTE]
> ライブラリの使用を開始する前に、プラットフォーム固有の初期化の実行が必要になる場合があります。 たとえば、Linux 上で AMQP を使用する計画がある場合は、OpenSSL ライブラリを初期化する必要があります。 [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)のサンプルはクライアントの起動時にユーティリティ関数 **platform_init** を呼び出し、終了する前に **platform_deinit** 関数を呼び出します。 これらの関数は、platform.h ヘッダー ファイルで宣言されます。 ターゲットとするプラットフォームについて [リポジトリ](https://github.com/Azure/azure-iot-sdk-c) でこれらの関数の定義を確認し、クライアントにいずれかのプラットフォーム初期化コードを含める必要があるかどうかを判断する必要があります。
> 
> 

ライブラリで作業を開始するには、最初に、IoT Hub クライアントのハンドルを割り当てる必要があります。

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

この関数 (デバイス エクスプローラー ツールから取得したもの) にデバイスの接続文字列のコピーを渡していることに注意してください。 また、使用するプロトコルを指定します。 この例では AMQP を使用しますが、MQTT と HTTP も使用できます。

有効な **IOTHUB\_CLIENT\_HANDLE** がある場合、最初にその API を呼び出して、IoT Hub とのイベントの送信とメッセージの受信を実行することができます。 次で確認します。

### <a name="sending-events"></a>イベントの送信
IoT Hub にイベントを送信するには、次の手順を実行する必要があります。

まず、次のようにメッセージを作成します。

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

次に、次のようにメッセージを送信します。

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

メッセージを送信するたびに、データが送信されるときに呼び出されるコールバック関数への参照を指定します。

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

メッセージの処理が終了したときに呼び出される **IoTHubMessage\_Destroy** 関数に注意してください。 この呼び出しを行って、メッセージを作成したときに割り当てられたリソースを解放する必要があります。

### <a name="receiving-messages"></a>メッセージの受信
メッセージの受信は非同期操作です。 最初に、デバイスがメッセージを受信したときに呼び出されるコールバックを登録します。

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

最後のパラメーターは任意のものを指す void ポインターです。 このサンプルのポインターは整数を指していますが、より複雑なデータ構造を指すこともできます。 これにより、このコールバック関数を、この関数の呼び出し元と共有した状態で操作できます。

デバイスがメッセージを受信すると、登録済みのコールバック関数が呼び出されます。

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

**IoTHubMessage\_GetByteArray** 関数を使用してメッセージを取得する点に注意してください。この例では文字列です。

### <a name="uninitializing-the-library"></a>ライブラリの初期化解除
イベントの送信とメッセージの受信を完了すると、次の方法で IoT ライブラリの初期化を解除することができます。 これを行うには、次の関数呼び出しを発行します。

```
IoTHubClient_Destroy(iotHubClientHandle);
```

これにより、以前に割り当てられていたリソースが、**IoTHubClient\_CreateFromConnectionString** 関数によって解放されます。

ご覧のように、 **IoTHubClient** ライブラリを使用すると、簡単にイベントの送信とメッセージの受信を実行できます。 使用するプロトコルなど (開発者の観点からは単純な構成オプションです)、IoT Hub との通信に関する詳細情報は、ライブラリで処理されます。

**IoTHubClient** ライブラリでは、デバイスから IoT Hub に送信されるイベントをシリアル化する方法を正確に制御することもできます。 これが利点になる場合もありますが、この実装の詳細が関心の対象にならない場合もあります。 その場合は、次のセクションで説明する **serializer** ライブラリの使用を検討することができます。

## <a name="serializer"></a>serializer
概念的には、**serializer** ライブラリは、SDK の **IoTHubClient** ライブラリの上位に位置します。 IoT Hub との基礎になる通信で **IoTHubClient** ライブラリを使用しますが、モデリング機能が追加されるため、開発者がメッセージのシリアル化を処理する負担が削減されます。 このライブラリの動作のわかりやすい例を示します。

[azure-iot-sdk-c リポジトリ](https://github.com/Azure/azure-iot-sdk-c)内の **serializer** フォルダー内には **samples** フォルダーがあり、**simplesample\_amqp** というアプリケーションが格納されています。 このサンプルの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

前のサンプルと同様、このソリューションにも、いくつかの NuGet パッケージが含まれています。

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.AmqpTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.uamqp

ほとんどのパッケージは前のサンプルにも含まれていましたが、 **Microsoft.Azure.IoTHub.Serializer** を使うのはこれが初めてです。 これは、 **serializer** ライブラリを使用するときに必要です。

サンプル アプリケーションの実装は **simplesample\_amqp.c** ファイル内にあります。

次のセクションで、このサンプルの主要な部分について説明します。

### <a name="initializing-the-library"></a>ライブラリの初期化
**serializer** ライブラリの使用を開始するには、初期化 API を呼び出す必要があります。

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

**serializer\_init** 関数の呼び出しは、基になるライブラリを初期化するために&1; 回だけ行われます。 次に、**IoTHubClient\_CreateFromConnectionString** 関数を呼び出します。これは、**IoTHubClient** サンプルに含まれる API と同じものです。 この呼び出しにより、デバイスに接続文字列が設定されます (使用するプロトコルを選択する場所でもあります)。 このサンプルでは、トランスポートとして AMQP を使用していますが、HTTP を使用することもできます。

最後に、**CREATE\_MODEL\_INSTANCE** 関数を呼び出します。 **WeatherStation** はモデルの名前空間で、**ContosoAnemometer** はモデルの名前です。 モデルのインスタンスを作成したら、そのモデルを使用して、イベントの送信とメッセージの受信を開始することができます。 ただし、モデルがどのようなものかを理解する必要があります。

### <a name="defining-the-model"></a>モデルの定義
**serializer** ライブラリ内のモデルは、デバイスから IoT Hub に送信できるイベントと、モデリング言語で *アクション* と呼ばれる、デバイスが受信できるメッセージを定義します。 **simplesample\_amqp** サンプル アプリケーションと同様に、一連の C マクロを使用してモデルを定義します。

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

**BEGIN\_NAMESPACE** と **END\_NAMESPACE** の両方のマクロは、モデルの名前空間を引数として取得します。 この&2; つのマクロの間には、モデルの定義とモデルが使用するデータ構造があることが想定されます。

この例では、 **ContosoAnemometer**という単一のモデルがあります。 このモデルは、デバイスが IoT Hub に送信できる、**DeviceId** と **WindSpeed** の&2; つのイベントを定義します。 さらに、デバイスが受信できる、**TurnFanOn**、**TurnFanOff**、**SetAirResistance** の&3; つのアクション (メッセージ) を定義します。 各イベントには型があり、各アクションには名前 (また、必要に応じて一連のパラメーター) があります。

モデルで定義されたイベントとアクションは、IoT Hub へのイベントの送信とデバイスに送信されるメッセージへの応答に使用できる API へのアクセスを定義します。 これは例を使用すると理解しやすくなります。

### <a name="sending-events"></a>イベントの送信
このモデルは、IoT Hub に送信できるイベントを定義します。 この例では、**WITH_DATA** マクロを使用して定義した&2; つのイベントのうちいずれかを意味します。 たとえば、 **WindSpeed** イベントを IoT Hub に送信する場合は、そのためにいくつかの手順を実行する必要があります。 最初に、送信するデータを設定します。

```
myWeather->WindSpeed = 15;
```

先ほど定義したモデルを使うと、 **構造体**のメンバーを設定することで送信するデータを設定できます。 次に、送信するイベントをシリアル化します。

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

このコードは、イベントをシリアル化して、( **送信先**によって参照される) バッファーに格納します。 最後に、次のコードで、イベントを IoT Hub に送信します。

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

これは、シリアル化されたイベントを IoT Hub に送信するサンプル アプリケーションのヘルパー関数です。

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

このコードは、**iothub\_client\_sample\_amqp** アプリケーションでバイト配列からメッセージを作成し、**IoTHubClient\_SendEventAsync** を使用して IoT Hub に送信したときのコードによく似ています。 その後で必要なのは、以前に割り当てたメッセージ ハンドルとシリアル化されたデータ バッファーの解放のみです。

**IoTHubClient\_SendEventAsync** の最後から&2; 番目のパラメーターは、データが正常に送信されたときに呼び出されるコールバック関数の参照です。 コールバック関数の例を次に示します。

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

2 番目のパラメーターは、ユーザー コンテキストを指すポインターであり、**IoTHubClient\_SendEventAsync** に渡したポインターと同じものです。 ここでは、このコンテキストは単純なカウンターですが、任意の内容にすることができます。

イベントの送信に必要なものは以上です。 残りは、メッセージを受信する方法の説明のみです。

### <a name="receiving-messages"></a>メッセージの受信
メッセージの受信は、 **IoTHubClient** ライブラリでメッセージが動作するしくみと同様です。 まず、メッセージのコールバック関数を登録します。

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

このコードは定型句であるため、すべてのソリューションで同じになります。 この関数は、メッセージを受信し、**EXECUTE\_COMMAND** を呼び出して、適切な関数へのルーティングを処理します。 この時点で呼び出す関数は、モデルのアクションの定義によって異なります。

モデルのアクションを定義するとき、対応するメッセージをデバイスが受信するときに呼び出される関数を実装する必要があります。 たとえば、モデルで次のアクションが定義されているとします。

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

関数の名前はモデル内のアクションの名前に一致し、関数のパラメーターはアクションに対して指定されたパラメーターに一致することに注意してください。 最初のパラメーターは常に必要であり、モデルのインスタンスへのポインターが含まれます。

デバイスがこのシグネチャと一致するメッセージを受信すると、対応する関数が呼び出されます。 したがって、 **IoTHubMessage**の定型コードを含める必要があることを除けば、メッセージの受信は、モデルで定義されている各アクションに単純な関数を定義する処理のみで実行できます。

### <a name="uninitializing-the-library"></a>ライブラリの初期化解除
データの送信とメッセージの受信を完了すると、次の方法で IoT ライブラリの初期化を解除することができます。

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

この&3; つの各関数は、前に説明した&3; つの初期化関数に対応しています。 これらの API を呼び出すと、以前に割り当てたリソースが確実に解放されます。

## <a name="next-steps"></a>次のステップ
この記事では、**C 用 Azure IoT device SDK** のライブラリの使用方法の基本を説明しました。SDK の内容、そのアーキテクチャ、Windows サンプルの実行を開始する方法を理解するうえで十分な情報を提供しました。 次の記事では、 [IoTHubClient ライブラリに関する詳細](iot-hub-device-sdk-c-iothubclient.md)を説明することで、引き続き SDK について記述します。

IoT Hub の開発に関する詳細については、「[Azure IoT SDKs (Azure IoT SDK)][lnk-sdks]」を参照してください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Feb17_HO2-->


