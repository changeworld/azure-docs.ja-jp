---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113211"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `node --version` を実行して、Node.js がインストールされていることを確認します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

まず、ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

作成したディレクトリのルートに、**phone-numbers-quickstart.js** というファイルを作成します。 そこに次のスニペットを追加します。

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services Phone Numbers クライアント ライブラリをインストールします。

```console
npm install @azure/communication-phone-numbers --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが追加されます。

## <a name="authenticate-the-client"></a>クライアントを認証する

クライアント ライブラリから **PhoneNumbersClient** をインポートし、接続文字列を使用してインスタンス化します。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法について確認してください。

**phone-numbers-quickstart.js** の先頭に次のコードを追加します。

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>電話番号を管理する

### <a name="search-for-available-phone-numbers"></a>利用可能な電話番号を検索する

電話番号を購入するにはまず、利用可能な電話番号を検索する必要があります。 電話番号を検索するには、市外局番、割り当ての種類、[電話番号機能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[電話番号の種類](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)、数量を指定します。 ただし、電話番号の種類が無料電話番号の場合、市外局番の指定は任意です。

`main` 関数に次のスニペットを追加します。

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>電話番号を購入する

電話番号の検索の結果は `PhoneNumberSearchResult` です。 そこに含まれる `searchId` を番号購入 API に渡すことで、検索した番号を取得することができます。 電話番号購入 API を呼び出すと、Azure アカウントに料金が発生するので注意してください。

`main` 関数に次のスニペットを追加します。

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>電話番号機能を更新する

電話番号を購入した後、その機能を更新するには、次のコードを追加します。

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>購入した電話番号を取得する

購入した番号は、その後、クライアントから取得することができます。 購入した電話番号を取得するには、`main` 関数に次のコードを追加します。

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

購入したすべての電話番号を取得することもできます。

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>電話番号を解放する

購入した電話番号を解放することができます。 `main` 関数に次のコードスニペットを追加します。

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>コードの実行

`node` コマンドを使用して、**phone-numbers-quickstart.js** ファイルに追加したコードを実行します。

```console
node phone-numbers-quickstart.js
```