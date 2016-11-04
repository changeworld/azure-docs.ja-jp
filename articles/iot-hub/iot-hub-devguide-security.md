---
title: 開発者ガイド - IoT Hub へのアクセス制御 | Microsoft Docs
description: Azure IoT Hub 開発者ガイド - IoT Hub へのアクセスを制御し、セキュリティを管理する方法
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett

---
# <a name="control-access-to-iot-hub"></a>IoT Hub へのアクセスの制御
## <a name="overview"></a>概要
この記事では、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。 IoT Hub では、*アクセス許可*を使用して、各 IoT Hub のエンドポイントへのアクセスを許可します。 次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

この記事では、次の内容について説明します。

* IoT Hub へのアクセス用にデバイスやバックエンド アプリに付与できるアクセス許可の種類。
* アクセス許可の確認に使用する認証プロセスとトークン。
* 資格情報のスコープを指定して特定のリソースへのアクセスを制限する方法。
* X.509 証明書の IoT Hub サポート。
* 既存のデバイス ID のレジストリまたは認証スキームを使用した、カスタムのデバイス認証メカニズム。

### <a name="when-to-use"></a>使用時の注意
IoT Hub のエンドポイントにアクセスするには、適切なアクセス許可が必要です。 たとえば、IoT Hub に送信するすべてのメッセージと共に、デバイスにはセキュリティ資格情報が格納されているトークンを含める必要があります。

