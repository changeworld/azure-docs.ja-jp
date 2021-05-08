---
title: Azure CLI を使用して Azure Content Delivery Network (CDN) のプロファイルとエンドポイントを作成する
description: Azure CDN のプロファイル、エンドポイント、配信元グループ、配信元、およびカスタム ドメインを作成するための Azure CLI サンプル スクリプト。
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723494"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Azure CLI を使用して Azure CDN のプロファイルとエンドポイントを作成する

Azure portal の代わりに、これらのサンプル Azure CLI スクリプトを使用して、次の CDN 操作を管理できます。

- CDN プロファイルを作成する。
- CDN エンドポイントを作成する。
- CDN 配信元グループを作成し、それを既定のグループにする。
- CDN 配信元を作成する。
- カスタム ドメインを作成し、HTTPS を有効にする。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>サンプルのスクリプト

CDN プロファイルのリソース グループがまだない場合は、コマンド `az group create` を実行してこれを作成します。

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

次の Azure CLI スクリプトでは、CDN プロファイルと CDN エンドポイントを作成します。

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

次の Azure CLI スクリプトでは、CDN 配信元グループを作成し、エンドポイントの既定の配信元グループを設定して、新しい配信元を作成します。

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

次の Azure CLI スクリプトでは、CDN カスタム ドメインを作成し、HTTPS を有効にします。 カスタム ドメインを Azure CDN エンドポイントと関連付けるためには、まず Azure DNS または DNS プロバイダーで正規名 (CNAME) レコードを作成し、CDN エンドポイントを指すようにする必要があります。 詳細については、「[CNAME DNS レコードを作成する](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)」を参照してください。

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

サンプル スクリプトの実行が完了したら、次のコマンドを使用して、リソース グループとそれに関連付けられているすべてのリソースを削除します。

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>この記事で使用されている Azure CLI コマンド

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
