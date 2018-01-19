---
title: "Azure Container Instances の概要"
description: "Azure Container Instances について"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 01e539856adbdcf02dc4e49087a3ab71b328db5a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

コンテナーは、クラウド アプリケーションのパッケージ化、デプロイ、および管理を行う方法として急速に選ばれるようになりました。 Azure Container Instances には、仮想マシンをプロビジョニングしたり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。

Azure Container Instances は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 複数コンテナー間でのサービスの検出、自動スケーリング、およびアプリケーションの調整されたアップグレードなど、コンテナーの完全なオーケストレーションが必要なシナリオには、[Azure Container Service (AKS)](../aks/index.yml) をお勧めします。

## <a name="fast-startup-times"></a>高速なスタートアップ時間

コンテナーは、スタートアップにおいて、仮想マシンよりもはるかに優れています。 Azure Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。

## <a name="hypervisor-level-security"></a>ハイパーバイザーレベルのセキュリティ

従来、コンテナーは、アプリケーション依存関係の分離とリソース ガバナンスを実現してきましたが、悪意のあるマルチテナント使用に対する十分なセキュリティ強化については考慮されていません。 Azure Container Instances を使用すると、アプリケーションは、VM 内であるかのように、コンテナー内で分離されます。

## <a name="custom-sizes"></a>カスタム サイズ

コンテナーは、通常、1 つのアプリケーションだけを実行するために最適化されますが、そのようなアプリケーションの正確なニーズはさまざまに異なることがあります。 Azure Container Instances を使用すると、CPU コアとメモリの観点から必要なものを正確に要求できます。 その要求内容に基づいて秒単位の課金に対して支払うので、ニーズに基づいて支出をきめ細やかに最適化できます。

## <a name="public-ip-connectivity"></a>パブリック IP 接続

Azure Container Instances を使用すると、パブリック IP アドレスを使ってコンテナーをインターネットに直接公開できます。 今後、Microsoft では、仮想ネットワークやロード バランサー、Azure ネットワーク インフラストラクチャのその他の中核的な部分との統合が含まれるようにネットワーク機能を拡張する予定です。

## <a name="persistent-storage"></a>永続的ストレージ

Azure Container Instances を使用して状態を取得および保持できるように、Microsoft では [Azure Files 共有を直接マウント](container-instances-mounting-azure-files-volume.md)しています。

## <a name="linux-and-windows-containers"></a>Linux コンテナーと Windows コンテナー

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 [コンテナー グループ](container-instances-container-groups.md)を作成するときに、OS の種類を指定するだけです。

一部の機能は、現在のところ Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Azure Container Instances のクォータとリージョンの可用性](container-instances-quotas.md)」で確認できます。

## <a name="co-scheduled-groups"></a>共同スケジュール グループ

Azure Container Instances は、ホスト コンピューター、ローカル ネットワーク、ストレージ、およびライフサイクルを共有する[複数コンテナー グループ](container-instances-container-groups.md)のスケジュール設定をサポートします。 これにより、メイン アプリケーションを、脇役として機能する他のアプリケーション (ログ記録など) と結合することができます。

## <a name="next-steps"></a>次の手順

[クイックスタート ガイド](container-instances-quickstart.md)を使用して、1 つのコマンドでコンテナーを Azure にデプロイしてみてください。