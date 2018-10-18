---
title: (Azure へのVMware/物理の) ターゲット環境を準備する |Microsoft Docs
description: この記事では、Azure への VMware VM レプリケーションおよび物理サーバーレプリケーションの Azure ターゲット環境を準備する方法について説明します。
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: bsiva
ms.openlocfilehash: 948812f05697362978ad041566d22977efec92a1
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434638"
---
# <a name="prepare-the-target-environment-vmwarephysical-to-azure"></a>(Azure へのVMware/物理の) ターゲット環境を準備する

この記事では、VMware 仮想マシンおよび物理サーバーの Azure へのレプリケーションを開始するための Azure ターゲット環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- Recovery Services コンテナーを[Azure portal](http://portal.azure.com "Azure portal")上で作成し、ソース マシンを保護します。
- ソースの[VMware の仮想マシン](vmware-azure-set-up-source.md)および[物理サーバー](physical-azure-set-up-source.md)を Azure にレプリケートできるようにオンプレミス環境をセットアップします。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択**と**ステップ 2: ソースの準備**を完了した後は、**ステップ 3: ターゲットの準備**を行います。

![ターゲットを準備する](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、仮想マシンおよび物理サーバーのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイメント モデルに基づいて検証が実行され、仮想マシンおよび物理サーバーのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、互換性のあるストレージ アカウントと仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

互換性のある Resource Manager ストレージ アカウントまたは仮想ネットワークがない場合は、ページの上部にある **[+ ストレージ アカウント]** または **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次の手順
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
