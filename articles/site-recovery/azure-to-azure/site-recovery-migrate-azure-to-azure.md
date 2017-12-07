---
title: "Azure リージョン間での Azure IaaS VM の移行 | Microsoft Docs"
description: "Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS 仮想マシンを移行します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行

[Site Recovery](../site-recovery-overview.md) サービスを使用して、Azure リージョン間で Azure 仮想マシン (VM) を移行する場合に、この記事をご利用ください。

## <a name="prerequisites"></a>前提条件

移行する IaaS VM が必要です。

## <a name="deployment-steps"></a>デプロイメントの手順

1. [コンテナーを作成します](azure-to-azure-tutorial-enable-replication.md#create-a-vault)。
2. 移行する VM の[レプリケーションを有効にして](azure-to-azure-tutorial-enable-replication.md#enable-replication)、ソースとして Azure を選択します。 現在、管理ディスクを使用する Azure VM のネイティブ レプリケーションはサポートされていません。
3. [フェールオーバーを実行します](../site-recovery-failover.md)。 初期レプリケーションが完了したら、Azure リージョン間でフェールオーバーを実行できます。 必要に応じて、復旧計画を作成し、フェールオーバーを実行して、複数の仮想マシンをリージョン間で移行できます。 [こちら](../site-recovery-create-recovery-plans.md) をご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure Site Recovery の概要](../site-recovery-overview.md)で、その他のレプリケーション シナリオを確認してください。
