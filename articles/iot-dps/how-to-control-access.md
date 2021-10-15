---
title: Microsoft Azure IoT Device Provisioning Service におけるセキュリティ エンドポイント
description: 概念 - バックエンド アプリの IoT Device Provisioning Service (DPS) へのアクセスを制御する方法。 セキュリティ トークンについての情報が含まれています。
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/22/2021
ms.author: v-stharr
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: dc33bf659785419619afe4f393a8a1ed09142452
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272531"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service のアクセスを制御する

この記事では、お使いの IoT デバイス プロビジョニング サービスをセキュリティで保護するために使用できるオプションについて説明します。 プロビジョニング サービスでは、"*認証*" と "*アクセス許可*" を使用して、各エンドポイントへのアクセス権が付与されます。 アクセス許可によって、認証プロセスで機能に基づいてサービス インスタンスへのアクセスを制限できます。

この記事では、以下について説明します。

* プロビジョニング サービスで、[サービスとデバイスの REST API](/rest/api/iot-dps/) の両方に対するアクセス許可を検証するために使用される認証プロセスとトークン。

* サービス API へのアクセス用にバックエンド アプリに付与できる別のアクセス許可。

## <a name="authentication"></a>認証

デバイス API では、キーベースおよび X.509 証明書ベースのデバイス認証がサポートされます。  

サービス API では、バックエンド アプリに対するキーベースの認証がサポートされます。  

キーベースの認証を使用する場合、デバイス プロビジョニング サービスでは、ネットワーク経由でのキーの送信を回避するために、セキュリティ トークンを使用してサービスが認証されます。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 Azure IoT Device Provisioning SDK を使用する場合、特別な構成を行うことなく自動的にトークンが生成されます。  

場合によっては、SDK を使用せずに HTTP デバイス プロビジョニング サービスの REST API を直接使用する必要があります。 次のセクションで、REST API に対して直接認証する方法について説明します。

## <a name="device-api-authentication"></a>デバイス API の認証

[デバイス API](/rest/api/iot-dps/device/runtime-registration) は、デバイス プロビジョニング サービスを証明し、IoT Hub 接続を受信するために、デバイスによって使用されます。

>[!NOTE]
>認証された接続を受信するには、まず登録を通じて、デバイスをデバイス プロビジョニング サービスに登録する必要があります。 サービス API を使用して登録を実行して、デバイスをプログラムによって登録します。

デバイスは、プロビジョニング プロセスの一部としてデバイス API に対して認証する必要があります。 認証するためにデバイスによって使用される方法は、登録グループまたは個々の登録を設定するときにユーザーが定義します。 認証方法に関係なく、デバイスでは、次の URL に HTTPS PUT を発行して自身をプロビジョニングする必要があります。

```http
    https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01
```
キーベースの認証を使用する場合は、セキュリティ トークンが次の形式で HTTP の  **Authorization**  要求ヘッダーに渡されます。

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

### <a name="security-token-structure-for-key-based-authentication"></a>キーベースの認証用のセキュリティ トークンの構造

セキュリティ トークンは、次の形式で HTTP の  **Authorization**  要求ヘッダーに渡されます。

```bash
    SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI} 
```

想定される値は次のとおりです。

| 値  | 説明 |
|:-------|:------------|
| `{signature}`  | 次の形式の HMAC-SHA256 シグネチャ文字列:  `{URL-encoded-resourceURI} + "\n" + expiry`。 **重要**: このキーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| `{expiry}`  | 1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。  |
| `{URL-encoded-resourceURI}`   |`{ID_Scope}/registrations/{registration_id}` の小文字の URL エンコード |
| `{policyName}`  | デバイス API の場合、このポリシーは常に "registration" です。 |

次の Python スニペットは、対称キー認証の種類を使用して、個々の登録用の  `uri`、`key`、`policy_name`、`expiry` の入力からトークンを計算する  `generate_sas_token`  と呼ばれる関数を示しています。

