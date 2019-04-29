---
title: Azure の OpenShift の概要 | Microsoft Docs
description: Azure の OpenShift の概要について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006966"
---
# <a name="openshift-in-azure"></a>Azure の OpenShift

OpenShift は、エンタープライズに Docker と Kubernetes を導入する、オープンで拡張可能なコンテナー アプリケーション プラットフォームです。  

OpenShift には、コンテナーのオーケストレーションと管理のための Kubernetes が含まれています。 これにより、開発者と操作を中心に据えたツールが追加され、次のことが可能になります。

- 迅速なアプリケーション開発。
- 容易なデプロイとスケール変更。
- チームおよびアプリケーション向けの長期的なライフサイクル メンテナンス。

複数のバージョンの OpenShift を使用できます。  これらのバージョンのうち、現時点でお客様が Azure にデプロイできるのは OpenShift Container Platform と OKD (旧 OpenShift Origin) の 2 つのみです。

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform は Red Hat 製のエンタープライズ対応[市販バージョン](https://www.openshift.com)であり、Red Hat でサポートされています。 このバージョンでは、OpenShift Container Platform の必要な権利を購入します。インフラストラクチャ全体のインストールと管理は顧客が行います。

プラットフォーム全体を顧客が "所有" しているため、プラットフォームを自社のオンプレミスのデータセンターや、パブリック クラウド (Azure など) にインストールすることができます。

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure Red Hat OpenShift は Azure で実行される OpenShift のフル マネージド オファリングです。 このサービスは、Microsoft と Red Hat によって共同で管理され、サポートされています。 クラスターは、お客様の Azure サブスクリプションにデプロイされます。 サービスは、2019 年 5 月に一般提供される予定です。 サービスが一般提供された後、マネージド サービス用の別のドキュメントが提供される予定です。

## <a name="okd"></a>OKD

OKD は OpenShift の[オープン ソース](https://www.okd.io/)上流プロジェクトであり、コミュニティによりサポートされています。 OKD は CentOS または Red Hat Enterprise Linux (RHEL) にインストールできます。

## <a name="next-steps"></a>次の手順

- [Azure で OpenShift の共通の前提条件を構成する](./openshift-prerequisites.md)
- [OpenShift Container Platform の Azure へのデプロイ](./openshift-container-platform.md)
- [OpenShift Container Platform 自己管理型マーケットプレース プランをデプロイする](./openshift-marketplace-self-managed.md)
- [OpenShift を Azure Stack にデプロイする](./openshift-azure-stack.md)
- [デプロイ後タスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
