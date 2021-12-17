---
title: カスタム構成テンプレートの作成
description: カスタム構成テンプレートの作成
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737187"
---
# <a name="create-custom-configuration-templates"></a>カスタム構成テンプレートの作成

この記事では、Azure Arc 対応データ コントローラーのカスタム構成テンプレートを作成する方法について説明します。 

間接的に接続されたモードでデータ コントローラーをデプロイする際に必要なパラメーターの 1 つに、`az arcdata dc create --profile-name` パラメーターがあります。 現時点では、次のクエリを実行すると、使用可能な組み込みプロファイルの一覧が表示されます。

```azurecli
az arcdata dc config list
```
これらのプロファイルは、Azure Arc 対応データ コントローラーのさまざまな設定 (Docker のレジストリとリポジトリの設定、データとログ用のストレージのクラス、データとログ用のストレージのサイズ、セキュリティ、サービスの種類など) を含むテンプレート JSON ファイルです。これは、ご利用の環境に合わせてカスタマイズすることもできます。 

ただし、場合によっては、要件に合わせてこれらの構成テンプレートをカスタマイズし、`--path` パラメーターを使用して、カスタマイズした構成テンプレートを `az arcdata dc create` コマンドに渡す必要があります (`--profile-name` パラメーターを使用して、構成済みの構成テンプレートを渡すのではなく)。

## <a name="create-customjson-file"></a>control.json ファイルを作成する

`az arcdata dc config init` を実行すると、Kubernetes クラスターのご利用のディストリビューションに基づいて事前に定義された設定で control.json ファイルが開始されます。
たとえば、現在の作業ディレクトリ内の `custom` という名前のサブディレクトリにある `azure-arc-kubeadm` テンプレートに基づいた、Kubernetes クラスター用のテンプレート control.json ファイルは、次のように作成できます。

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
作成された control.json ファイルを Visual Studio Code などの任意のエディターで編集すれば、ご利用の環境に合わせて設定をカスタマイズできます。

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>Azure CLI (az) で Azure Arc 対応データ コントローラーをデプロイするためのカスタム control.json ファイルを使用する

テンプレート ファイルが作成されたら、Azure Arc 対応データ コントローラーの create コマンドの実行中にそのファイルを次のように適用できます。

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>Azure portal で Azure Arc データ コントローラーをデプロイするためのカスタム control.json ファイルを使用する

Azure Arc データ コントローラーの create 画面で、[カスタム テンプレート] の下にある "カスタム テンプレートの構成" を選択します。 これにより、ブレードが呼び出され、カスタム設定が提供されます。 このブレードでは、さまざまな設定に値を入力することも、事前に構成された control.json ファイルを直接アップロードすることもできます。 

値が正しいことを確認できたら、[適用] をクリックして Azure Arc データ コントローラーのデプロイを続行します。

## <a name="next-steps"></a>次のステップ

[データ コントローラーのデプロイ - 直接接続モード (前提条件)](create-data-controller-direct-prerequisites.md)

[Azure Arc データ コントローラー (CLI) を作成する](create-data-controller-direct-cli.md)
