---
title: Azure IoT Central で REST API を承認する
description: IoT Central REST API 呼び出しを認証および承認する方法
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 73ef942b42858a3219502fe09c3b9281be81f964
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776549"
---
# <a name="how-to-authenticate-and-authorize-iot-central-rest-api-calls"></a>IoT Central REST API 呼び出しを認証および承認する方法

IoT Central REST API を使用すると、IoT Central アプリケーションと統合するクライアント アプリケーションを開発できます。 デバイス テンプレート、デバイス、ジョブ、ユーザー、ロールなどの IoT Central アプリケーションのリソースを操作するには、REST API を使用します。

すべての IoT Central REST API 呼び出しには、IoT Central が呼び出し元の ID と、アプリケーション内で呼び出し元に付与されるアクセス許可を決定するために使用する Authorization ヘッダーが必要です。

この記事では、Authorization ヘッダーで使用できるトークンの種類と、それらを取得する方法について説明します。

## <a name="token-types"></a>トークンの種類

REST API を使用して IoT Central アプリケーションにアクセスするには、次の方法を使用できます。

- _Azure Active Directory ベアラー トークン_。 ベアラー トークンは、Azure Active Directory ユーザー アカウントに関連付けられています。 このトークンにより、IoT Central アプリケーションでユーザーが持っているのと同じアクセス許可が呼び出し元に付与されます。
- IoT Central API トークン。 API トークンは、IoT Central アプリケーションのロールに関連付けられています。

IoT Central のユーザーとロールの詳細については、「[IoT Central アプリケーションでユーザーとロールを管理する](howto-manage-users-roles.md)」を参照してください。

## <a name="get-a-bearer-token"></a>ベアラー トークンを取得する

Azure Active Directory ユーザー アカウントのベアラー トークンを取得するには、次の Azure CLI コマンドを使用します。

```azurecli
az login
az account get-access-token --resource https://apps.azureiotcentral.com
```

> [!IMPORTANT]
> `az login` コマンドは、Cloud Shell を使用している場合でも必要です。

上のコマンドからの JSON 出力は、次の例のようになります。

```json
{
  "accessToken": "eyJ0eX...fNQ",
  "expiresOn": "2021-03-22 11:11:16.072222",
  "subscription": "{your subscription id}",
  "tenant": "{your tenant id}",
  "tokenType": "Bearer"
}
```

ベアラー トークンの有効期間は約 1 時間です。有効期間を過ぎたら、新しいものを作成する必要があります。

## <a name="get-an-api-token"></a>API トークンを取得する

API トークンを取得するには、IoT Central UI または REST API 呼び出しを使用します。

IoT Central UI を使用する場合:

1. **[認証] > [API トークン]** に移動します。
1. **[+ トークンの生成]** を選択します。
1. トークンの名前を入力し、ロールを選択します。
1. **[Generate] \(生成)** を選択します。
1. IoT Central に、次の例のようなトークンが表示されます。

    `SharedAccessSignature sr=5782ed70...&sig=dvZZE...&skn=operator-token&se=1647948035850`

    API トークンを確認できるのは、この画面が表示されたときだけです。分からなくなった場合は、新しいものを生成する必要があります。

API トークンの有効期間は、約 1 年間です。 IoT Central アプリケーションでは、組み込みとカスタムの両方のロールに対してトークンを生成できます。

アクセスを取り消す必要がある場合は、IoT Central UI で API トークンを削除できます。

REST API を使用する場合:

1. 次の REST API を使用して、アプリケーションからロール ID の一覧を取得します。

    ```http
    GET https://{your app subdomain}.azureiotcentral.com/api/roles?api-version=1.0
    ```

    この要求に対する応答は、次の例のようになります。

    ```json
    {
      "value": [
        {
          "displayName": "Administrator",
          "id": "ca310b8d-2f4a-44e0-a36e-957c202cd8d4"
        },
        {
          "displayName": "Operator",
          "id": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        },
        {
          "displayName": "Builder",
          "id": "344138e9-8de4-4497-8c54-5237e96d6aaf"
        }
      ]
    }
    ```

1. REST API を使用して、ロールの API トークンを作成します。 たとえば、オペレーター ロールに対して `operator-token` という名前の API トークンを作成するには、次のようにします。

    ```http
    PUT https://{your app subdomain}.azureiotcentral.com/api/roles/operator-token?api-version=1.0
    ```

    要求本文:

    ```json
    {
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ]
    }
    ```

    上記のコマンドに対する応答は、次の JSON のようになります。

    ```json
    {
      "expiry": "2022-03-22T12:01:27.889Z",
      "id": "operator-token",
      "roles": [
        {
          "role": "ae2c9854-393b-4f97-8c42-479d70ce626e"
        }
      ],
      "token": "SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889"
    }
    ```

    API トークンには、この応答を通じてのみアクセスできます。これを失った場合は、新しいものを生成する必要があります。

REST API を使用すると、アプリケーションで API トークンを一覧表示したり、削除したりできます。

## <a name="use-a-bearer-token"></a>ベアラー トークンを使用する

REST API 呼び出しを行うときにベアラー トークンを使用する場合、Authorization ヘッダーは次の例のようになります。

`Authorization: Bearer eyJ0eX...fNQ`

## <a name="use-an-api-token"></a>API トークンを使用する

REST API 呼び出しを行うときに API トークンを使用する場合、Authorization ヘッダーは次の例のようになります。

`Authorization: SharedAccessSignature sr=e8a...&sig=jKY8W...&skn=operator-token&se=1647950487889`

## <a name="next-steps"></a>次のステップ

REST API の呼び出しを承認する方法を学習したので、お勧めする次のステップは、「[ユーザーとロールを 管理するために IoT Central REST API を使用する方法](howto-manage-users-roles-with-rest-api.md)」です。
