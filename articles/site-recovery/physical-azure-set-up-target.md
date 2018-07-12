---
title: ターゲットを準備する (物理サーバーから Azure) | Microsoft Docs
description: この記事では、Windows または Linux を実行している物理サーバーを Azure にレプリケートする前に、Azure 環境を準備する方法について説明します。
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 370d245e39b848acade18d0e73f60a3246737629
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915522"
---
# <a name="prepare-target-vmware-to-azure"></a>ターゲットを準備する (VMware から Azure)

この記事では、Windows または Linux を実行している物理サーバー (x64)を Azure にレプリケートする前に、Azure 環境を準備する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は以下を前提としています。
- 物理サーバーを保護するための Recovery Services コンテナーを作成してあること。 Recovery Services コンテナーは [Azure Portal](http://portal.azure.com "Azure Portal") から作成できます。
- 物理サーバーを Azure にレプリケートできるように[オンプレミス環境をセットアップ](physical-azure-disaster-recovery.md)してあること。

## <a name="prepare-target"></a>ターゲットを準備する

**ステップ 1: 保護の目標の選択**と**ステップ 2: ソースの準備**を完了した後は、**ステップ 3: ターゲットの準備**を行います。

![ターゲットを準備する](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **サブスクリプション:** ドロップダウン メニューから、物理サーバーのレプリケート先のサブスクリプションを選びます。
2. **デプロイメント モデル:** デプロイメント モデルを選びます (クラシックまたは Resource Manager)。

選んだデプロイメント モデルに基づいて検証が実行され、物理サーバーのレプリケートおよびフェールオーバー先のターゲット サブスクリプションに、互換性のあるストレージ アカウントと仮想ネットワークが少なくとも 1 つはあることが確認されます。

検証が正常に完了した後、[OK] をクリックして次の手順に進みます。

互換性のある Resource Manager ストレージ アカウントまたは仮想ネットワークがない場合は、ページの上部にある **[+ ストレージ アカウント]** または **[+ ネットワーク]** ボタンをクリックして作成できます。

## <a name="next-steps"></a>次の手順
[レプリケーションの設定を構成する](vmware-azure-set-up-replication.md)。
