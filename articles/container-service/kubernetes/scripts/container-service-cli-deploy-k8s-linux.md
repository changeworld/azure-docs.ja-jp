---
title: Azure CLI のサンプル スクリプト - ACS Linux Kubernetes クラスターの作成
description: Azure CLI のサンプル スクリプト - ACS Linux Kubernetes クラスターの作成
author: iainfoulds
tags: acs, azure-container-service
keywords: Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 7b5c6d5931b5d36069850b1bba90413731a48022
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270691"
---
# <a name="deprecated-create-an-azure-container-service-kubernetes-linux-cluster"></a>(非推奨) Kubernetes for Linux を実行する Azure Container Service クラスターを作成する

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

このサンプルでは、Kubernetes for Linux ベースのコンテナーを実行する Azure Container Service クラスターが作成されます。

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | ACS クラスターが作成されます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Container Service の CLI サンプル スクリプトは、[Azure Container Service のドキュメント](../cli-samples.md)のページにあります。

