---
title: Azure への VMware レプリケーションのターゲット環境を準備する | Microsoft Docs
description: この記事では、Azure への VMware VM レプリケーションの Azure ターゲット環境を準備する方法について説明します。
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Azure への VMware レプリケーションのターゲット環境を準備する

この記事では、VMware 仮想マシンの Azure へのレプリケーションを開始するための Azure ターゲット環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- VMware の仮想マシンを保護するための Recovery Services コンテナーを作成してあること。 Recovery Services コンテナーは [Azure Portal](http://portal.azure.com "Azure Portal") から作成できます。
- VMware の仮想マシンを Azure にレプリケートできるように[オンプレミス環境をセットアップ](vmware-azure-set-up-source.md)してあること。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択**と**ステップ 2: ソースの準備**を完了した後は、**ステップ 3: ターゲットの準備**を行います。

![ターゲットを準備する](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、仮想マシンのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイメント モデルに基づいて検証が実行され、仮想マシンのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、互換性のあるストレージ アカウントと仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

互換性のある Resource Manager ストレージ アカウントまたは仮想ネットワークがない場合は、ページの上部にある **[+ ストレージ アカウント]** または **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次の手順
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
