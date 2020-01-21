---
title: Azure のサーバーレス コンテナー
description: Azure Container Instances サービスには、仮想マシンを管理したり、より高度なオーケストレーターを採用したりせずに、Azure で最も高速かつ簡単に別個のコンテナーを実行する方法が用意されています。
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888035"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances とは

コンテナーは、クラウド アプリケーションのパッケージ化、デプロイ、管理を行う優れた方法としてしだいに普及しつつあります。 Azure Container Instances には、仮想マシンを管理したり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。

Azure Container Instances は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 複数コンテナー間でのサービスの検出、自動スケーリング、およびアプリケーションの調整されたアップグレードなど、コンテナーの完全なオーケストレーションが必要なシナリオには、[Azure Kubernetes Service (AKS)](../aks/index.yml) をお勧めします。

## <a name="fast-startup-times"></a>高速なスタートアップ時間

コンテナーは、スタートアップにおいて、仮想マシン (VM) よりもはるかに優れています。 Azure Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。

## <a name="container-access"></a>コンテナー アクセス

Azure Container Instances を使用すると、IP アドレスと完全修飾ドメイン名 (FQDN) を使用してコンテナー グループをインターネットに直接公開できます。 コンテナー インスタンスを作成するとき、カスタム DNS 名ラベルを指定できるので、*customlabel*.*azureregion*.azurecontainer.io でアプリケーションに到達できます。

Azure Container Instances では、アプリケーションの開発とトラブルシューティングを支援するために、対話型シェルを提供することで、実行中のコンテナーでのコマンドの実行もサポートしています。 アクセスは HTTPS 経由で行われ、TLS を使用してクライアント接続がセキュリティで保護されます。

> [!IMPORTANT]
> 2020 年 1 月 13 日以降、Azure Container Instances では、サーバーとアプリケーションからのセキュリティで保護されたすべての接続で TLS 1.2 を使用する必要があります。 TLS 1.0 と 1.1 のサポートは、廃止される予定です。

## <a name="hypervisor-level-security"></a>ハイパーバイザーレベルのセキュリティ

従来、コンテナーは、アプリケーション依存関係の分離とリソース ガバナンスを実現してきましたが、悪意のあるマルチテナント使用に対する十分なセキュリティ強化については考慮されていません。 Azure Container Instances を使用すると、アプリケーションは、VM 内であるかのように、コンテナー内で確実に分離されます。


## <a name="custom-sizes"></a>カスタム サイズ

コンテナーは、通常、1 つのアプリケーションだけを実行するために最適化されますが、そのようなアプリケーションの正確なニーズはさまざまに異なることがあります。 Azure Container Instances で、CPU のコア数とメモリを厳密に指定することによって最適な稼働率を確保することが可能です。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。

機械学習などの計算集中型ジョブの場合、Azure Container Instances では、NVIDIA Tesla [GPU リソース](container-instances-gpu.md) (プレビュー) を使用するように Linux コンテナーをスケジュールできます。

## <a name="persistent-storage"></a>永続的ストレージ

Azure Container Instances を使用して状態を取得および保持できるように、Azure Storage によってサポートされる [Azure Files 共有の直接マウント](container-instances-mounting-azure-files-volume.md)が提供されます。

## <a name="linux-and-windows-containers"></a>Linux コンテナーと Windows コンテナー

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 [コンテナー グループ](container-instances-container-groups.md)を作成するときに、OS の種類を指定するだけです。

一部の機能は、現在のところ Linux コンテナーに限定されています。

* コンテナー グループあたりの複数のコンテナー
* ボリューム マウント ([Azure Files](container-instances-volume-azure-files.md)、[emptyDir](container-instances-volume-emptydir.md)、[GitRepo](container-instances-volume-gitrepo.md)、[シークレット](container-instances-volume-secret.md))
* [リソース使用量メトリック](container-instances-monitor.md)と Azure Monitor
* [仮想ネットワークのデプロイ](container-instances-vnet.md)
* [GPU リソース](container-instances-gpu.md) (プレビュー)

Windows コンテナーのデプロイでは、一般的な [Windows ベースのイメージ](container-instances-faq.md#what-windows-base-os-images-are-supported)に基づくイメージを使用します。

> [!NOTE]
> Azure Container Instances での Windows Server 2019 ベースのイメージの使用は、プレビュー段階です。

## <a name="co-scheduled-groups"></a>共同スケジュール グループ

Azure Container Instances は、ホスト コンピューター、ローカル ネットワーク、ストレージ、およびライフサイクルを共有する[複数コンテナー グループ](container-instances-container-groups.md)のスケジュール設定をサポートします。 これにより、メイン アプリケーション コンテナーを、サイドカーのように主要な機能と併走しながら補助的な役割をつかさどるコンテナー (ログ記録など) と結合することができます。

## <a name="virtual-network-deployment"></a>仮想ネットワークのデプロイ

Azure Container Instances のこの機能を使用すると、[Azure 仮想ネットワークにコンテナー インスタンスをデプロイ](container-instances-vnet.md)できます。現在、Azure リージョンのサブセットの運用ワークロードで使用できます。 コンテナー インスタンスを仮想ネットワーク内のサブネットにデプロイすることで、オンプレミス上のリソースとの ([VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) 経由での) 通信も含め、仮想ネットワークに存在する他のリソースとの通信を安全に行うことができます。

## <a name="next-steps"></a>次のステップ

クイックスタート ガイドを使用して、1 つのコマンドでコンテナーを Azure にデプロイしてみてください。

> [!div class="nextstepaction"]
> [Azure Container Instances のクイックスタート](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
