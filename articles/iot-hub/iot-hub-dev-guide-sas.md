---
title: SAS トークンを使用して IoT Hub へのアクセスを制御する |Microsoft Docs
description: Shared Access Signature トークンを使用して、デバイスアプリとバックエンド アプリの IoT Hub へのアクセスを制御する方法。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 032f67427f070818aa6874d33414c857fa6ca39c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867184"
---
# <a name="control-access-to-iot-hub-using-shared-access-signatures-and-security-tokens"></a>Shared Access Signature とセキュリティ トークンを使用して IoT Hub へのアクセスを制御する

この記事では、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。 IoT Hub では、"*アクセス許可*" を使用して、IoT Hub の各エンドポイントへのアクセス権を付与します。 次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

この記事では、次の内容について説明します。

* IoT Hub へアクセスするためにクライアントに付与できるアクセス許可の種類。
* アクセス許可を確認するために IoT Hub トークンが使用されます。
* 資格情報のスコープを指定して特定のリソースへのアクセスを制限する方法。
* 既存のデバイス ID のレジストリまたは認証スキームを使用した、カスタムのデバイス認証メカニズム。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub のエンドポイントにアクセスするには、適切なアクセス許可が必要です。 たとえば、デバイスは IoT Hub に送信するすべてのメッセージにセキュリティ資格情報含めたトークンを付けなければいけません。 ただし、デバイスの対称キーと同様に、署名キーはネットワーク経由で送信されることはありません。

## <a name="access-control-and-permissions"></a>アクセス制御とアクセス許可

IoT ハブ レベルのアクセスには共有アクセス ポリシーを使用し、個々のデバイスの資格情報を使用して、そのデバイスへのアクセスのみをスコープにします。

### <a name="iot-hub-level-shared-access-policies"></a>IoT ハブ レベルの共有アクセス ポリシー

共有アクセス ポリシーにより、アクセス許可を自由に組み合わせて付与できます。 ポリシーは、[Azure portal](https://portal.azure.com) で定義したり、[IoT Hub Resource REST API](/rest/api/iothub/iothubresource) や [az iot hub policy](/cli/azure/iot/hub/policy) CLI を使ってプログラムで定義できます。 新しく作成された IoT Hub には、次の既定のポリシーがあります。
  
| 共有アクセス ポリシー | アクセス許可 |
| -------------------- | ----------- |
| iothubowner | すべてのアクセス許可 |
| サービス (service) | **ServiceConnect** アクセス許可 |
| device | **DeviceConnect** アクセス許可 |
| registryRead | **RegistryRead** アクセス許可 |
| registryReadWrite | **RegistryRead** および **RegistryWrite** アクセス許可 |

次の表は、IoT hub へのアクセス制御に使用できるアクセス許可の一覧です。

| 権限 | Notes |
| --- | --- |
| **ServiceConnect** |クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。 <br/>デバイスからクラウドへのメッセージの受信、クラウドからデバイスへのメッセージの送信、対応する配信確認メッセージの取得のアクセス許可を付与します。 <br/>ファイル アップロードの配信確認メッセージの取得のアクセス許可を付与します。 <br/>タグおよび必要なプロパティを更新するためのツインへのアクセス、報告されるプロパティの取得、クエリの実行のアクセス許可を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **DeviceConnect** |デバイス向けのエンドポイントへのアクセスを許可します。 <br/>デバイスからクラウドへのメッセージの送信、クラウドからデバイスへのメッセージの受信のアクセス許可を付与します。 <br/>デバイスからのファイル アップロードの実行のアクセス許可を付与します。 <br/>デバイス ツインの必要なプロパティ通知の受信と、デバイス ツインの報告されるプロパティの更新のアクセス許可を付与します。 <br/>ファイル アップロードの実行のアクセス許可を付与します。 <br/>このアクセス許可はデバイスによって使用されます。 |
| **RegistryRead** |ID レジストリへの読み取りアクセスを許可します。 詳細については、「[Identity registry](iot-hub-devguide-identity-registry.md)」(ID レジストリ) を参照してください。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistryReadWrite** |ID レジストリへの読み取りと書き込みアクセスを許可します。 詳細については、「[Identity registry](iot-hub-devguide-identity-registry.md)」(ID レジストリ) を参照してください。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |

### <a name="per-device-security-credentials"></a>デバイスごとのセキュリティ資格情報

各 IoT Hub には [ID レジストリ](iot-hub-devguide-identity-registry.md)が含まれます。この ID レジストリ内の各デバイスでは、対応するデバイスのエンドポイントを対象として **DeviceConnect** アクセス許可を付与する、セキュリティ資格情報を構成できます。

## <a name="security-tokens"></a>セキュリティ トークン

IoT Hub では、デバイスとサービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 これらのセキュリティ トークンは、Shared Access Signature (SAS) トークンです。 [Azure IoT SDK](iot-hub-devguide-sdks.md) を使用すると、特別な構成を行うことなく自動でトークンを生成できます。 一部のシナリオでは、セキュリティ トークンを自分で生成して直接使用する必要があります。 これらのシナリオは、次のとおりです。

* 直接、MQTT、AMQP、または HTTPS を使用する。

* トークンのサービス パターンを実装する (「[カスタム デバイスの認証](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices)」で説明)。

セキュリティ トークンは、IoT Hub の特定の機能へのアクセスを期限付きでデバイスとサービスに許可するために使用します。 IoT Hub に接続する承認を取得するには、デバイスとサービスが共有アクセス キーまたは対称キーのいずれかで署名されたセキュリティ トークンを送信する必要があります。 これらのキーは、ID レジストリにデバイス ID と共に格納されます。

### <a name="security-token-structure"></a>セキュリティ トークンの構造

共有アクセス キーで署名されたトークンでは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスが許可されます。 デバイス ID の対称キーで署名されたトークンは、関連付けられたデバイス ID の **DeviceConnect** アクセス許可のみを付与します。

セキュリティ トークンには、次の形式があります。

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

考えられる値を次に示します。

| 値 | 説明 |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。 **重要**:キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| {resourceURI} |IoT Hub のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。 たとえば、`myHub.azure-devices.net/devices/device1` のように指定します。 |
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} |小文字のリソース URI の小文字の URL エンコード |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 トークンがデバイス レジストリ資格情報を参照する場合は存在しません。 |

URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。 たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

### <a name="nodejs"></a>[Node.js](#tab/node)

次の Node.js スニペットは、`resourceUri, signingKey, policyName, expiresInMins` の入力からトークンを計算する **generateSasToken** という名前の関数を示しています。 以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

### <a name="python"></a>[Python](#tab/python)

比較として、セキュリティ トークンを生成する同等の Python コードを次に示します。

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```
### <a name="c"></a>[C#](#tab/csharp)

セキュリティ トークンを生成する C# の機能を下に示します。

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```
### <a name="java"></a>[Java](#tab/java)

Java の場合:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```
---

### <a name="protocol-specifics"></a>プロトコルの詳細

MQTT、AMQP、および HTTPS など、サポートされているプロトコルごとに、さまざまな方法でトークンが転送されます。

MQTT を使用する場合、CONNECT パケットには、ClientId として deviceId が指定され、ユーザー名フィールドには `{iothubhostname}/{deviceId}`、パスワード フィールドには SAS トークンが指定されます。 `{iothubhostname}` は IoT Hub の完全な CName とする必要があります (contoso.azure-devices.net など)。

[AMQP](https://www.amqp.org/) を使用する場合、IoT Hub では [SASL PLAIN](https://tools.ietf.org/html/rfc4616) と [AMQP Claims-Based-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) がサポートされます。

AMQP Claims-Based-Security の場合、標準でこれらのトークンの送信方法が指定されます。

SASL PLAIN では、 **ユーザー名** を以下のように指定できます。

* `{policyName}@sas.root.{iothubName}` (IoT Hub レベルのトークンを使用する場合)。
* `{deviceId}@sas.{iothubname}` (デバイスを対象とするトークンを使用する場合)。

どちらの場合も、[IoT Hub セキュリティ トークン](iot-hub-dev-guide-sas.md#security-tokens)に関するセクションで説明されているように、パスワード フィールドにトークンが含まれています。

HTTPS では、 **Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。

たとえば、Username (DeviceId は大文字と小文字を区別する): `iothubname.azure-devices.net/DeviceId`

パスワード (CLI 拡張コマンド [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) または [Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して SAS トークンを生成できます):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) を使用すると、サービスに接続した時点で自動的にトークンが生成されます。 場合によっては、Azure IoT SDK でサポートされないプロトコルや認証方法もあります。

### <a name="special-considerations-for-sasl-plain"></a>SASL PLAIN に関する特別な考慮事項

SASL PLAIN を AMQP で使用する場合、IoT Hub に接続するクライアントは、TCP 接続ごとにトークンを 1 つ使用できます。 トークンの有効期限が切れると、サービスから TCP 接続が切断され、再接続がトリガーされます。 この動作は、バックエンド アプリの場合は問題ありませんが、デバイス アプリの場合は、次の理由から有害である可能性があります。

* ゲートウェイは、通常多くのデバイスの代理として接続しています。 SASL PLAIN を使用する場合、IoT Hub に接続するデバイスごとに、個別の TCP 接続を作成する必要があります。 このシナリオにより、電源とネットワーク リソースの消費量が大幅に増大し、各デバイスの接続の待機時間が増加します。

* 各トークンの有効期限が切れた後に、再接続に使用するリソースの量が増加すると、リソースが限られたデバイスには悪影響が出ます。

## <a name="use-security-tokens-from-service-components"></a>サービス コンポーネントからセキュリティ トークンを使用する

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

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は次のようになります (すべてのデバイス ID の読み取りアクセスが許可されます)。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="authenticating-a-device-to-iot-hub"></a>デバイスを IoT Hub に認証

### <a name="supported-x509-certificates"></a>サポートされている X.509 証明書

証明書拇印や証明書機関 (CA) を Azure IoT Hub にアップロードすることで、IoT Hub のデバイスを X.509 証明書で認証できます。 詳細については、「[X.509 CA 証明書を使用したデバイス認証](iot-hub-x509ca-overview.md)」をご覧ください。 IoT ハブで証明機関から証明書をアップロードし、検証する方法については、「[Azure IoT Hub での X.509 セキュリティの設定](./tutorial-x509-scripts.md)」を参照してください。

### <a name="enforcing-x509-authentication"></a>X.509 認証の適用

セキュリティを強化するために、デバイスとモジュールの SAS 認証を許可しないように IoT ハブを構成し、X.509 を許可されている唯一の認証オプションとして残します。 現在のところ、この機能は Azure portal では使用できません。 構成するには、IoT Hub リソース プロパティの `disableDeviceSAS` と `disableModuleSAS` を `true` に設定します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.disableDeviceSAS=true properties.disableModuleSAS=true
```

