---
title: Azure Cosmos DB Automation - Azure CLI 2.0 | Microsoft Docs
description: "Azure CLI 2.0 を使用して、Azure Cosmos DB アカウントを作成および管理します。 Azure Cosmos DB は、可用性の高いグローバル分散データベースです。"
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Azure CLI を使用して Azure Cosmos DB アカウントを作成する

以下のガイドでは、Azure CLI 2.0 で提供されるプレビュー コマンドを使用して、Azure Cosmos DB データベース アカウントの管理を自動化するためのコマンドについて説明します。 また、[複数リージョンのデータベース アカウント][scaling-globally]でアカウント キーとフェールオーバー優先度を管理するコマンドについても説明します。 データベース アカウントを更新すると、一貫性ポリシーの変更と、リージョンの追加または削除を行うことができます。 Azure Cosmos DB データベース アカウントのクロスプラットフォーム管理には、[Azure PowerShell](documentdb-manage-account-with-powershell.md)、[リソースプロバイダー REST API][rp-rest-api]、または [Azure Portal](documentdb-create-account.md) を使用できます。

## <a name="getting-started"></a>使用の開始

[Azure CLI 2.0 のインストールと構成方法][install-az-cli2]に関するページの手順に従って、Azure CLI 2.0 で開発環境をセットアップしてください。

次のコマンドを実行して画面上の手順に従い、Azure アカウントにログインします。

```azurecli
az login
```

[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)が作成されていない場合は、リソース グループを作成します。

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

`<resourcegrouplocation>` は、Azure Cosmos DB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

### <a name="notes"></a>メモ

* 使用できるすべてのコマンドの一覧を取得するには、'az documentdb -h' を実行するか、[リファレンス ページ][az-documentdb-ref]をご覧ください。
* 各コマンドの必須パラメーターと省略可能なパラメーターの詳細の一覧を取得するには、"az documentdb &lt;command&gt; -h" を実行します。

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>Azure Cosmos DB を使用するサブスクリプションを登録する

このコマンドでは、CLI を使用して Azure Cosmos DB を使用するサブスクリプションを登録します。

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a>Azure Cosmos DB データベース アカウントを作成する

このコマンドでは、Azure Cosmos DB データベース アカウントを作成できます。 新しいデータベース アカウントは、単一リージョンまたは[複数リージョン][scaling-globally]で、特定の[一貫性ポリシー](documentdb-consistency-levels.md)に基づいて構成します。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

次に例を示します。 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>メモ 
* 場所は、Azure Cosmos DB が一般公開されているリージョンである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。
* ポータルのアクセスを有効にするには、「[IP アクセス制御ポリシーの構成](documentdb-firewall-support.md#configure-ip-policy)」を参照して、お使いのリージョンの Azure Portal の IP アドレスを ip-range-filter に含めます。

## <a id="update-documentdb-account-cli"></a>Azure Cosmos DB データベース アカウントを更新する

このコマンドでは、Azure Cosmos DB データベース アカウントのプロパティを更新できます。 これには、一貫性ポリシーと、データベース アカウントが存在する場所が含まれます。

> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更することはできません。 フェールオーバー優先度の変更方法については、[こちら](#modify-failover-priority-powershell)を参照してください。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

次に例を示します。 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a>Azure Cosmos DB データベース アカウントに対してリージョンを追加または削除する

既存の Azure Cosmos DB データベース アカウントに対してリージョンを追加または削除するには、[update](#update-documentdb-account-cli) コマンドと `--locations` フラグを使用します。 次の例では、新しいアカウントを作成し、続いてそのアカウントにリージョンを追加したり、アカウントからリージョンを削除したりする方法を説明します。

例:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a>Azure Cosmos DB データベース アカウントを削除する

このコマンドでは、既存の Azure Cosmos DB データベース アカウントを削除できます。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a>Azure Cosmos DB データベース アカウントのプロパティを取得する

このコマンドでは、既存の Azure Cosmos DB データベース アカウントのプロパティを取得できます。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> アカウント キーの一覧表示

Azure Cosmos DB アカウントを作成すると、2 つのマスター アクセス キーが生成されます。これらのアクセス キーは、Azure Cosmos DB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、Azure Cosmos DB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> 接続文字列の一覧表示

MongoDB アカウントの場合、MongoDB アプリをデータベース アカウントに接続する接続文字列は、次のコマンドで取得できます。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> アカウント キーの再生成

接続のセキュリティを高めるために、Azure Cosmos DB アカウントのアクセス キーは定期的に変更する必要があります。 一方のアクセス キーを使用して Azure Cosmos DB アカウントへの接続を維持しながら、もう一方のアクセス キーを再生成できるように、2 つのアクセス キーが割り当てられます。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

例:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a>Azure Cosmos DB データベース アカウントのフェールオーバー優先度を変更する

複数リージョンのデータベース アカウントでは、Azure Cosmos DB データベース アカウントが存在するさまざまなリージョンのフェールオーバー優先度を変更できます。 Azure Cosmos DB データベース アカウントでのフェールオーバーの詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)に関する記事をご覧ください。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

例:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>次のステップ

* .NET を使用して接続する方法については、[.NET を使った接続とクエリ](../cosmos-db/create-documentdb-dotnet.md)に関する記事をご覧ください。
* .NET Core を使用して接続する方法については、[.NET Core を使った接続とクエリ](../cosmos-db/create-documentdb-dotnet-core.md)に関する記事をご覧ください。
* Node.js を使用して接続する方法については、[Node.js と MongoDB アプリを使った接続とクエリ](../cosmos-db/create-mongodb-nodejs.md)に関する記事をご覧ください。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

