---
title: Azure Cloud Shell を使用して Azure Dev Spaces に対応する Kubernetes クラスターを作成する方法 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: article
description: 何もインストールせずにブラウザーから直接 Azure Dev Spaces に対応する Kubernetes クラスターをすばやく作成する方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 531781ec48617d2038698665696fdc61b3c52cd9
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413542"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Azure Cloud Shell を使用して Kubernetes クラスターを作成する

このページの **[使ってみる]** ボタンを使用すると、[Azure Cloud Shell](/azure/cloud-shell) を使用して Azure Dev Spaces 用のクラスターを作成できます。 サインインしていない場合は、プロンプトに従って Azure アカウントを使用してサインインした後、表示された Azure Cloud Shell プロンプトでコマンドを入力します。

## <a name="create-the-cluster"></a>クラスターを作成する

まず、リソース グループを作成します。 現在サポートされているリージョン (EastUS、EastUS2、CentralUS、WestUS2、WestEurope、SoutheastAsia、CanadaCentral、または CanadaEast) のいずれかを使用します。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

以下のコマンドを使用して Kubernetes クラスターを作成します。

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing
```

クラスターの作成には数分かかります。  完了すると、JSON 形式で出力が表示されます。 `provisioningState` を見つけ、それが `Succeeded` であることを確認します。

## <a name="next-steps"></a>次の手順

各種のチュートリアルへのリンクについては、「[Azure Dev Spaces](/azure/dev-spaces/)」を参照してください。
