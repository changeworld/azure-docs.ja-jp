---
title: Azure Container Instances の概要
description: Azure Container Instances について
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 10/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 33d6d89e91ecdec00c1b17ecddf91128e9d07526
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802102"
---
# <a name="azure-container-instances"></a>Azure Container Instances

コンテナーは、クラウド アプリケーションのパッケージ化、デプロイ、管理を行う優れた方法としてしだいに普及しつつあります。 Azure Container Instances には、仮想マシンを管理したり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。

Azure Container Instances は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 複数コンテナー間でのサービスの検出、自動スケーリング、およびアプリケーションの調整されたアップグレードなど、コンテナーの完全なオーケストレーションが必要なシナリオには、[Azure Kubernetes Service (AKS)](../aks/index.yml) をお勧めします。

## <a name="fast-startup-times"></a>高速なスタートアップ時間

コンテナーは、スタートアップにおいて、仮想マシン (VM) よりもはるかに優れています。 Azure Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。

## <a name="public-ip-connectivity-and-dns-name"></a>パブリック IP 接続と DNS 名

Azure Container Instances を使用すると、IP アドレスと完全修飾ドメイン名 (FQDN) を使ってコンテナーをインターネットに直接公開できます。 コンテナー インスタンスを作成するとき、カスタム DNS 名ラベルを指定できるので、*customlabel*.*azureregion*.azurecontainer.io でアプリケーションに到達できます。

## <a name="hypervisor-level-security"></a>ハイパーバイザーレベルのセキュリティ

従来、コンテナーは、アプリケーション依存関係の分離とリソース ガバナンスを実現してきましたが、悪意のあるマルチテナント使用に対する十分なセキュリティ強化については考慮されていません。 Azure Container Instances を使用すると、アプリケーションは、VM 内であるかのように、コンテナー内で確実に分離されます。

## <a name="custom-sizes"></a>カスタム サイズ

コンテナーは、通常、1 つのアプリケーションだけを実行するために最適化されますが、そのようなアプリケーションの正確なニーズはさまざまに異なることがあります。 Azure Container Instances で、CPU のコア数とメモリを厳密に指定することによって最適な稼働率を確保することが可能です。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。

## <a name="persistent-storage"></a>永続的ストレージ

Azure Container Instances を使用して状態を取得および保持できるように、Microsoft では [Azure Files 共有を直接マウント](container-instances-mounting-azure-files-volume.md)しています。

## <a name="linux-and-windows-containers"></a>Linux コンテナーと Windows コンテナー

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 [コンテナー グループ](container-instances-container-groups.md)を作成するときに、OS の種類を指定するだけです。

一部の機能は、現在のところ Linux コンテナーに限定されています。 Windows コンテナーに対する機能パリティ実現に向けて取り組んでいますが、現時点では、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) に記載されているような違いがプラットフォーム間には存在します。

Azure Container Instances では、長期的なサービス チャネル (LTSC) のバージョンに基づいて Windows イメージをサポートしています。 1709 や 1803 のような Windows 半期チャネル (SAC) のリリースは、サポートされていません。

## <a name="co-scheduled-groups"></a>共同スケジュール グループ

Azure Container Instances は、ホスト コンピューター、ローカル ネットワーク、ストレージ、およびライフサイクルを共有する[複数コンテナー グループ](container-instances-container-groups.md)のスケジュール設定をサポートします。 これにより、メイン アプリケーション コンテナーを、サイドカーのように主要な機能と併走しながら補助的な役割をつかさどるコンテナー (ログ記録など) と結合することができます。

## <a name="virtual-network-deployment-preview"></a>仮想ネットワークのデプロイ (プレビュー)

これは [Azure 仮想ネットワークにコンテナー インスタンスをデプロイ](container-instances-vnet.md)する機能です。Azure Container Instances の機能であり、現在はプレビュー段階となっています。 コンテナー インスタンスを仮想ネットワーク内のサブネットにデプロイすることで、オンプレミス上のリソースとの ([VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) 経由での) 通信も含め、仮想ネットワークに存在する他のリソースとの通信を安全に行うことができます。

> [!IMPORTANT]
> 仮想ネットワークに対するコンテナー グループのデプロイは現在プレビュー段階であり、いくつかの[制限が適用](container-instances-vnet.md#preview-limitations)されます。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="next-steps"></a>次の手順

クイックスタート ガイドを使用して、1 つのコマンドでコンテナーを Azure にデプロイしてみてください。

> [!div class="nextstepaction"]
> [Azure Container Instances のクイックスタート](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
