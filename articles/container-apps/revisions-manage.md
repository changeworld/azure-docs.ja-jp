---
title: Azure Container Apps プレビューのリビジョンを管理する
description: Azure Container Apps でリビジョンとトラフィックの分割を管理します。
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 97270075f7ca7e33dabb23a6aa21ed746518ed65
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131891871"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>Azure Container Apps プレビューのリビジョンを管理する

Azure Container Apps で複数のリビジョンをサポートすると、バージョン管理と[各リビジョンに送信されるとトラフィック](#traffic-splitting)の量を管理できます。 次のコマンドを使用して、お使いのコンテナー アプリでのリビジョンの管理方法を制御します。

## <a name="list"></a>List

`az containerapp revision list` を使用して、コンテナー アプリに関連付けられているすべてのリビジョンを一覧表示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="show"></a>表示

`az containerapp revision show` を使用して、特定のリビジョンに関する詳細を表示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="update"></a>更新

コンテナー アプリを更新するには、`az containerapp update` を使用します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="activate"></a>アクティブ化

`az containerapp revision activate` を使用して、リビジョンをアクティブにします。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision activate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```poweshell
az containerapp revision activate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="deactivate"></a>非アクティブ化

`az container app revision deactivate` を使用して、使用されなくなったリビジョンを非アクティブ化します。 非アクティブ化すると、リビジョンの実行中のすべてのレプリカが停止します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="restart"></a>Restart

既存のすべてのコンテナー アプリのリビジョンは、再起動されるまでこのシークレットにアクセスできません

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="set-active-revision-mode"></a>アクティブなリビジョン モードの設定

お使いのコンテナー アプリで複数のアクティブなリビジョンをサポートするかどうかを構成します。

`activeRevisionsMode` プロパティで受け入れられる値は次の 2 つです。

- `multiple`: 複数のアクティブなリビジョンを許可するようにコンテナー アプリを構成します。

- `single`: リビジョンがアクティブになると、他のすべてのリビジョンが自動的に非アクティブになります。 `single` モードを有効にすると、 リビジョンスコープの変更を作成し、新しいリビジョンが作成されるときに、その他のリビジョンが自動的に非アクティブになります。

```json
{
  ...
  "resources": [
  {
    ...
    "properties": {
        "configuration": {
          "activeRevisionsMode": "multiple"
      }
    }
  }]
}
```

次の構成フラグメントは、`activeRevisionsMode` プロパティを設定する方法を示しています。 このプロパティに加えられた変更には、コンテナー アプリの完全な ARM テンプレートのコンテキストが必要です。

## <a name="traffic-splitting"></a>トラフィックの分割

割合の値を割り当てると適用されます。異なるリビジョン間でトラフィックを分散する方法を決定できます。 トラフィックの分割ルールは、重みを異なるリビジョンに設定することで割り当てられます。

次の例では、3 つのリビジョン間でトラフィックを分割する方法を示します。

```json
{
  ...
  "configuration": {
    "ingress": {
      "traffic": [
        {
          "revisionName": <REVISION1_NAME>,
          "weight": 50
        },
        {
          "revisionName": <REVISION2_NAME>,
          "weight": 30
        },
        {
          "latestRevision": true,
          "weight": 20
        }
      ]
    }
  }
}
```

各リビジョンには、次のルールに基づいてトラフィックが割り当てられます。

- 要求の 50% が REVISION1 に割り当てられる
- 要求の 30% が REVISION2 に割り当てられる
- 要求の 20% が最新のリビジョンに割り当てられる

すべてのリビジョンの重みの合計が、100 になる必要があります。

この例では、`<REVISION*_NAME>` プレースホルダーをお使いのコンテナー アプリのリビジョン名に置き換えます。 リビジョン名には [list](#list) コマンドを使用してアクセスします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [開始するには](get-started.md)
