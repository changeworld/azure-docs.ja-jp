---
title: "Azure CLI を使用したコンテナー レジストリの作成 | Microsoft Docs"
description: "Azure CLI 2.0 プレビューを使用した Azure コンテナー レジストリの作成と管理の概要"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: d806564056390ea2db74eebbbe89e1b20a350f33

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Azure CLI を使用したコンテナー レジストリの作成
Linux、Mac、または Windows コンピューターから [Azure CLI 2.0 プレビュー](https://github.com/Azure/azure-cli)のコマンドを使用して、コンテナー レジストリを作成し、その設定を管理します。 コンテナー レジストリの作成と管理は、[Azure Portal](container-registry-get-started-portal.md) を使用するか、Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) を使用してプログラムで行うこともできます。


* 背景情報と概念については、「[Azure Container Registry とは](container-registry-intro.md)」を参照してください。
* Container Registry CLI コマンド (`az acr` コマンド) に関するヘルプについては、任意のコマンドに `-h` のパラメーターを渡します。

> [!NOTE]
> Container Registry は現在プレビュー段階です。
> 
> 

## <a name="prerequisites"></a>前提条件
* **Azure CLI 2.0 プレビュー** - CLI 2.0 プレビューをインストールし、利用を開始するには、[インストール手順](https://github.com/Azure/azure-cli/blob/master/README.rst)を参照してください。 `az login` を実行して Azure サブスクリプションにログインします。
* **リソース グループ** - コンテナー レジストリを作成する前に[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)を作成するか、既存のリソース グループを使用します。 Container Registry サービスが[利用可能](https://azure.microsoft.com/regions/services/)な場所にリソース グループがあることを確認します。 CLI 2.0 プレビューを使用してリソース グループを作成するには、[CLI 2.0 プレビューのサンプル](https://github.com/Azure/azure-cli-samples/tree/master/arm)を参照してください。 
* **ストレージ アカウント** (省略可能) - 同じ場所にコンテナー レジストリをバックアップするには、Standard Azure [ストレージ アカウント](../storage/storage-introduction.md)を作成します。 `az acr create` を使用してレジストリを作成するときにストレージ アカウントを指定しないと、ストレージ アカウントが自動的に作成されます。 CLI 2.0 プレビューを使用してストレージ アカウントを作成するには、[CLI 2.0 プレビューのサンプル](https://github.com/Azure/azure-cli-samples/tree/master/storage)を参照してください。
* **サービス プリンシパル** (省略可能) - CLI を使用してレジストリを作成する場合、既定でアクセス権は設定されません。 必要に応じて、既存の Azure Active Directory サービス プリンシパルをレジストリに割り当てる (または新しく作成して割り当てる) か、レジストリの管理者ユーザー アカウントを有効にします。 この記事の後半のセクションを参照してください。 レジストリ アクセスの詳細については、「[Authenticate with a container registry (コンテナー レジストリによる認証)](container-registry-authentication.md)」を参照してください。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
コンテナー レジストリを作成するには、`az acr create` コマンドを実行します。 

> [!TIP]
> レジストリを作成する場合は、アルファベットと数字のみを含む、グローバルに一意のトップレベル ドメイン名を指定します。 この例のレジストリの名前は `myRegistry` ですが、独自の一意の名前に置き換えてください。 
> 
> 

次のコマンドでは、最小限のパラメーターを使用して、米国中南部の場所のリソース グループ `myResourceGroup` にコンテナー レジストリ `myRegistry` を作成します。

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* `--storage-account-name` または `-s` は省略可能です。 指定しない場合、指定したリソース グループにランダムな名前のストレージ アカウントが作成されます。

次のように出力されます。

![az acr create の出力](./media/container-registry-get-started-azure-cli/acr_create.png)


次の点に特に注意してください。

* `id` - サブスクリプション内のレジストリの識別子。サービス プリンシパルを割り当てる場合に必要です。 
* `loginServer` - [レジストリにログイン](container-registry-authentication.md)するために指定する完全修飾名。 この例では、`myregistry-contoso.exp.azurecr.io` です (すべて小文字)。

## <a name="assign-a-service-principal"></a>サービス プリンシパルの割り当て
CLI 2.0 プレビュー コマンドを使用して、Azure Active Directory サービス プリンシパルをレジストリに割り当てます。 これらの例のサービス プリンシパルでは所有者ロールが割り当てられますが、必要に応じて[他のロール](../active-directory/role-based-access-control-configure.md)を割り当てることができます。

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>サービス プリンシパルを作成し、レジストリにアクセス権を割り当てる
次のコマンドでは、`--scopes` パラメーターで渡したレジストリ ID に新しいサービス プリンシパルで所有者ロールのアクセス権を割り当てます。 `--password` パラメーターで強力なパスワードを指定します。

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>既存のサービス プリンシパルを割り当てる
既存のサービス プリンシパルでレジストリに所有者ロールのアクセス権を割り当てる場合は、次の例のようなコマンドを実行します。 `--assignee` パラメーターを使用して、サービス プリンシパルのアプリ ID を渡します。

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>管理者の資格情報の管理
管理者アカウントは、コンテナー レジストリごとに自動的に作成され、既定で無効になります。 次の例では、コンテナー レジストリの管理者の資格情報を管理する `az acr` CLI コマンドを示しています。

### <a name="obtain-admin-user-credentials"></a>管理者ユーザーの資格情報を取得する
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを有効にする
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを無効にする
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>イメージとタグの一覧表示
`az acr` CLI コマンドを使用して、リポジトリ内のイメージとタグを照会します。 

> [!NOTE]
> 現在、Container Registry では、`docker search` コマンドによるイメージとタグの照会をサポートしていません。


### <a name="list-repositories"></a>リポジトリの一覧を表示する
次の例では、JSON (JavaScript Object Notation) 形式でレジストリ内のリポジトリの一覧を表示します。

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>タグの一覧を表示する
次の例では、JSON 形式で **samples/nginx** リポジトリのタグの一覧を表示します。

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>次のステップ
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)




<!--HONumber=Nov16_HO3-->


