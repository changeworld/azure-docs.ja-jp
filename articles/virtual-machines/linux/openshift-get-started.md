---
title: "OpenShift on Azure の概要 | Microsoft Docs"
description: "OpenShift on Azure の概要。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>OpenShift の概要

OpenShift は、エンタープライズに docker と Kubernetes を導入する、オープンで拡張可能なコンテナー アプリケーション プラットフォームです。  

OpenShift には、コンテナーのオーケストレーションと管理のための Kubernetes が含まれています。 これにより、開発者と操作を中心に据えたツールが追加され、次のことが可能になります。

- 迅速なアプリケーション開発
- 容易なデプロイとスケール変更
- チームおよびアプリケーション向けの長期的なライフサイクル メンテナンス

OpenShift には複数のオファリングがあり、そのうちの 2 つが Azure で実行可能です。

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

提供されている 4 つのオファリングのうち 2 つ (OpenShift Origin と OpenShift Container Platform) を、顧客が自力で Azure にデプロイできます。

## <a name="openshift-origin"></a>OpenShift Origin

OpenShift の[オープン ソース](https://www.openshift.org/)上流プロジェクトであり、コミュニティによりサポートされています。 Origin は、CentOS または RHEL にインストールすることができます。

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Red Hat から提供されているエンタープライズ対応 ([商用オファリング](https://www.openshift.com)) のバージョンで、Red Hat によりサポートされています。 顧客は OpenShift Container Platform の必要な権利を購入し、インフラストラクチャ全体のインストールと管理は顧客が自ら行います。

プラットフォーム全体を顧客が "所有" しているため、プラットフォームを自社のオンプレミスのデータセンターや、パブリック クラウド (Azure、AWS、Google など) などにインストールすることができます。

## <a name="openshift-online"></a>OpenShift Online

Red Hat によって管理されている**マルチテナント**の OpenShift です (Container Platform を使用)。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 

顧客は、コンテナーをデプロイしますが、コンテナーをどのホストで実行するかは制御できません。 マルチテナントであるため、他の顧客のコンテナーと同じ VM ホストにコンテナーが併置される場合があります。 コストは、コンテナーごとに課金されます。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Red Hat によって管理されている**シングルテナント**の OpenShift です (Container Platform を使用)。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 クラスターは顧客ごとに専用で、パブリック クラウド (AWS、Google、Azure (2018 年初頭に公開予定)) で実行されます。 開始時のクラスターには 4 つのアプリケーション ノードが含まれており、$48K/年です (1 年分を前払い)。

## <a name="next-steps"></a>次のステップ

- [Azure で OpenShift の共通の前提条件を構成する](./openshift-prerequisites.md)
- [OpenShift Origin のデプロイ](./openshift-origin.md)
- [OpenShift Container Platform のデプロイ](./openshift-container-platform.md)
- [デプロイ後のタスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