## <a name="access-control-and-permissions"></a>アクセス制御とアクセス許可
次の方法で[アクセス許可](#iot-hub-permissions)を付与できます。

* **ハブレベルの共有アクセス ポリシー**。 共有アクセス ポリシーにより、[アクセス許可](#iot-hub-permissions)を自由に組み合わせて付与できます。 ポリシーは、[Azure Portal][lnk-management-portal]で定義するか、[Azure IoT Hub のリソースプロバイダー API][lnk-resource-provider-apis] を使用してプログラムにより定義できます。 新しく作成された IoT Hub には、次の既定のポリシーがあります。
  
  * **iothubowner**: すべてのアクセス許可を持つポリシー。
  * **service**: ServiceConnect アクセス許可を持つポリシー。
  * **device**: DeviceConnect アクセス許可を持つポリシー。
  * **registryRead**: RegistryRead アクセス許可を持つポリシー。
  * **registryReadWrite**: RegistryRead アクセス許可と RegistryWrite アクセス許可を持つポリシー。
* **デバイスごとのセキュリティ資格情報**。 各 IoT Hub には、[デバイス ID レジストリ][lnk-identity-registry]が含まれています。 このレジストリ内の各デバイスでは、対応するデバイスのエンドポイントを対象として **DeviceConnect** アクセス許可を付与する、セキュリティ資格情報を構成できます。

たとえば、標準的な IoT ソリューションの場合は次のようになります。

* デバイス管理コンポーネントでは *registryReadWrite* ポリシーを使用します。
* イベント プロセッサ コンポーネントでは *service* ポリシーを使用します。
* ランタイム デバイス ビジネス ロジック コンポーネントでは *service* ポリシーを使用します。
* 個々のデバイスは、IoT Hub の ID レジストリに格納されている資格情報を使用して接続します。

## <a name="authentication"></a>認証
Azure IoT Hub では、共有アクセス ポリシーとデバイス ID レジストリのセキュリティ資格情報に対してトークンを確認することにより、エンドポイントへのアクセスを許可します。

対称キーなどのセキュリティの資格情報がネットワーク上で送信されることはありません。

> [!NOTE]
> Azure IoT Hub のリソース プロバイダーは、[Azure Resource Manager][lnk-azure-resource-manager] のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。
> 
> 

セキュリティ トークンを作成して使用する方法の詳細については、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関するページをご覧ください。

### <a name="protocol-specifics"></a>プロトコルの詳細
MQTT、AMQP、および HTTP など、サポートされているプロトコルごとに、さまざまな方法でトークンが転送されます。

MQTT を使用する場合、CONNECT パケットでは、ClientId、ユーザー名フィールドの {iothubhostname}/{deviceId}、およびパスワード フィールドの SAS トークンとして、deviceId が使用されます。 {iothubhostname} は IoT Hub の完全な CName とする必要があります (contoso.azure-devices.net など)。

[AMQP][lnk-amqp] を使用する場合、[SASL PLAIN][lnk-sasl-plain] と [AMQP Claims-Based-Security][lnk-cbs] が IoT Hub でサポートされます。

AMQP Claims-Based-Security の場合、標準でこれらのトークンの送信方法が指定されます。

SASL PLAIN では、 **ユーザー名** を以下のように指定できます。

* `{policyName}@sas.root.{iothubName}` (ハブレベルのトークンを使用する場合)。
* `{deviceId}@sas.{iothubname}` (デバイスを対象とするトークンを使用する場合)。

どちらの場合も、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関する記事で説明されているように、パスワード フィールドにトークンが含まれています。

HTTP では、 **Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。

#### <a name="example"></a>例
ユーザー名 (DeviceId では大文字と小文字が区別されます): `iothubname.azure-devices.net/DeviceId`

パスワード (デバイス エクスプローラーで SAS を生成します): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT Hub の SDK][lnk-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。 場合によっては、SDK でサポートされないプロトコルや認証方法もあります。
> 
> 

### <a name="special-considerations-for-sasl-plain"></a>SASL PLAIN に関する特別な考慮事項
SASL PLAIN を AMQP で使用する場合、IoT Hub に接続するクライアントは、TCP 接続ごとにトークンを 1 つ使用できます。 トークンの有効期限が切れると、サービスから TCP 接続が切断され、再接続がトリガーされます。 この動作は、アプリケーション バックエンド コンポーネントの場合は問題ありませんが、デバイス側アプリケーションの場合は、次の理由から大きな損害を及ぼす可能性があります。

* ゲートウェイは、通常多くのデバイスの代理として接続しています。 SASL PLAIN を使用する場合、IoT Hub に接続するデバイスごとに、個別の TCP 接続を作成する必要があります。 このシナリオにより、電源とネットワーク リソースの消費量が大幅に増大し、各デバイスの接続の待機時間が増加します。
* 各トークンの有効期限が切れた後に、再接続に使用するリソースの量が増加すると、リソースが限られたデバイスには悪影響が出ます。

## <a name="scope-hublevel-credentials"></a>ハブレベルの資格情報のスコープ
制限付きのリソース URI を持つトークンを作成することにより、ハブレベルのセキュリティ ポリシーのスコープを指定できます。 たとえば、デバイスからの D2C メッセージを送信するエンドポイントは **/devices/{deviceId}/messages/events**になります。 また、**DeviceConnect** アクセス許可を持つハブレベルの共有アクセス ポリシーを使用して、resourceURI が **/devices/{deviceId}** であるトークンに署名することもできます。 この方法により、デバイスの **deviceId** の代わりにメッセージを送信するためにのみ使用できるトークンが作成されます。

これは [Event Hubs の発行元ポリシー][lnk-event-hubs-publisher-policy]に似たメカニズムであり、カスタムの認証方法の実装を可能にします。

## <a name="security-tokens"></a>セキュリティ トークン
IoT Hub では、デバイスとサービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 [Azure IoT Hub SDK][lnk-sdks] を使用すると、自動的にトークンを生成できます。その際、特別な構成は必要ありません。 ただし、一部のシナリオでは、ユーザーがセキュリティ トークンを直接生成して使用する必要があります。 これには、MQTT、AMQP、HTTP サーフェスの直接的な使用や、「[Custom device authentication][lnk-custom-auth] (カスタム デバイスの認証)」で説明されているようにトークン サービス パターンの実装が含まれます。

IoT Hub では、[X.509 証明書][lnk-x509]を使用して IoT Hub でデバイスの認証を行うことができます。 

### <a name="security-token-structure"></a>セキュリティ トークンの構造
セキュリティ トークンは、IoT Hub の特定の機能へのアクセスを期限付きでデバイスとサービスに許可するために使用します。 承認済みのデバイスとサービスのみが接続できるようにするには、共有アクセス ポリシー キーか、ID レジストリにデバイス ID と共に格納されている対称キーでセキュリティ トークンに署名する必要があります。

共有アクセス ポリシー キーで署名されたトークンは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスを許可します。 一方、デバイス ID の対称キーで署名されたトークンは、関連付けられたデバイス ID の **DeviceConnect** アクセス許可のみを付与します。

セキュリティ トークンには、次の形式があります。

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

考えられる値を次に示します。

| 値 | Description |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。 **重要**: キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| {resourceURI} |IoT Hub のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。 たとえば、 `myHub.azure-devices.net/devices/device1` |
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} |小文字のリソース URI の小文字の URL エンコード |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 デバイスとレジストリの資格情報を参照するトークンの場合は存在しません。 |

**プレフィックスに関する注意事項**: URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。 たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

以下は、`resourceUri, signingKey, policyName, expiresInMins`の各入力からトークンを計算する Node.js 関数です。 以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

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

 なお、上記に対応する Python のコードは次のとおりです。

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode

    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [!NOTE]
> トークンの有効期間は IoT Hub コンピューターで検証されるため、重要なのは、トークンを生成するコンピューターのクロックのずれが最小限であることです。
> 
> 

### <a name="use-sas-tokens-in-a-device-client"></a>デバイス クライアントで SAS トークンを使用する
セキュリティ トークンを使用して IoT Hub で **DeviceConnect** アクセス許可を取得するには、[デバイス ID レジストリからの対称デバイス キー](#use-a-symmetric-key-in-the-identity-registry)を使用する方法と、[共有アクセス ポリシー キー](#use-a-shared-access-policy)を使用する方法の 2 通りがあります。

デバイスからアクセスできるすべての機能は、仕様により、`/devices/{deviceId}`というプレフィックスを持つエンドポイントで公開されることにご注意ください。

> [!IMPORTANT]
> IoT Hub が特定のデバイスを認証する唯一の方法では、デバイス ID 対称キーを使用します。 デバイスの機能へのアクセスに共有アクセス ポリシーを使用する場合、ソリューションでは、セキュリティ トークンを発行するコンポーネントを、信頼できるサブコンポーネントと見なす必要があります。
> 
> 

デバイス向けのエンドポイントを次に示します (プロトコルは関係ありません)。

| エンドポイント | 機能 |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |デバイスからクラウドへのメッセージを送信します。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` |クラウドからデバイスへのメッセージを受信します。 |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>ID レジストリの対称キーを使用する
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

> [!NOTE]
> .NET ツールの [Device Explorer][lnk-device-explorer] を使用すると、セキュリティ トークンを生成できます。
> 
> 

### <a name="use-a-shared-access-policy"></a>共有アクセス ポリシーを使用する
共有アクセス ポリシーからトークンを作成する際は、ポリシー名フィールド `skn` に、使用するポリシーの名前を設定する必要があります。 また、ポリシーで **DeviceConnect** アクセス許可を付与することも必要です。

共有アクセス ポリシーを使用してデバイスの機能にアクセスするための 2 つの主なシナリオは次のとおりです。

* [クラウド プロトコル ゲートウェイ][lnk-endpoints]
* [トークン サービス][lnk-custom-auth] (カスタム認証スキームの実装に使用)。

共有アクセス ポリシーは任意のデバイスとして接続するためのアクセス権を付与する可能性があるため、セキュリティ トークンの作成時に適切なリソース URI を使用することが重要です。 これは、トークン サービスに特に重要です。このサービスでは、リソース URI を使用して、トークンのスコープを特定のデバイスに限定する必要があります。 これは、プロトコル ゲートウェイにはあまり関連がありません。このようなゲートウェイは、すべてのデバイスのトラフィックを既に仲介しているためです。

たとえば、 **device** という事前作成済みの共有アクセス ポリシーを使用するトークン サービスの場合、次のパラメーターを持つトークンが作成されます。

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

リソース URI を `myhub.azure-devices.net/devices`に設定するだけで、プロトコル ゲートウェイはすべてのデバイスに同じトークンを使用できます。

### <a name="use-security-tokens-from-service-components"></a>サービス コンポーネントからセキュリティ トークンを使用する
前述したように、サービス コンポーネントでは、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます。

エンドポイントで公開されるサービス機能を次に示します。

| エンドポイント | 機能 |
| --- | --- |
| `{iot hub host name}/devices` |デバイス ID を作成、更新、取得、削除します。 |
| `{iot hub host name}/messages/events` |デバイスからクラウドへのメッセージを受信します。 |
| `{iot hub host name}/servicebound/feedback` |クラウドからデバイスへのメッセージのフィードバックを受信します。 |
| `{iot hub host name}/devicebound` |クラウドからデバイスへのメッセージを送信します。 |

たとえば、 **registryRead** という事前作成済みの共有アクセス ポリシーを使用してトークンを生成するサービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices`
* 署名キー: `registryRead` ポリシーのいずれかのキー
* ポリシー名: `registryRead`
* 任意の有効期間
  
    var endpoint ="myhub.azure-devices.net/devices";   var policyName = 'device';   var policyKey = '...';
  
    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果は次のようになります (すべてのデバイス ID の読み取りアクセスが許可されます)。

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>サポートされている X.509 証明書
任意の X.509 証明書を使用して IoT Hub でデバイスを認証することができます。 次のトピックがあります。

* **既存の X.509 証明書**。 デバイスには、既に X.509 証明書が関連付けられている場合があります。 デバイスはこの証明書を使用して IoT Hub で認証を受けることができます。
* **自己生成および自己署名の X-509 証明書**。 デバイスの製造業者または社内のデプロイ担当者はこれらの証明書を生成し、対応する秘密キー (および証明書) をデバイスに格納することができます。 [OpenSSL][lnk openssl] や [Windows SelfSignedCertificate][lnk-selfsigned] ユーティリティなどのツールを使用することができます。
* **証明機関署名入りの X.509 証明書**。 証明機関 (CA) によって生成され署名された X.509 証明書を使用して、デバイスを識別し、IoT Hub でデバイスを認証することもできます。

デバイスは X.509 証明書またはセキュリティ トークンのいずれかを使用できますが、両方一緒に使用することはできません。

### <a name="register-an-x509-client-certificate-for-a-device"></a>デバイスの X.509 クライアント証明書を登録する
[C# 用 Azure IoT サービス SDK][lnk-service-sdk] (バージョン 1.0.8+) では、認証の際に X.509 クライアント証明書を使用するデバイスの登録をサポートします。 デバイスのインポート/エクスポートなどの他の API でも X.509 クライアント証明書をサポートします。

### <a name="c-support"></a>C\# のサポート
**RegistryManager** クラスでは、プログラムでデバイスを登録する方法が用意されています。 具体的には、**AddDeviceAsync** メソッドと **UpdateDeviceAsync** メソッドを使用することで、ユーザーは IoT Hub デバイス ID レジストリにデバイスを登録して更新できます。 これら 2 つのメソッドは、入力として **Device** インスタンスを取ります。 **Device** クラスには **Authentication** プロパティが含まれています。これにより、ユーザーはプライマリとセカンダリの X.509 証明書拇印を指定することができます。 拇印は、X.509 証明書の SHA-1 ハッシュ (DER バイナリ エンコードを使用して格納) を表します。 ユーザーは、プライマリ拇印、セカンダリ拇印、またはその両方を指定することができます。 証明書のロールオーバー シナリオを処理するために、プライマリ拇印とセカンダリ拇印がサポートされています。

> [!NOTE]
> IoT Hub で必要とする、または格納するのは X.509 クライアント証明書全体ではなく、拇印のみとなります。
> 
> 

X.509 クライアント証明書を使用してデバイスを登録するための C\# コード スニペットの例を次に示します。

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>ランタイム操作中に X.509 クライアント証明書を使用する
[.NET 用 Azure IoT device SDK][lnk-service-sdk] (バージョン 1.0.11+) では、X.509 クライアント証明書の使用をサポートします。

### <a name="c-support"></a>C\# のサポート
**DeviceAuthenticationWithX509Certifcate** クラスでは、X.509 クライアント証明書を使用した  **DeviceClient** インスタンスの作成をサポートします。

コード スニペットのサンプルを次に示します。

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>カスタム デバイスの認証
[IoT Hub のデバイス ID レジストリ][lnk-identity-registry]を使用して、デバイスごとのセキュリティ資格情報とアクセス制御を[トークン][lnk-sas-tokens]により構成できます。 ただし、IoT ソリューションで既にカスタム デバイス ID レジストリや認証スキームにかなり投資している場合、 *トークン サービス*を作成すると、この既存のインフラストラクチャを IoT Hub と統合できます。 この方法により、ソリューションで他の IoT 機能を使用できます。

トークン サービスはカスタム クラウド サービスの 1 つです。 このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub *共有アクセス ポリシー*を使用して、*デバイスを対象とする*トークンを作成します。 これらのトークンにより、デバイスは IoT Hub に接続できるようになります。

  ![Steps of the token service pattern][img-tokenservice]

トークン サービス パターンの主な手順を次に示します。

1. IoT Hub 共有アクセス ポリシーを作成し、IoT Hub に対する **DeviceConnect** アクセス許可を指定します。 このポリシーは、[Azure Portal][lnk-management-portal] またはプログラムで作成できます。 トークン サービスは、このポリシーを使用して、作成されるトークンに署名します。
2. IoT Hub にアクセスする必要があるデバイスは、トークン サービスに署名付きトークンを要求します。 デバイスは、カスタム デバイス ID レジストリ/認証スキームで認証し、トークン サービスがトークンの作成に使用するデバイス ID を特定できます。
3. トークン サービスは、トークンを返します。 トークンは、`/devices/{deviceId}` を `resourceURI` として使用して作成され、`deviceId` は認証対象のデバイスになります。 トークン サービスは、共有アクセス ポリシーを使用してトークンを作成します。
4. デバイスは、IoT Hub で直接トークンを使用します。

> [!NOTE]
> .NET クラス [SharedAccessSignatureBuilder][lnk-dotnet-sas] または Java クラス [IotHubServiceSasToken][lnk-java-sas] を使用して、トークン サービスでトークンを作成できます。
> 
> 

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。 トークンの期限が切れた時点で、IoT Hub はデバイスの接続を切断します。 その後、デバイスは新しいトークンをトークン サービスに要求する必要があります。 有効期限までの期間を短くすると、デバイスとトークン サービスの両方に対する負荷が増加します。

デバイスをハブに接続するには、デバイスが接続にデバイス キーではなくトークンを使用している場合でも、引き続き IoT Hub のデバイス ID レジストリにデバイスを追加する必要があります。 そのため、デバイスでトークンを使った認証を行うときに、[IoT Hub ID レジストリ][lnk-identity-registry]でデバイス ID を有効または無効にすることで、引き続きデバイスごとのアクセス制御を使用できます。 これにより、有効期限までの期間が長い場合にトークンを使用するリスクが軽減されます。

### <a name="comparison-with-a-custom-gateway"></a>カスタム ゲートウェイとの比較
IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。 これにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。 ただし、カスタム認証スキームとプロトコルとの関係が複雑すぎるため、すべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要になる場合があります。 その例として、[トランスポート層セキュリティ (TLS) と事前共有キー (PSK)][lnk-tls-psk] があります。 詳細については、[プロトコル ゲートウェイ][lnk-protocols]に関するトピックをご覧ください。

## <a name="reference"></a>リファレンス
### <a name="iot-hub-permissions"></a>IoT Hub のアクセス許可
次の表は、IoT hub へのアクセス制御に使用できるアクセス許可の一覧です。

| アクセス許可 | メモ |
| --- | --- |
| **RegistryRead** |デバイス ID レジストリへの読み取りアクセス権を許可します。 詳細については、「[デバイス ID レジストリ][lnk-identity-registry]」をご覧ください。 |
| **RegistryReadWrite** |デバイス ID レジストリへの読み取りと書き込みのアクセスを許可します。 詳細については、「[デバイス ID レジストリ][lnk-identity-registry]」をご覧ください。 |
| **ServiceConnect** |クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。 たとえば、D2C メッセージの受信、C2D メッセージの送信、対応する配信確認メッセージの取得のアクセス許可をバックエンド クラウド サービスに付与します。 |
| **DeviceConnect** |デバイス向けの通信エンドポイントへのアクセスを許可します。 たとえば、D2C メッセージの送信と、C2D メッセージの受信のアクセス許可を付与します。 このアクセス許可はデバイスによって使用されます。 |

### <a name="additional-reference-material"></a>参考資料
開発者ガイド内の他の参照トピックは次のとおりです。

* 「[IoT Hub エンドポイント][lnk-endpoints]」では、各 IoT Hub がランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
* 「[クォータと調整][lnk-quotas]」では、IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* 「[IoT Hub のデバイス SDK とサービス SDK][lnk-sdks]」では、IoT Hub とやりとりするデバイスとサービス アプリケーションの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* 「[Query language for twins, methods, and jobs (ツイン、メソッド、ジョブのクエリ言語)][lnk-query]」では、IoT Hub からデバイス ツイン、メソッド、ジョブに関する情報を取得する際に使用できるクエリ言語について説明します。
* 「[IoT Hub MQTT サポート][lnk-devguide-mqtt]」では、MQTT プロトコルの IoT Hub サポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ
IoT Hub へのアクセス制御の方法を理解できたら、次の開発者ガイドトピックも参考にしてください。

* [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-devguide-directmethods]
* [複数デバイスでのジョブのスケジュール][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [Azure IoT Hub を使ってみる][lnk-getstarted-tutorial]
* [チュートリアル: IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d-tutorial]
* [チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-d2c-tutorial]

<!-- links and images -->

[img token service]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md



<!---HONumber=Oct16_HO2-->


