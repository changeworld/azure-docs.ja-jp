<properties
 pageTitle="IoT Hub セキュリティ トークンの生成方法 | Microsoft Azure"
 description="IoT Hub で使用されるさまざまな種類のセキュリティ トークンとその生成方法について説明します。"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="04/29/2016"
 ms.author="elioda"/>

# IoT Hub セキュリティ トークンの使用

IoT Hub では、デバイスとサービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。さらに、セキュリティ トークンには、有効期間とスコープの制限があります。[Azure IoT Hub SDK][lnk-apis-sdks] を使用すると、自動的にトークンを生成できます。その際、特別な構成は必要ありません。ただし、一部のシナリオでは、ユーザーがセキュリティ トークンを直接生成して使用する必要があります。これには、AMQP、MQTT、HTTP サーフェスの直接的な使用や、「[ソリューションの設計][lnk-guidance-security]」で説明されているようにトークン サービス パターンの実装が含まれます。

この記事では、次の内容について説明します。

* セキュリティ トークンの形式と生成方法。
* セキュリティ トークンを使用してデバイスとバックエンド サービスの両方を認証する主な使用例。

## セキュリティ トークンの構造
セキュリティ トークンは、IoT Hub の特定の機能へのアクセスを期限付きでデバイスとサービスに許可するために使用します。承認済みのデバイスとサービスのみが接続できるようにするには、共有アクセス ポリシー キーか、ID レジストリにデバイス ID と共に格納されている対称キーでセキュリティ トークンに署名する必要があります。

共有アクセス ポリシー キーで署名されたトークンは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスを許可します。IoT Hub 開発者ガイドの「[セキュリティ][lnk-devguide-security]」セクションを参照してください。一方、デバイス ID の対称キーで署名されたトークンは、関連付けられたデバイス ID の **DeviceConnect** アクセス許可のみを付与します。

セキュリティ トークンには、次の形式があります。

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

考えられる値を次に示します。

| 値 | 説明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。**重要**: キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| {resourceURI} | IoT Hub のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。たとえば、`myHub.azure-devices.net/devices/device1` のように指定します。 |
| {expiry} | 1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} | 小文字のリソース URI の小文字の URL エンコード |
| {policyName} | このトークンの参照先となる共有アクセス ポリシーの名前。デバイスとレジストリの資格情報を参照するトークンの場合は存在しません。 |

**プレフィックスに関する注意事項**: URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

これは、`resourceUri, signingKey, policyName, expiresInMins` の各入力からトークンを計算するノード関数です。以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

> [AZURE.NOTE] トークンの有効期間は IoT Hub コンピューターで検証されるため、重要なのは、トークンを生成するコンピューターのクロックのずれが最小限であることです。

## デバイスとして SAS トークンを使用する

セキュリティ トークンを使用して IoT Hub で **DeviceConnect** アクセス許可を取得するには、デバイス ID キーを使用する方法と共有アクセス ポリシー キーを使用する方法の 2 とおりがあります。

さらに、デバイスからアクセスできるすべての機能は、仕様により、`/devices/{deviceId}` というプレフィックスを持つエンドポイントで公開されることに注意することが重要です。

> [AZURE.IMPORTANT] IoT Hub が特定のデバイスを認証する唯一の方法では、デバイス ID 対称キーを使用します。デバイスの機能へのアクセスに共有アクセス ポリシーを使用する場合、ソリューションでは、セキュリティ トークンを発行するコンポーネントを、信頼できるサブコンポーネントと見なす必要があります。

デバイス向けのエンドポイントを次に示します (プロトコルは関係ありません)。

| エンドポイント | 機能 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | デバイスからクラウドへのメッセージを送信します。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` | クラウドからデバイスへのメッセージを受信します。 |

### ID レジストリの対称キーを使用する

デバイス ID の対称キーを使用してトークンを生成すると、トークンの policyName (`skn`) 要素は省略されます。

たとえば、すべてのデバイス機能にアクセスするために作成されるトークンには、次のパラメーターが必要です。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `{device id}` ID の任意の対称キー
* ポリシー名なし
* 任意の有効期間

上のノード関数を使用した例を次に示します。

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] .NET ツールの [Device Explorer][lnk-device-explorer] を使用すると、セキュリティ トークンを生成することができます。

### 共有アクセス ポリシーを使用する

共有アクセス ポリシーからトークンを作成する際は、ポリシー名フィールド `skn` に、使用するポリシーの名前を設定する必要があります。また、ポリシーで **DeviceConnect** アクセス許可を付与することも必要です。

共有アクセス ポリシーを使用してデバイスの機能にアクセスするための 2 つの主なシナリオは次のとおりです。

* [クラウド プロトコル ゲートウェイ][lnk-azure-protocol-gateway]
* [トークン サービス][lnk-devguide-security] (カスタム認証スキームの実装に使用)

共有アクセス ポリシーは任意のデバイスとして接続するためのアクセス権を付与する可能性があるため、セキュリティ トークンの作成時に適切なリソース URI を使用することが重要です。これは、トークン サービスに特に重要です。このサービスでは、リソース URI を使用して、トークンのスコープを特定のデバイスに限定する必要があります。これは、プロトコル ゲートウェイにはあまり関連がありません。このようなゲートウェイは、すべてのデバイスのトラフィックを既に仲介しているためです。

たとえば、**device** という事前作成済みの共有アクセス ポリシーを使用するトークン サービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `device` ポリシーのいずれかのキー
* ポリシー名: `device`
* 任意の有効期間

上のノード関数を使用した例を次に示します。

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

リソース URI を `myhub.azure-devices.net/devices` に設定するだけで、プロトコル ゲートウェイはすべてのデバイスに同じトークンを使用できます。

## サービス コンポーネントからセキュリティ トークンを使用する

サービス コンポーネントでは、IoT Hub 開発者ガイドの「[セキュリティ][lnk-devguide-security]」セクションで説明されているように、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます。

エンドポイントで公開されるサービス機能を次に示します。

| エンドポイント | 機能 |
| ----- | ----------- |
| `{iot hub host name}/devices` | デバイス ID を作成、更新、取得、削除します。 |
| `{iot hub host name}/messages/events` | デバイスからクラウドへのメッセージを受信します。 |
| `{iot hub host name}/servicebound/feedback` | クラウドからデバイスへのメッセージのフィードバックを受信します。 |
| `{iot hub host name}/devicebound` | クラウドからデバイスへのメッセージを送信します。 |

たとえば、**registryRead** という事前作成済みの共有アクセス ポリシーを使用してトークンを生成するサービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices`
* 署名キー: `registryRead` ポリシーのいずれかのキー
* ポリシー名: `registryRead`
* 任意の有効期間

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果は次のようになります (すべてのデバイス ID の読み取りアクセスが許可されます)。

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead


[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

<!---HONumber=AcomDC_0504_2016-->