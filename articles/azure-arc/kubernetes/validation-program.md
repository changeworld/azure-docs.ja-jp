---
title: Azure Arc 対応 Kubernetes の検証
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Kubernetes ディストリビューションの Arc 検証プログラムについて説明します
keywords: Kubernetes, Arc, Azure, K8s, 検証
ms.openlocfilehash: 53000b0099ef31bd31346c22d759d45321ae4369
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423890"
---
# <a name="azure-arc-enabled-kubernetes-validation"></a>Azure Arc 対応 Kubernetes の検証

Azure Arc 対応 Kubernetes は、すべての Cloud Native Computing Foundation (CNCF) 認定 Kubernetes クラスターで動作します。 また、Azure Arc チームは業界の主要な Kubernetes オファリング プロバイダーと協力して、それらの Kubernetes ディストリビューションでも Azure Arc 対応 Kubernetes を検証しています。 これらのプロバイダーによって将来リリースされる Kubernetes ディストリビューションのメジャーおよびマイナー バージョンは、Azure Arc 対応 Kubernetes との互換性が検証されることになります。

## <a name="validated-distributions"></a>検証済みのディストリビューション

Microsoft が提供する次の Kubernetes ディストリビューションおよびインフラストラクチャ プロバイダーは、Azure Arc 対応 Kubernetes の準拠テストに合格しています。

| ディストリビューションおよびインフラストラクチャ プロバイダー | Version |
| ---------------------------------------- | ------- |
| Azure のクラスター API プロバイダー            | リリース バージョン: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12)、Kubernetes バージョン: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Azure Stack HCI 上の AKS                   | リリース バージョン: [2020 年 12 月の更新プログラム](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012)、Kubernetes バージョン: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

次のプロバイダーとそれに対応する Kubernetes ディストリビューションは、Azure Arc 対応 Kubernetes の準拠テストに合格しています。

| プロバイダー名 | ディストリビューション名 | Version |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5.41 以降](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)、[4.6.35 以降](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html)、[4.7.18 以降](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid) | TKGm 1.4.0; upstream K8s v1.21.2+vmware.1 <br>TKGm 1.3.1; upstream K8s v1.20.5_vmware.2 <br>TKGm 1.2.1; upstream K8s v1.19.3+vmware.1 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes Engine](https://rancher.com/products/rke/) | RKE CLI バージョン: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4)、Kubernetes バージョン: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6))、[1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14))、[1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | バージョン 2.2.1 |
| Platform9      | [Platform9 Managed Kubernetes (PMK)](https://platform9.com/managed-kubernetes/)    | PMK バージョン [5.3.0](https://platform9.com/docs/kubernetes/release-notes#platform9-managed-kubernetes-version-53-release-notes); Kubernetes バージョン: v 1.20.5、v 1.19.6、v 1.18.10 |
| Cisco | [Intersight Kubernetes Service (IKS)](https://www.cisco.com/c/en/us/products/cloud-systems-management/cloud-operations/intersight-kubernetes-service.html) ディストリビューション | アップストリーム K8s バージョン: 1.19.5 |

Azure Arc チームは、次のパブリック クラウド プロバイダーでも準拠テストを実行し、Azure Arc 対応 Kubernetes シナリオを検証しました。

| パブリック クラウド プロバイダー名 | ディストリビューション名 | Version |
| -------------------------- | ----------------- | ------- |
| アマゾン ウェブ サービス        | Elastic Kubernetes Service (EKS) | v1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v1.17.15 |

## <a name="scenarios-validated"></a>検証されたシナリオ

Azure Arc 対応 Kubernetes 検証の一部として実行される準拠テストは、以下のシナリオをカバーします。

1. Kubernetes クラスターを Azure Arc に接続する: 
    * Azure Arc 対応 Kubernetes エージェント Helm chart をクラスターにデプロイします。
    * クラスターでマネージド システム ID (MSI) 証明書を設定します。
    * エージェントによってクラスターのメタデータが Azure に送信されます。

2. Configuration: 
    * Azure Arc 対応 Kubernetes リソースの上に構成を作成します。
    * GitOps ワークフローの設定に必要な [Flux](https://docs.fluxcd.io/) がクラスターにデプロイされます。
    * Flux によって、デモ用の Git リポジトリから Helm Chart とマニフェストがプルされ、クラスターにデプロイされます。

## <a name="next-steps"></a>次のステップ

クラスターを Azure Arc に接続する方法について学習します。
> [!div class="nextstepaction"]
> [Azure Arc にクラスターを接続する](./quickstart-connect-cluster.md)
