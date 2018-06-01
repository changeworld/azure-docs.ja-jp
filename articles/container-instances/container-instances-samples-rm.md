---
title: Azure Resource Manager テンプレートのサンプル - Azure Container Instances
description: Azure Container Instances のための Azure Resource Manager テンプレートのサンプル
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259618"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances のための Azure Resource Manager テンプレート

次のサンプル テンプレートは、さまざまな構成のコンテナー インスタンスを展開します。

展開オプションについては、[展開](#deployment)セクションを参照してください。 独自のテンプレートを作成する場合は、Azure Container Instances の [Azure Resource Manager テンプレート リファレンス][ref]にテンプレートの形式と使用可能なプロパティが詳しく説明されています。

## <a name="sample-templates"></a>サンプル テンプレート

| | |
|-|-|
| **アプリケーション** ||
| [Wordpress][app-wp] | コンテナー インスタンスに WordPress Web サイトと、MySQL データベースを作成します。 WordPress サイトのコンテンツと MySQL データベースは、Azure Files 共有で永続化されます。 |
| [SQL Server と IIS を使用する MS NAV][app-nav] | 全機能を備えた自己完結型 Dynamics NAV / Dynamics 365 Business Central 環境に単一の Windows コンテナーを展開します。 |
| **Volumes** ||
| [emptyDir][vol-emptydir] | emptyDir ボリュームを共有する 2 つの Linux コンテナーを展開します。 |
| [gitRepo][vol-gitrepo] | GitHub リポジトリのクローンを作成し、ボリュームとしてマウントする Linux コンテナーを展開します。 |
| [secret][vol-secret] | シークレット ボリュームとしてマウントされている PFX 証明書を使用して Linux コンテナーを配置します。 |
| **ネットワーク** ||
| [UDP 公開されたコンテナー][net-udp] | UDP ポートを公開する、Windows または Linux のコンテナーを展開します。 |
| [パブリック IP を使用する Linux コンテナー][net-publicip] | パブリック IP を使用してアクセスできる 1 つの Linux コンテナーを展開します。 |
| **Azure リソース** ||
| [Azure Storage アカウントおよび Azure Files 共有を作成する][az-files] | コンテナー インスタンスで Azure CLI を使用して、ストレージ アカウントと Azure Files 共有します。

## <a name="deployment"></a>デプロイ

Resource Manager テンプレートを使用してリソースを配置するには、いくつかのオプションがあります。

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups