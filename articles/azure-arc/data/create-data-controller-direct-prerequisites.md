---
title: 前提条件 | 直接接続モード
description: データ コントローラーを直接接続モードでデプロイするための前提条件。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: cd1c12eaf4c4d5df575c7774386da007c9124712
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549833"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>データ コントローラーを直接接続モードでデプロイするための前提条件

この記事では、Azure Arc 対応データ サービス用のデータ コントローラーを直接接続モードでデプロイする準備をする方法について説明します。 Azure Arc データ コントローラーをデプロイする前に、[Azure Arc 対応データ サービスのデプロイの計画](plan-azure-arc-data-services.md)に関する記事で説明されている概念を理解してください。

Azure Arc データ コントローラーを **直接** 接続モードで作成する前提条件は、大まかには次のとおりです。

1. Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する
2. Azure Arc 対応のデータ サービス データ コントローラーを作成する。 この手順では、次を作成する必要がある。
    - Azure Arc データ サービス拡張機能
    - カスタムの場所
    - Azure Arc データ コントローラー
3. Azure Log Analytics へのログの自動アップロードが必要な場合は、Log Analytics ワークスペース ID と共有アクセス キーがデプロイの一環として必要になります。

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する

Kubernetes クラスターを Azure に接続するには、Azure CLI の `az` を次の拡張機能または Helm で使用します。

### <a name="install-tools"></a>ツールをインストールする

- Helm バージョン 3.3 以降 ([インストール](https://helm.sh/docs/intro/install/))
- 最新バージョンの Azure CLI をインストールするか、アップグレードする ([ダウンロード](https://aka.ms/installazurecliwindows))

### <a name="add-extensions-for-azure-cli"></a>Azure CLI 用の拡張機能を追加する

次の az の拡張機能の最新バージョンをインストールします。
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

次のコマンドを実行し、az CLI の拡張機能をインストールします。

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

`k8s-extension`、`connectedk8s`、`k8s-configuration`、`customlocation` の拡張機能を以前にインストールしている場合は、次のコマンドを使用して最新バージョンに更新します。

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>お使いのクラスターを Azure に接続する

このタスクを完了するには、[既存の Kubernetes クラスターを Azure Arc に接続する](../kubernetes/quickstart-connect-cluster.md)方法に関するページの手順に従います。

## <a name="2-optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>2. 必要に応じて、Log Analytics ワークスペース ID と共有アクセス キーを準備しておく

Azure Arc 対応データ コントローラーをデプロイする場合、セットアップ時にメトリックとログの自動アップロードを有効にすることができます。 メトリックのアップロードでは、システム割り当てマネージド ID が使用されます。 ただし、ログをアップロードする場合は、ワークスペース ID とワークスペースのアクセス キーが必要です。 

また、データ コントローラーをデプロイした後にメトリックとログの自動アップロードを有効または無効にすることもできます。 

## <a name="3-create-azure-arc-data-services"></a>3. Azure Arc データ サービスを作成する

これらの前提条件を完了したら、[Azure Arc データ コントローラーを直接接続モードでデプロイする](create-data-controller-direct-azure-portal.md)ことができます。
