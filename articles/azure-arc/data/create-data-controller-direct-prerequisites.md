---
title: 前提条件 | 直接接続モード
description: データ コントローラーを直接接続モードでデプロイするための前提条件。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 143a601f89301d3ed4302efa6e978cba53539e2c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734074"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>データ コントローラーを直接接続モードでデプロイするための前提条件

この記事では、Azure Arc 対応データ サービス用のデータ コントローラーを直接接続モードでデプロイする準備をする方法について説明します。 Azure Arc データ コントローラーをデプロイするには、[Azure Arc 対応のデータ サービスのデプロイ計画](plan-azure-arc-data-services.md)に関するページで説明されている追加の理解と概念が必要です。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Azure Arc データ コントローラーを **直接** 接続モードで作成する前提条件は、大まかには次のとおりです。

1. Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する
2. サービス プリンシパルを作成し、メトリックのロールを構成する
3. Azure Arc 対応のデータ サービス データ コントローラーを作成する。 この手順では、次を作成する必要がある。
    - Azure Arc データ サービス拡張機能
    - カスタムの場所
    - Azure Arc データ コントローラー

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する

お使いの Kubernetes クラスターを Azure に接続するには、次の拡張機能と Helm を使用して ```az``` CLI を使用します。

#### <a name="install-tools"></a>ツールをインストールする

- Helm バージョン 3.3 以降 ([インストール](https://helm.sh/docs/intro/install/))
- 最新バージョンの Azure CLI をインストールするか、アップグレードする ([インストール](/sql/azdata/install/deploy-install-azdata))

#### <a name="add-extensions-for-azure-cli"></a>Azure CLI 用の拡張機能を追加する

次の az の拡張機能の最新バージョンをインストールします。
- ```k8s-extension```
- ```connectedk8s```
- ```k8s-configuration```
- `customlocation`

次のコマンドを実行し、az CLI の拡張機能をインストールします。

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

```k8s-extension```、```connectedk8s```、```k8s-configuration```、`customlocation` の拡張機能を以前にインストールしている場合は、次のコマンドを使用して最新バージョンに更新します。

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```
#### <a name="connect-your-cluster-to-azure"></a>お使いのクラスターを Azure に接続する

このタスクを完了するには、[既存の Kubernetes クラスターを Azure Arc に接続する](../kubernetes/quickstart-connect-cluster.md)方法に関するページの手順に従います。

お使いのクラスターを Azure に接続したら、次はサービス プリンシパルを作成します。 

## <a name="2-create-service-principal-and-configure-roles-for-metrics"></a>2. サービス プリンシパルを作成し、メトリックのロールを構成する

[メトリックのアップロード](upload-metrics-and-logs-to-azure-monitor.md)に関する記事に記載されている手順に従ってサービス プリンシパルを作成し、記事の説明に従ってロールを付与します。 

[Azure Arc データ コントローラー](create-data-controller-direct-azure-portal.md)をデプロイするときは、SPN ClientID、TenantID、およびクライアント シークレットの情報が必要になります。 

## <a name="3-create-azure-arc-data-services"></a>3. Azure Arc データ サービスを作成する

これらの前提条件を完了したら、[Azure Arc データ コントローラーを直接接続モードでデプロイする](create-data-controller-direct-azure-portal.md)ことができます。