### <a name="use-sas-tokens-as-a-device"></a>デバイスとして SAS トークンを使用する

セキュリティ トークンを使用して IoT Hub で **DeviceConnect** アクセス許可を取得するには、[ID レジストリの対称デバイス キー](#use-a-symmetric-key-in-the-identity-registry)を使用する方法と、[共有アクセス キー](#use-a-shared-access-policy-to-access-on-behalf-of-a-device)を使用する方法の 2 通りがあります。

デバイスからアクセスできるすべての機能は、仕様により、`/devices/{deviceId}`というプレフィックスを持つエンドポイントで公開されることにご注意ください。

デバイス向けのエンドポイントを次に示します (プロトコルは関係ありません)。

| エンドポイント | 機能 |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |デバイスからクラウドへのメッセージを送信します。 |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |クラウドからデバイスへのメッセージを受信します。 |

#### <a name="use-a-symmetric-key-in-the-identity-registry"></a>ID レジストリの対称キーを使用する

デバイス ID の対称キーを使用してトークンを生成すると、トークンの policyName (`skn`) 要素は省略されます。

たとえば、すべてのデバイス機能にアクセスするために作成されるトークンには、次のパラメーターが必要です。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `{device id}` ID の任意の対称キー
* ポリシー名なし
* 任意の有効期間

上の Node.js 関数を使用した例を次に示します。

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> CLI 拡張コマンド [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) または [Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して SAS トークンを生成できます。

#### <a name="use-a-shared-access-policy-to-access-on-behalf-of-a-device"></a>デバイスの代わりに共有アクセス ポリシーを使用してアクセスする

共有アクセス ポリシーからトークンを作成するときは、`skn` フィールドをポリシーの名前に設定します。 このポリシーは **DeviceConnect** アクセス許可を付与する必要があります。

共有アクセス ポリシーを使用してデバイスの機能にアクセスするための 2 つの主なシナリオは次のとおりです。

* [クラウド プロトコル ゲートウェイ](iot-hub-devguide-endpoints.md)
* [トークン サービス](iot-hub-dev-guide-sas.md#create-a-token-service-to-integrate-existing-devices) (カスタム認証スキームの実装に使用)

共有アクセス ポリシーは任意のデバイスとして接続するためのアクセス権を付与する可能性があるため、セキュリティ トークンの作成時に適切なリソース URI を使用することが重要です。 この設定は、トークン サービスに特に重要です。このサービスでは、リソース URI を使用して、トークンのスコープを特定のデバイスに限定する必要があります。 これは、プロトコル ゲートウェイにはあまり関連がありません。このようなゲートウェイは、すべてのデバイスのトラフィックを既に仲介しているためです。

たとえば、 **device** という事前作成済みの共有アクセス ポリシーを使用するトークン サービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `device` ポリシーのいずれかのキー
* ポリシー名: `device`
* 任意の有効期間

上の Node.js 関数を使用した例を次に示します。

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

リソース URI を `myhub.azure-devices.net/devices`に設定するだけで、プロトコル ゲートウェイはすべてのデバイスに同じトークンを使用できます。

## <a name="create-a-token-service-to-integrate-existing-devices"></a>既存のデバイスを統合するためにトークン サービスを作成する

IoT Hub の [ID レジストリ](iot-hub-devguide-identity-registry.md)を使用して、デバイス/モジュールごとのセキュリティ資格情報とアクセス制御を[トークン](iot-hub-dev-guide-sas.md#security-tokens)により構成できます。 IoT ソリューションにすでにカスタム ID レジストリや認証スキームがある場合、*トークン サービス* を作成してこのインフラストラクチャを IoT Hub と統合することを検討してください。 この方法により、ソリューションで他の IoT 機能を使用できます。

トークン サービスはカスタム クラウド サービスの 1 つです。 これにより、**DeviceConnect** アクセス許可が指定された IoT Hub の "*共有アクセス ポリシー*" を使用して、"*デバイス スコープ*" または "*モジュール スコープ*" のトークンが作成されます。 これらのトークンにより、デバイスとモジュールは IoT Hub に接続できるようになります。

![Steps of the token service pattern](./media/iot-hub-devguide-security/tokenservice.png)

トークン サービス パターンの主な手順を次に示します。

1. IoT Hub に対する **DeviceConnect** アクセス許可を指定して、IoT Hub の共有アクセス ポリシーが作成されます。 このポリシーは、[Azure portal](https://portal.azure.com) またはプログラムで作成できます。 トークン サービスは、このポリシーを使用して、作成されるトークンに署名します。

2. IoT Hub にアクセスする必要があるデバイス/モジュールは、トークン サービスに署名付きトークンを要求します。 デバイスは、カスタム ID レジストリ/認証スキームで認証し、トークン サービスがトークンの作成に使用するデバイス/モジュール ID を特定できます。

3. トークン サービスは、トークンを返します。 トークンは、`/devices/{deviceId}` または `/devices/{deviceId}/module/{moduleId}` を `resourceURI` として使用して作成され、`deviceId` は認証対象のデバイスになり、`moduleId` は認証対象のモジュールになります。 トークン サービスは、共有アクセス ポリシーを使用してトークンを作成します。

4. デバイス/モジュールは、IoT Hub で直接トークンを使用します。

> [!NOTE]
> .NET クラス [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) または Java クラス [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) を使用して、トークン サービスでトークンを作成できます。

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。 トークンの期限が切れた時点で、IoT Hub はデバイス/モジュールの接続を切断します。 その後、デバイス/モジュールは新しいトークンをトークン サービスに要求する必要があります。 有効期限までの期間が短いと、デバイス/モジュールとトークン サービスの両方で負荷が増加します。

デバイス/モジュールをハブに接続するには、デバイス/モジュールが接続にキーではなくトークンを使用している場合でも、引き続き IoT Hub の ID レジストリにデバイス/モジュールを追加する必要があります。 そのため、[ID レジストリ](iot-hub-devguide-identity-registry.md)でデバイス/モジュール ID を有効または無効にすることで、引き続きデバイスごと/モジュールごとのアクセス制御を行うことができます。 この方法は、有効期限までの期間が長い場合にトークンを使用するリスクを軽減します。

### <a name="comparison-with-a-custom-gateway"></a>カスタム ゲートウェイとの比較

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。 このパターンにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。 ただし、カスタム認証スキームがプロトコルとそのように組み合わさっている場合、すべてのトラフィックを処理するには *カスタム ゲートウェイ* が必要になる場合があります。 このようなシナリオの例として、[トランスポート層セキュリティ (TLS) と事前共有キー (PSK)](https://tools.ietf.org/html/rfc4279) の使用があります。 詳細については、[プロトコル ゲートウェイ](iot-hub-protocol-gateway.md)に関する記事をご覧ください。


## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。

* [スロットルとクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータとスロットルの動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [IoT Hub のクエリ言語](iot-hub-devguide-query-language.md): IoT Hub からデバイス ツインとジョブに関する情報を取得する際に使用できるクエリ言語について説明します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

* [RFC 5246 - トランスポート層セキュリティ (TLS) プロトコル バージョン 1.2](https://tools.ietf.org/html/rfc5246/) では、TLS 認証についてさらに詳細な情報が提供されます。

* 証明書機関を使用する認証の詳細については、「[X.509 CA 証明書を使用したデバイス認証](iot-hub-x509ca-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Hub へのアクセス制御の方法を理解できたら、次の IoT Hub 開発者ガイドのトピックもご覧ください。

* [デバイス ツインを使って状態と構成を同期する](iot-hub-devguide-device-twins.md)
* [デバイスでダイレクト メソッドを呼び出す](iot-hub-devguide-direct-methods.md)
* [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご覧ください。

* [Azure IoT Hub を使ってみる](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)
* [IoT Hub を使用した C2D メッセージの送信方法](iot-hub-csharp-csharp-c2d.md)
* [IoT Hub の D2C メッセージの処理方法](tutorial-routing.md)