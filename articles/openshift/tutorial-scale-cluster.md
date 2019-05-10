---
title: チュートリアル - Azure Red Hat OpenShift クラスターをスケーリングする | Microsoft Docs
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターをスケーリングする方法を学習します
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: f86b60fbe20fc630863d6e177d45f2c1f06a7863
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079397"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>チュートリアル:Azure Red Hat OpenShift クラスターをスケーリングする

このチュートリアルは、シリーズの第 2 部です。 Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成し、スケーリングしてから、削除してリソースをクリーンアップする方法について説明します。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Red Hat OpenShift クラスターをスケーリングする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)
> * Azure Red Hat OpenShift クラスターをスケーリングする
> * [Azure Red Hat OpenShift クラスターを削除する](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* チュートリアルの「[Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)」に従ってクラスターを作成します。

## <a name="step-1-sign-in-to-azure"></a>手順 1:Azure へのサインイン

Azure CLI をローカルで実行している場合は、`az login` を実行して Azure にサインインします。

```bash
az login
```

複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>手順 2:追加のノードによるクラスターのスケーリング

Bash ターミナルで、変数 CLUSTER_NAME にクラスターの名前を設定します。

```bash
CLUSTER_NAME=yourclustername
```

Azure CLI を使用して、クラスターを 5 ノードにスケーリングしましょう。

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

数分後に `az openshift scale` は正常に完了し、スケーリングされたクラスターの詳細を含む JSON ドキュメントが返されます。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターをスケーリングする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを削除する](tutorial-delete-cluster.md)