```python

from base64 import b64encode, b64decode, encode 
from hashlib import sha256 
from time import time 
from urllib.parse import quote_plus, urlencode 
from hmac import HMAC 

 def generate_sas_token(uri, key, policy_name, expiry=3600): 
    ttl = time() + expiry 
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl)) 
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest()) 

    rawtoken = { 
        'sr' :  uri, 
        'sig': signature, 
        'se' : str(int(ttl)), 
        'skn' : policy_name 
    } 

    return 'SharedAccessSignature ' + urlencode(rawtoken) 

print(generate_sas_token("myIdScope/registrations/mydeviceregistrationid", "00mysymmetrickey", "registration"))

```

結果は次の出力のようになります。

```bash

SharedAccessSignature sr=myIdScope%2Fregistrations%2Fmydeviceregistrationid&sig=SDpdbUNk%2F1DSjEpeb29BLVe6gRDZI7T41Y4BPsHHoUg%3D&se=1630175722&skn=registration 
```

次の例は、共有アクセス署名を使用してデバイス API での認証を行う方法を示しています。  

```python

curl -L -i -X PUT -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -H 'Authorization: [token]' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

対称キーベースの登録グループを使用する場合は、最初に登録グループ キーを使用して `device symmetric` キーを生成する必要があります。 登録グループのプライマリ キーまたはセカンダリ キーを使用して、デバイスの登録 ID の HMAC-SHA256 を計算します。 その後、結果が Base64 形式に変換され、派生デバイス キーが取得されます。 コード例を確認するには、「[対称キー登録グループを使用してデバイスをプロビジョニングする方法](how-to-legacy-device-symm-key.md?tabs=linux%22%20%5Cl%20%22derive-a-device-key)」を参照してください。 デバイス対称キーが派生された後、前の例を使用してデバイスを登録できます。

>[!WARNING]
>デバイス コードにグループ マスター キーを含めないようにするため、デバイス キーを派生するプロセスはデバイス外で行う必要があります。

### <a name="certificate-based-authentication"></a>証明書ベースの認証 

X.509 証明書ベースの認証用に個々の登録または登録グループを設定している場合、デバイスでは、発行された X.509 証明書を使用してデバイス API を証明する必要があります。 登録を設定してデバイス証明書を生成する方法については、次の記事を参照してください。

* クイックスタート - [シミュレートされた X.509 デバイスを Azure IoT Hub にプロビジョニングする](quick-create-simulated-device-x509.md)

* クイックスタート - [X.509 デバイスを Azure Device Provisioning Service に登録する](quick-enroll-device-x509.md)

登録が設定され、デバイス証明書が発行された後、デバイスの X.509 証明書を使用して Device API に対して認証する方法を次の例に示します。

```bash

curl -L -i -X PUT –cert ./[device_cert].pem –key ./[device_cert_private_key].pem -H 'Content-Type: application/json' -H 'Content-Encoding:  utf-8' -d '{"registrationId": "[registration_id]"}' https://global.azure-devices-provisioning.net/[ID_Scope]/registrations/[registration_id]/register?api-version=2021-06-01 

