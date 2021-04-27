---
title: 前提条件 | 直接接続モード
description: データ コントローラーを直接接続モードでデプロイするための前提条件。
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716295"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>データ コントローラーのデプロイ - 直接接続モード (前提条件)

この記事では、Azure Arc 対応データ サービス用のデータ コントローラーを直接接続モードでデプロイする準備をする方法について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

大まかに言えば、前提条件には以下が含まれます。

1. ツールをインストールする
1. 拡張機能の追加
1. サービス プリンシパルを作成し、メトリックのロールを構成する
1. Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する

これらの前提条件を完了したら、[Azure Arc データ コントローラーを直接接続モードでデプロイする](deploy-data-controller-direct-mode.md)ことができます。

この記事の残りのセクションでは、前提条件を識別します。

## <a name="install-tools"></a>ツールをインストールする

- Helm バージョン 3.3 以降 ([インストール](https://helm.sh/docs/intro/install/))
- Azure CLI ([インストール](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Azure CLI 用の拡張機能を追加する

さらに、次の az 拡張機能も必要です。
- Azure CLI `k8s-extension` 拡張機能 (0.2.0)
- Azure CLI `customlocation` (0.1.0)

サンプル `az` とその CLI 拡張機能は次のようになります。

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>サービス プリンシパルを作成し、メトリックのロールを構成する

[メトリックのアップロード](upload-metrics-and-logs-to-azure-monitor.md)に関する記事に記載されている手順に従ってサービス プリンシパルを作成し、記事の説明に従ってロールを付与します。 

[Azure Arc データ コントローラー](deploy-data-controller-direct-mode.md)をデプロイするときは、SPN ClientID、TenantID、およびクライアント シークレットの情報が必要になります。 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Azure Arc 対応の Kubernetes を使用して、Kubernetes クラスターを Azure に接続する

このタスクを完了するには、[既存の Kubernetes クラスターを Azure Arc に接続する](../kubernetes/quickstart-connect-cluster.md)方法に関するページの手順に従います。

## <a name="next-steps"></a>次のステップ

これらの前提条件を完了したら、[Azure Arc データ コントローラーを直接接続モードでデプロイする](deploy-data-controller-direct-mode.md)ことができます。
