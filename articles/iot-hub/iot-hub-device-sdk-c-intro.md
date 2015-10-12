<properties
	pageTitle="C 用 Azure IoT デバイス SDK の概要"
	description="C 用 Azure IoT デバイス SDK のサンプル コードについて説明し、操作を開始します。"
	services="iot-hub"
	documentationCenter="na"
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# C 用 Azure IoT デバイス SDK の概要

**Azure IoT デバイス SDK** は、**Azure IoT Hub** サービスとのイベントの送信とメッセージの受信のプロセスを簡略化するために設計された一連のライブラリです。SDK にはさまざまなバリエーションがあり、それぞれが特定のプラットフォームを対象としていますが、この記事では、**C 用 Azure IoT デバイス SDK** の紹介に焦点を絞っています。

C 用 Azure IoT デバイス SDK は、移植性を最大限まで高めるために ANSI C (C99) で記述されています。これにより、さまざまなプラットフォームとデバイス、特にディスクとメモリの量を最小限に抑えることが優先される環境で操作する場合に適しています。

SDK がテストされたプラットフォームは幅広くあります ([SDK ドキュメント](https://github.com/Azure/azure-iot-sdks/tree/master/c)を参照してください)。この記事には、*Windows* プラットフォームで実行されるサンプル コードのチュートリアルが含まれますが、以下のコードはサポートされているプラットフォーム全体でまったく同じです。

この記事では、C 用 Azure IoT デバイス SDK のアーキテクチャについて紹介します。また、デバイスのライブラリを初期化する方法、IoT Hub にイベントを送信する方法や IoT Hub からメッセージを受信する方法の例を示します。この記事の情報は、SDK を使用し始めるにあたり十分な内容ですが、ライブラリに関する追加情報を入手できる場所も紹介します。


## SDK のアーキテクチャ

次の GitHub リポジトリ内で **C 用 Azure IoT デバイス SDK** を見つけることができます。

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

最新バージョンのライブラリは、このリポジトリの **master** ブランチにあります。

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

このリポジトリには、Azure IoT デバイス SDK のファミリ全体が含まれています。ただし、この記事では、**c** フォルダーにある *C 用* Azure IoT デバイス SDK を取り扱います。

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

SDK の中心となる実装は、リポジトリ内の **common**、**iothub\_client**、**serializer** の各フォルダーにあります。**common** フォルダーには、ライブラリ全体で使用される共有コードが格納されています (通常、**common** フォルダーのコードは直接使用しないでください)。一方、**iothub\_client** フォルダーと **serializer** フォルダーには、ユーザーのコードで使用する SDK の 2 つの独立した層の実装が格納されています。

-   **IoTHubClient**: **iothub\_client** フォルダーには、ライブラリ内の最下位の API 層の実装が格納されています。この層には、IoT Hub にイベントを送信するだけでなく IoT Hub からメッセージを受信する API が含まれます。この層を使用する場合、メッセージのシリアル化はユーザー自身が実装する必要がありますが、IoT Hub と通信するためのその他の詳細は自動で処理されます。

-   **serializer**: **serializer** フォルダーには、**IoTHubClient** 上に構築するライブラリの実装が格納されています。具体的には、**serializer** ライブラリは、**IoTHubClient** が提供する機能の上にモデリング機能を追加します。**serializer** ライブラリを使用する場合は、まず、IoT Hub から受信するメッセージだけでなく IoT Hub に送信するイベントを指定するモデルを定義します。いったんモデルを定義すれば、SDK が提供する API にアクセスして、シリアル化の詳細を気にすることなく、イベントやメッセージを簡単に処理できます。

これらはすべて、サンプル コードを確認した方が簡単に理解できます。次のセクションでは、SDK に含まれているいくつかのサンプル アプリケーションについて説明します。API の動作のしくみの概要だけでなく、SDK のアーキテクチャの層のさまざまな機能を理解するうえで役立つ内容です。

## サンプルを実行する前に

C 用 Azure IoT デバイス SDK でサンプルを実行する前に、開発環境の準備とデバイスの資格情報の取得の 2 つのタスクを完了する必要があります。SDK に含まれる [readme ファイル](https://github.com/Azure/azure-iot-sdks/tree/master/c)に、両方のタスクの手順が説明されています。ただし、次のセクションで、これらの手順について追加でコメントしています。

### 開発環境の準備

最初に、GitHub から SDK のコピーを入手して、ソースを作成する必要があります。GitHub リポジトリの **master** ブランチからソースのコピーを取得します。

<https://github.com/Azure/azure-iot-sdks>

ソースのコピーをダウンロードしたら、コードを実行する前にいくつかの手順を完了する必要があります。「[Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md)」というタイトルの SDK の記事にこれらの手順の概要が記載されています。Windows 用に実行する必要がある作業のほとんどが、[QPID Proton ライブラリの構築](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#rebuilding-the-qpid-proton-library-in-windows)に関係しています。ここでは、準備ガイドで説明されている手順を完了するのに役立ついくつかのヒントを紹介します。

-   **PROTON\_PATH** 環境変数を作成する場合は、次に示すように、**システム**環境変数にします。

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   **CMake** ユーティリティをインストールするとき、**すべてのユーザー**の PATH システム環境変数に **CMake** を追加するオプションを選択します (**現在のユーザー**に追加しても効果は同じです)。

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   必ず、適切なバージョンの Python をインストールしてください。Windows では通常、x86 バージョンをインストールします。

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

インストーラーの既定のオプションを使用できます。

-   **Developer Command Prompt for VS2015** を開く前に、*Git コマンド ライン ツール*をインストールします。これらのツールをインストールするには、次の手順を実行します。

	1. **Visual Studio 2015** セットアップ プログラムを起動します (または **[プログラムと機能]** コントロール パネルの **[Microsoft Visual Studio 2015]** を選択して **[変更]** をクリックします)。
	2. **[Git for Windows]** 機能がインストーラーで選択されていることを確認します。![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. セットアップ ウィザードを実行してツールをインストールします。
	4. Git ツールの **bin** ディレクトリを **PATH** システム環境変数に追加します。Windows では、次のような画面になります。![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

**build\_proton.cmd** スクリプトをコマンド プロンプトから実行すると、このスクリプトが GitHub リポジトリから Proton ソースを取得し、Proton ライブラリを作成します。

前の手順を完了したら、対応する Visual Studio のソリューション ファイルを開き、**[ビルド]** メニューの **[ソリューションのビルド]** を選択して、AMQP に依拠している Windows サンプルをビルドできます。

### デバイスの資格情報の取得

これで開発環境をセットアップできたので、最後にデバイスの資格情報のセットを取得します。デバイスを IoT Hub にアクセスできるようにするには、まず、そのデバイスを IoT Hub のデバイスのレジストリに追加する必要があります。デバイスを追加すると、そのデバイスを IoT Hub に接続するために必要な、デバイスの資格情報のセットが得られます。次のセクションで確認するサンプル アプリケーションでは、これらの資格情報は**デバイスの接続文字列**の形式であると想定されています。

[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) ツール (Azure IoT デバイス SDK を含む) は、Azure IoT サービス ライブラリを使用して、デバイスの追加など、IoT Hub のさまざまな機能を実行します。デバイス エクスプローラーを使用してデバイスを追加すると、対応する接続文字列が表示されます。この接続文字列は、サンプル アプリケーションを実行するために必要なものです。

このプロセスにまだ詳しくない方のために、次の手順で、デバイス エクスプローラーを使用してデバイスを追加し、デバイスの接続文字列を取得する方法について説明します。

**Visual Studio 2015** の **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** を開き、ソリューションをビルドします。プログラムを実行すると、次のインターフェイスが表示されます。

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

**IoT Hub の接続文字列**を最初のフィールドに入力し、**[更新]** をクリックします。これにより、IoT Hub と通信できるようにツールが構成されます。

IoT Hub の接続文字列を構成したら、**[管理]** タブをクリックします。

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

ここで、IoT Hub に登録されたデバイスを管理します。

**[作成]** をクリックしてデバイスを作成することができます。ダイアログ ボックスに、一連のキー (プライマリおよびセカンダリ) が入力された状態で表示されます。ここで必要なのは、**[デバイス ID]** を入力して、**[作成]** をクリックするだけです。

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

デバイスを作成したら、今作成したデバイスを含めたすべての登録済みデバイスで、デバイスの一覧が更新されます。新しいデバイスを右クリックすると、次のメニューが表示されます。

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

**[選択したデバイスの接続文字列のコピー]** オプションを選択すると、デバイスの接続文字列がクリップボードにコピーされます。このコピーを残しておきます。後で説明するサンプル アプリケーションを実行するときにこれが必要になります。

前の手順が完了すると、コードを実行する準備が整います。次のセクションでは、C 用 Azure デバイス SDK に含まれるサンプル ソリューションの中の 2 つを使用して、ライブラリの機能を説明します。以下に示す 2 つのサンプルは、メインのソース ファイルの上部に、接続文字列を入力するための定数が使用されています。たとえば、**iothub\_client\_sample\_amqp** アプリケーションの対応する行は次のとおりです。

```
static const char* connectionString = "[device connection string]";
```

以下の説明に従って作業する場合は、ここにご使用のデバイスの接続文字列を入力し、ソリューションを再コンパイルすると、サンプルを実行することができます。

## IoTHubClient

azure-iot-sdks リポジトリ内の **iothub\_client** フォルダーに **samples** フォルダーがあり、**iothub\_client\_sample\_amqp** というアプリケーションが格納されています。

**iothub\_client\_sample\_ampq** アプリケーションの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

**iothub\_client\_sample\_amqp** プロジェクトには、4 つのプロジェクトが含まれます。サンプル コードは、**iothub\_client\_sample\_amqp プロジェクト**にあります。ただし、このソリューションには、C 用 Azure IoT デバイス SDK の **common**、**iothub\_amqp\_transport**、**iothub\_client** プロジェクトも含まれます。SDK を使用するときは常に **common** プロジェクトが必要になります。このサンプルは AMQP に依拠しているため、**iothub\_amqp\_transport** プロジェクトを含める必要があります (HTTP 向けにも同等のプロジェクトがあります)。また、サンプルでは **IoTHubClient** ライブラリが使用されているため、ソリューション内に **iothub\_client** プロジェクトも含める必要があります。

**iothub\_client\_sample\_amqp.c** ソース ファイルでサンプル アプリケーションの実装を見つけることができます。

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

このサンプル アプリケーションを使用して、**IoTHubClient** ライブラリの使用に必要な内容を説明します。


### ライブラリの初期化

ライブラリで作業を開始するには、最初に、IoT Hub クライアントのハンドルを作成する必要があります。

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

この関数 (デバイス エクスプローラーから取得したもの) にデバイスの接続文字列のコピーを渡していることに注意してください。使用するプロトコルも指定します。この例では AMQP を使用していますが、HTTP を選ぶこともできます。

有効な **IOTHUB\_CLIENT\_HANDLE** がある場合、最初にその API を呼び出して、IoT Hub とのイベントの送信とメッセージの受信を実行することができます。次で確認します。

### イベントの送信

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

メッセージの処理が終了したときに **IoTHubMessage\_Destroy** 関数を呼び出しています。これは、メッセージを作成したときに割り当てられたリソースを解放するために必要な手順です。

### メッセージの受信

メッセージの受信は非同期操作です。最初に、デバイスがメッセージを受信したときに呼び出されるコールバックを登録します。

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

最後のパラメーターは任意のものを指す void ポインターです。このサンプルのポインターは整数を指していますが、より複雑なデータ構造を指すこともできます。これにより、このコールバック関数を、この関数の呼び出し元と共有した状態で操作できます。

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

**IoTHubMessage\_GetByteArray** 関数を使用してメッセージを取得する方法に注意してください。この例では文字列です。

### ライブラリの初期化解除

イベントの送信とメッセージの受信が完了すると、IoT ライブラリの初期化を解除することができます。これは、次の関数呼び出しによって実行されます。

```
IoTHubClient_Destroy(iotHubClientHandle);
```

これにより、以前に割り当てられていたリソースが、**IoTHubClient\_CreateFromConnectionString** 関数によって解放されます。

ご覧のように、**IoTHubClient** ライブラリを使用すると、簡単にイベントの送信とメッセージの受信を実行できます。使用するプロトコルなど (開発者の観点からは単純な構成オプションです)、IoT Hub との通信に関する詳細情報は、ライブラリで処理されます。

**IoTHubClient** ライブラリでは、デバイスから IoT Hub に送信されるイベントをシリアル化する方法を正確に制御することもできます。これが利点になる場合もありますが、この実装の詳細が関心の対象にならない場合もあります。その場合は、次に説明する **serializer** ライブラリの使用を検討することができます。

## シリアライザー

概念的には、**serializer** ライブラリは、SDK の **IoTHubClient** ライブラリの上位に位置します。IoT Hub との基礎になる通信で **IoTHubClient** ライブラリを使用しますが、モデリング機能が追加されるため、開発者がメッセージのシリアル化を処理する負担が削減されます。このライブラリの動作のわかりやすい例を示します。

azure-iot-sdks リポジトリ内の **serializer** フォルダー内には **samples** フォルダーがあり、**simplesample\_amqp** というアプリケーションが格納されています。このサンプルの Windows バージョンには、次の Visual Studio のソリューションが含まれます。

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

サンプル自体は、**simplesample\_amqp** プロジェクトです。これまで見てきたように、このソリューションには C 用 Azure IoT デバイス SDK の **common**、**iothub\_amqp\_transport**、**iothub\_client** の各プロジェクトが含まれます。ただし、このサンプル ソリューションには、**serializer** という追加のプロジェクトも含まれます。これは、**IoTHubClient** ライブラリの上にモデリングのサポートを追加するライブラリです。

**simplesample\_amqp.c** コード ファイルでサンプル アプリケーションの実装を見つけることができます。

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

次のセクションで、このサンプルの主要な部分について説明します。

### ライブラリの初期化

ライブラリの使用を開始するには、初期化 API を呼び出す必要があります。

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

**serializer\_init** 関数の呼び出しは、基になるライブラリを初期化するために 1 回限り行われます。次に、**IoTHubClient\_CreateFromConnectionString** 関数を呼び出します。これは、**IoTHubClient** サンプルと同じ API です。この呼び出しにより、デバイスに接続文字列が設定されます (使用するプロトコルを選択する場所でもあります)。このサンプルでは、トランスポートとして AMQP を使用していますが、HTTP を使用することもできます。

最後に、**CREATE\_MODEL\_INSTANCE** 関数を呼び出します。**WeatherStation** はモデルの名前空間で、**ContosoAnemometer** はモデルの名前です。モデルを定義したら、定義したモデルを使用して、イベントの送信とメッセージの受信を開始することができます。ただし最初に、モデルがどのようなものかを理解する必要があります。

### モデルの定義

**serializer** ライブラリ内のモデルは、デバイスから IoT Hub に送信できるイベントと、モデリング言語で*アクション*と呼ばれる、デバイスが受信できるメッセージを定義します。**simplesample\_amqp** サンプル アプリケーションと同様に、一連の C マクロを使用してモデルを定義します。

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

**BEGIN\_NAMESPACE** と **END\_NAMESPACE** の両方のマクロは、モデルの名前空間を引数として取得します。この 2 つのマクロの間には、モデルの定義とモデルが使用するデータ構造があることが想定されます。

この例では、**ContosoAnemometer** という単一のモデルがあります。このモデルは、デバイスが IoT Hub に送信できる、**DeviceId** と **WindSpeed** の 2 つのイベントを定義します。さらに、デバイスが受信できる、**TurnFanOn**、**TurnFanOff**、**SetAirResistance** の 3 つのアクション (メッセージ) を定義します。各イベントには型があり、各アクションには名前 (また、必要に応じて一連のパラメーター) があります。

モデルで定義されたイベントとアクションは、IoT Hub へのイベントの送信とデバイスに送信されるメッセージへの応答に使用できる API へのアクセスを定義します。これは例を使用すると理解しやすくなります。

### イベントの送信

このモデルは、IoT Hub に送信できるイベントを定義します。このサンプルでは、**WITH\_DATA** マクロを使用して定義した 2 つのイベントのうちの 1 つを使用することを意味します。たとえば、**WindSpeed** イベントを IoT Hub に送信する場合は、そのためにいくつかの手順を実行する必要があります。最初に、送信するデータを設定します。

```
myWeather->WindSpeed = 15;
```

先ほど定義したモデルを使うと、構造体のメンバーを設定するだけで送信するデータを設定することができます。次に、送信するイベントをシリアル化する必要があります。

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

このコードは、イベントをシリアル化して、(**送信先**によって参照される) バッファーに格納します。最後に、次のコードで、イベントを IoT Hub に送信します。

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

このコードは、**iothub\_client\_sample\_amqp** アプリケーションで見た内容とよく似ています。そこでは、バイト配列からメッセージを作成し、**IoTHubClient\_SendEventAsync** を使用して IoT Hub に送信しました。その後で必要なのは、以前に割り当てたメッセージ ハンドルとシリアル化されたデータ バッファーの解放のみです。

**IoTHubClient\_SendEventAsync** の最後から 2 番目のパラメーターは、データが正常に送信されたときに呼び出されるコールバック関数の参照です。コールバック関数の例を次に示します。

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

2 番目のパラメーターは、ユーザーのコンテキストを指すポインターです。これは **IoTHubClient\_SendEventAsync** に渡したのと同じポインターです。この場合、このコンテキストは単純なカウンターですが、任意の内容にすることができます。

イベントの送信に必要なものは以上です。残りは、メッセージを受信する方法の説明のみです。

### メッセージの受信

メッセージの受信は、**IoTHubClient** ライブラリでメッセージが動作するしくみと同様です。まず、メッセージのコールバック関数を登録します。

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

このコードは定型句であるため、すべてのソリューションで同じになります。この関数は、メッセージを受信し、**EXECUTE\_COMMAND** を呼び出して、適切な関数へのルーティングを処理します。基本的に、この時点でどのような関数を呼び出すかは、モデルのアクションの定義によって異なります。

モデルのアクションを定義するとき、デバイスがメッセージを受信するときに呼び出される対応する関数を実装する必要があります。たとえば、モデルで次のアクションが定義されているとします。

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

関数の名前はモデル内のアクションの名前に一致し、関数のパラメーターはアクションに対して指定されたパラメーターに一致することに注意してください。最初のパラメーターは常に必要であり、モデルのインスタンスへのポインターが含まれます。

デバイスがこのシグネチャと一致するメッセージを受信すると、対応する関数が呼び出されます。**IoTHubMessage** の定型コードを使用する必要があることを除けば、メッセージの受信は、モデルで定義されている各アクションに単純な関数を定義する処理のみで実行できます。

### ライブラリの初期化解除

データの送信とメッセージの受信を完了すると、次の方法で IoT ライブラリの初期化を解除することができます。

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

この 3 つの各関数は、前に説明した 3 つの初期化関数に対応しています。これらの API を呼び出すと、以前に割り当てたリソースが確実に解放されます。

## 次のステップ

この記事では、**C 用 Azure IoT デバイス SDK** のライブラリの使用方法の基本を説明しています。SDK の内容、そのアーキテクチャ、Windows サンプルの実行を開始する方法を理解するうえで十分な情報を提供しています。次の記事では、[IoTHubClient ライブラリに関する詳細](iot-hub-device-sdk-c-iothubclient.md)を説明することで、引き続き SDK について記述します。

<!---HONumber=Oct15_HO1-->