```

## <a name="service-api-authentication"></a>サービス API の認証

[サービス API](/rest/api/iot-dps/service/device-registration-state) は、登録状態の取得と、デバイスの登録の削除を行うために使用されます。 このサービスは、バックエンド アプリで[個々のグループ](/rest/api/iot-dps/service/individual-enrollment)と[登録グループ](/rest/api/iot-dps/service/enrollment-group)の両方をプログラムによって管理するためにも使用されます。 サービス API では、バックエンド アプリに対するキーベースの認証がサポートされます。  

サービス API のエンドポイントにアクセスするには、適切なアクセス許可が必要です。 たとえば、サービスに送信するすべてのメッセージと共に、バックエンド アプリにはセキュリティ資格情報が格納されているトークンを含める必要があります。

Azure IoT Hub Device Provisioning Service では、共有アクセス ポリシーに対するトークンの検証によって、エンドポイントへのアクセスが許可されます。 対称キーなどのセキュリティ資格情報がネットワーク上で送信されることはありません。

### <a name="access-control-and-permissions"></a>アクセス制御とアクセス許可

次の方法で[アクセス許可](#device-provisioning-service-permissions)を付与できます。

* **共有アクセス承認ポリシー**。 共有アクセス ポリシーにより、[アクセス許可](#device-provisioning-service-permissions)を自由に組み合わせて付与できます。 ポリシーは、[Azure Portal][lnk-management-portal] で定義することも、[Device Provisioning Service REST API][lnk-resource-provider-apis] を使用してプログラムで定義することもできます。 新しく作成されたプロビジョニング サービスには、次の既定のポリシーがあります。

* **provisioningserviceowner**: すべてのアクセス許可を持つポリシー。 詳細については、[権限](#device-provisioning-service-permissions)に関する項目をご覧ください。

> [!NOTE]
> Device Provisioning Service のリソースプロバイダーは、[Azure Resource Manager][lnk-azure-resource-manager] のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。

セキュリティ トークンを作成して使用する方法の詳細については、次のセクションをご覧ください。

HTTP は唯一サポートされているプロトコルであり、**Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。

#### <a name="example"></a>例

```bash
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT Device Provisioning Service SDK][lnk-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。

### <a name="security-tokens"></a>セキュリティ トークン

Device Provisioning Service では、サービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 [Azure IoT Device Provisioning Service SDK][lnk-sdks] を使用すると、特別な構成を行うことなく自動でトークンを生成できます。 一部のシナリオでは、セキュリティ トークンを自分で生成して直接使用する必要があります。 このようなシナリオには、HTTP のサーフェスの直接使用が含まれます。

### <a name="security-token-structure"></a>セキュリティ トークンの構造

セキュリティ トークンは、Device Provisioning Service の特定の機能へのアクセスを期限付きでサービスに許可するために使用します。 プロビジョニング サービスに接続する承認を取得するには、サービスが共有アクセスまたは対称キーのいずれかで署名されたセキュリティ トークンを送信する必要があります。

共有アクセス キーで署名されたトークンでは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスが許可されます。 

セキュリティ トークンには、次の形式があります。

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

考えられる値を次に示します。

| 値 | 説明 |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。 **重要**: キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。|
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} | 小文字のリソース URI の小文字の URL エンコード。 IoT Device Provisioning Service のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。 たとえば、「 `mydps.azure-devices-provisioning.net` 」のように入力します。 |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 |

>[!NOTE]
>URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。 たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

次の Node.js スニペットは、`resourceUri, signingKey, policyName, expiresInMins` の入力からトークンを計算する **generateSasToken** という名前の関数を示しています。 以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

比較として、セキュリティ トークンを生成する同等の Python コードを次に示します。

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> トークンの有効期間は IoT Device Provisioning Service のコンピューターで検証されるため、トークンを生成するコンピューターのクロックのずれは最小限である必要があります。

### <a name="use-security-tokens-from-service-components"></a>サービス コンポーネントからセキュリティ トークンを使用する

前述したように、サービス コンポーネントでは、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます。

エンドポイントで公開されるサービス機能を次に示します。

| エンドポイント | 機能 |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Device Provisioning Service を使用したデバイスの登録操作を提供します。 |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |デバイス登録グループを管理する操作を提供します。 |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |デバイスの登録状態を取得し、管理する操作を提供します。 |


たとえば、`enrollmentread` という事前作成済みの共有アクセス ポリシーを使用してトークンを生成するサービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{mydps}.azure-devices-provisioning.net`
* 署名キー: `enrollmentread` ポリシーのいずれかのキー
* ポリシー名: `enrollmentread`
* 任意の有効期間

![ポータルで Device Provisioning Service インスタンスの共有アクセス ポリシーを作成する][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は次のようになります (すべての登録レコードの読み取りアクセスが許可されます)。

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックは、IoT Device Provisioning Service へのアクセスの制御に関する詳細情報を提供しています。

### <a name="device-provisioning-service-permissions"></a>Device Provisioning Service のアクセス許可

次の表は、IoT Device Provisioning Service へのアクセス制御に使用できるアクセス許可の一覧です。

| 権限 | Notes |
| --- | --- |
| **ServiceConfig** |サービス構成を変更するためのアクセス権を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **EnrollmentRead** |デバイスの登録および登録グループの読み取りアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **EnrollmentWrite** |デバイスの登録および登録グループの書き込みアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistrationStatusRead** |デバイスの登録状態の読み取りアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistrationStatusWrite**  |デバイスの登録状態の削除のアクセス権を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/