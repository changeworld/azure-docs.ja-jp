---
title: OpenShift Container Platform 4 x の Azure へのデプロイ | Microsoft Docs
description: OpenShift Container Platform 4.x を Azure にデプロイする。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392078"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>OpenShift Container Platform 4.x の Azure へのデプロイ

OpenShift Container Platform (OCP) 4.2 のデプロイは、Installer-Provisioned Infrastructure (IPI) モデルを通じて Azure でサポートされるようになりました。  OpenShift 4 をお試しいただけるランディング ページは [try.openshift.com](https://try.openshift.com/) です。 Azure に OCP 4.2 をインストールするには、[Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) のページにアクセスします。  このサイトにアクセスするには、Red Hat 資格情報が必要です。


## <a name="notes"></a>メモ 

 - Azure で OCP 4.x をインストールして実行するには、Azure Active Directory (AAD) サービス プリンシパル (SP) が必要です
     - SP には、Azure Active Directory Graph における **Application.ReadWrite.OwnedBy** の API アクセス許可が付与されている必要があります
     - この API のアクセス許可を有効にするには、AAD テナント管理者が管理者の同意を付与する必要があります
     - SP は、サブスクリプションの **[共同作成者]** ロールと **[ユーザーアクセス管理者]** ロールが付与されている必要があります
 - OCP 4.x のインストール モデルは 3.x とは異なり、Azure で OCP 4.x をデプロイするために使用できる Azure Resource Manager テンプレートはありません
 - インストール プロセス中に問題が発生した場合は、適切な会社 (Microsoft または Red Hat) にお問い合わせください

| 問題の説明 | コンタクト ポイント |
|-------------------|---------------|
| Azure 固有の問題 (AAD、SP、Azure サブスクリプションなど)                              | Microsoft |
| OpenShift 固有の問題 (インストール エラー/エラー、Red Hat サブスクリプションなど) |  Red Hat  |




## <a name="next-steps"></a>次の手順

- [OpenShift Container Platform の概要](https://docs.openshift.com)
