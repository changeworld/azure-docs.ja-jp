---
title: Azure の OpenShift の概要 | Microsoft Docs
description: Azure の OpenShift の概要について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: c8e740a66271c88b3abb036867d1760cc9e77607
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944503"
---
# <a name="openshift-in-azure"></a>Azure の OpenShift

OpenShift は、エンタープライズに Docker と Kubernetes を導入する、オープンで拡張可能なコンテナー アプリケーション プラットフォームです。  

OpenShift には、コンテナーのオーケストレーションと管理のための Kubernetes が含まれています。 これにより、開発者と操作を中心に据えたツールが追加され、次のことが可能になります。

- 迅速なアプリケーション開発。
- 容易なデプロイとスケール変更。
- チームおよびアプリケーション向けの長期的なライフサイクル メンテナンス。

以下の複数のバージョンの OpenShift を使用できます。

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

顧客が Azure にデプロイできるのは、この記事で扱う 4 つのバージョンのうち 2 つ (OpenShift Origin と OpenShift Container Platform) のみです。

## <a name="openshift-origin"></a>OpenShift Origin

Origin は OpenShift の[オープン ソース](https://www.openshift.org/)上流プロジェクトであり、コミュニティによりサポートされています。 Origin は CentOS または Red Hat Enterprise Linux (RHEL) にインストールできます。

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform は Red Hat 製のエンタープライズ対応[市販バージョン](https://www.openshift.com)であり、Red Hat でサポートされています。 このバージョンでは、OpenShift Container Platform の必要な権利を購入します。インフラストラクチャ全体のインストールと管理は顧客が行います。

プラットフォーム全体を顧客が "所有" しているため、プラットフォームを自社のオンプレミスのデータセンターや、パブリック クラウド (Azure、AWS、Google など) にインストールすることができます。

## <a name="openshift-online"></a>OpenShift Online

Online は、Red Hat によって管理されている、Container Platform を使用する*マルチテナント* OpenShift です。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 

このバージョンで、顧客はコンテナーをデプロイしますが、コンテナーを実行するホストは制御できません。 Online はマルチテナントなので、他の顧客のコンテナーと同じ VM ホストにコンテナーが配置される場合があります。 コストは、コンテナーごとに課金されます。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated は、Red Hat によって管理されている、Container Platform を使用する*シングルテナント*の OpenShift です。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 クラスターは各顧客専用で、パブリック クラウド (AWS、Google、Azure (2018 年初頭に公開予定)) で実行されます。 クラスターの開始には、1 年間に $48,000 (前払い) の 4 つのアプリケーション ノードが含まれています。

## <a name="next-steps"></a>次の手順

- [Azure で OpenShift の共通の前提条件を構成する](./openshift-prerequisites.md)
- [Azure に OpenShift Origin をデプロイする](./openshift-origin.md)
- [OpenShift Container Platform の Azure へのデプロイ](./openshift-container-platform.md)
- [デプロイ後タスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
