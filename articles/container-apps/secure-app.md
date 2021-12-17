---
title: Azure Container Apps プレビューでアプリをセキュリティ保護する
description: Azure Container Apps でのアプリケーションのセキュリティ保護について説明します。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: aca235582babedaa9bf6a7c44a835734ba4cb5dc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026997"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでアプリをセキュリティ保護する

Azure Container Apps を使うと、アプリケーションで機密性の高い構成値を安全に格納できます。 アプリケーション レベルで定義された後、セキュリティ保護された値は、コンテナー、スケーリング ルール内、および Dapr 経由で使用できます。

- シークレットのスコープはアプリケーションであり、アプリケーションの特定のリビジョンの外部です。
- シークレットを追加、削除、または変更しても、新しいリビジョンは生成されません。
- アプリケーションの各リビジョンで、1 つまたは複数のシークレットを参照できます。
- 複数のリビジョンで同じシークレットを参照できます。

シークレットが更新または削除されるときは、2 つの方法のいずれかで変更に対応できます。

 1. 新しいリビジョンをデプロイします。
 2. 既存のリビジョンを再起動します。

更新または削除されたシークレットにより、リビジョンが自動的に再起動されることはありません。

- シークレットを削除する前に、古いシークレットを参照しない新しいリビジョンをデプロイします。
- シークレットの値を変更する場合は、新しい値を使うためにリビジョンを再起動する必要があります。

## <a name="defining-secrets"></a>シークレットの定義

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm-template)

シークレットは、`resources.properties.configuration.secrets` セクションのアプリケーション レベルで定義されます。

```json
"resources": [
{
    ...
    "properties": {
        "configuration": {
            "secrets": [
            {
                "name": "queue-connection-string",
                "value": "<MY-CONNECTION-STRING-VALUE>"
            }],
        }
    }
}
```

ここで、キュー ストレージ アカウントへの接続文字列は `secrets` 配列で宣言されています。 この構成を使うには、`<MY-CONNECTION-STRING-VALUE>` を実際の接続文字列の値に置き換えます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

シークレットは、`--secrets` パラメーターを使って定義します。

- パラメーターは、名前と値のペアのコンマで区切られたセットを受け入れます。
- 各ペアを区切るには、等号 (`=`) を使います。

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

ここで、キュー ストレージ アカウントへの接続文字列は `--secrets` パラメーターで宣言されています。 `queue-connection-string` の値は、`$CONNECTION_STRING` という名前の環境変数から取得されます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

シークレットは、`--secrets` パラメーターを使って定義します。

- パラメーターは、名前と値のペアのコンマで区切られたセットを受け入れます。
- 各ペアを区切るには、等号 (`=`) を使います。

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

ここで、キュー ストレージ アカウントへの接続文字列は `--secrets` パラメーターで宣言されています。 `queue-connection-string` の値は、`$CONNECTION_STRING` という名前の環境変数から取得されます。

---

## <a name="using-secrets"></a>シークレットの使用

アプリケーションのシークレットは、`secretref` プロパティによって参照されます。 シークレットの値はアプリケーション レベルのシークレットにマップされ、`secretref` の値はアプリケーション レベルで宣言されたシークレット名と一致します。

## <a name="example"></a>例

次の例のアプリケーションでは、接続文字列がアプリケーション レベルで宣言され、`secretref` を通して構成全体で使用されています。

# <a name="arm-template"></a>[ARM テンプレート](#tab/arm-template)

この例のアプリケーション接続文字列は、`queue-connection-string` として宣言され、構成セクションの他の場所で使うことができます。

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

ここで、`connection-string` という名前の環境変数の値は、アプリケーション レベルの `queue-connection-string` シークレットから取得されます。 また、Azure Queue Storage のスケーリング ルールの承認構成では、接続が確立されるときに `queue-connection-string` が使用されます。

ARM テンプレートでシークレットの値がソース管理にコミットされないようにするには、シークレットの値を ARM テンプレートのパラメーターとして渡します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この例では、環境変数で参照されるシークレットを使うアプリケーションを、Azure CLI を使って作成します。

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

ここで、`connection-string` という名前の環境変数の値は、`secretref` を使ってアプリケーション レベルの `queue-connection-string` シークレットから取得されます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

この例では、環境変数で参照されるシークレットを使うアプリケーションを、Azure CLI を使って作成します。

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

ここで、`connection-string` という名前の環境変数の値は、`secretref` を使ってアプリケーション レベルの `queue-connection-string` シークレットから取得されます。

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Containers](containers.md)
