---
title: Azure Container Apps プレビューで GitHub Actions を含むリビジョンを発行する
description: Azure Container Apps プレビューで GitHub Actions を使用して新しいリビジョンを自動的に作成する方法について説明します
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: d83c6904f721d429488bca50359c3b32877ad50b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477997"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Azure Container Apps プレビューで GitHub Actions を含むリビジョンを発行する

Azure Container Apps を使用すると GitHub Actions を使用して、コンテナー アプリに[リビジョン](revisions.md)を発行できます。 コミットが GitHub リポジトリにプッシュされると、コンテナー レジストリ内の[コンテナー](containers.md) イメージを更新する GitHub アクションがトリガーされます。 レジストリでコンテナーが更新されると、Azure Container Apps によって、更新されたコンテナー イメージに基づいて新しいリビジョンが作成されます。

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="GitHub リポジトリを変更すると、新しいリビジョンを作成するためのアクションがトリガーされます。":::

GitHub アクションは、リポジトリ内の特定のブランチへのコミットによってトリガーされます。 統合リンクを作成するときに、どのブランチがアクションをトリガーするかを決定します。

## <a name="authentication"></a>認証

GitHub Actions の統合を追加または削除する場合は、GitHub [個人用アクセス トークン](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)を渡すか、対話型の GitHub ログイン操作を使用して認証できます。 対話型エクスペリエンスを使用すると、Web ブラウザーでフォームが開き、GitHub にログインできるようになります。 正常に認証されると、現在のセッションの残りの部分に対して GitHub によって使用される CLI にトークンが戻されます。

- 個人用アクセス トークンを渡すには、`--token` パラメーターを使用してトークン値を指定します。
- 対話型ログインを使用する場合は、`--login-with-github` パラメーターに値を指定せずに使用します。

## <a name="add"></a>追加

`containerapp github-action add` コマンドにより、コンテナー アプリとの GitHub Actions の統合が作成されます。

GitHub Actions をコンテナー アプリに初めてアタッチするときは、サービス プリンシパルのコンテキストを指定する必要があります。 次のコマンドは、サービス プリンシパルを作成する方法を示しています。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> `
  --sdk-auth
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

このコマンドからの戻り値は、サービス プリンシパルの `tenantId`、`cliendId`、`clientSecret` を含む JSON ペイロードです。

次の例は、個人用アクセス トークンを使用して統合を追加する方法を示しています。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-user-name <REGISTRY_USER_NAME> \
  --registry-password <REGISTRY_PASSWORD> \
  --service-principal-client-id <CLIENT_ID> \
  --service-principal-client-secret <CLIENT_SECRET> \
  --service-principal-tenant-id <TENANT_ID> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action add `
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" `
  --docker-file-path "./dockerfile" `
  --branch <BRANCH_NAME> `
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-user-name <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

## <a name="show"></a>表示

`containerapp github-action show` コマンドは、コンテナー アプリの GitHub Actions の構成設定を返します。

この例では、個人用アクセス トークンを使用しているときに統合を追加する方法を示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。

このコマンドは、GitHub Actions 統合構成設定を含む JSON ペイロードを返します。

## <a name="delete"></a>削除

`containerapp github-action remove` コマンドは、GitHub Actions をコンテナー アプリから削除します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action remove \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action remove `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

この例を操作するときは、`<>` で囲まれたプレースホルダーを実際の値に置き換えてください。
