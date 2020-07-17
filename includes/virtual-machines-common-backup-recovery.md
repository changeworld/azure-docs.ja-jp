---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181169"
---
## <a name="azure-backup"></a>Azure Backup

実稼働ワークロードを実行している Azure VM のバックアップには、Azure Backup をご利用ください。 Azure Backup では、Windows VM と Linux VM のアプリケーション整合性バックアップをサポートしています。 Azure Backup では、geo 冗長 Recovery コンテナーに保存される復旧ポイントが作成されます。 復旧ポイントから復元するときは、VM 全体を復元するか、特定のファイルだけを復元することができます。 

Azure VM のための Azure Backup の簡単で、実践的な紹介については、[Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) または [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md) 用の「Back up Azure virtual machines (Azure 仮想マシンのバックアップ)」チュートリアルを参照してください。

Azure Backup のしくみの詳細については、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](../articles/backup/backup-azure-vms-introduction.md)」を参照してください。


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery を使えば、大きな自然災害や広範囲にわたるサービス中断によりリージョン全体で障害が発生したような状況でも、VM を保護することができます。 VM に Azure Site Recovery を構成して、1 回のクリックによってわずか数分でアプリケーションを復旧できるようにします。 レプリケート先の Azure リージョンはペア リージョンに限られず、任意に選択できます。 

オンデマンドのテスト フェールオーバーによって、ディザスター リカバリーのテストを、実稼働ワークロードや進行中のレプリケーションに影響を与えずに実行できます。 復旧計画を作成して、複数の VM で実行されているアプリケーション全体のフェールオーバーおよびフェールバックを調整できます。 復旧計画機能は、Azure Automation Runbook と統合されています。

[仮想マシンのレプリケート](https://aka.ms/a2a-getting-started)から始めます。 

## <a name="managed-snapshots"></a>管理されたスナップショット 

開発環境やテスト環境では、管理ディスクを使用している VM のバックアップをスナップショットを使って迅速かつ簡単に作成できます。 管理されたスナップショットとは、マネージド ディスクの読み取り専用フル コピーのことを指します。 スナップショットはソース ディスクとは独立して存在し、VM のリビルドのための新しいマネージド ディスクの作成に使用できます。 課金は、ディスクの使用量に基づいて発生します。 たとえば、64 GB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GB である場合、スナップショットは使用されたデータ サイズである 10 GB 分のみ課金されます。  

スナップショットの作成の詳細については、以下を参照してください。

* [Windows のスナップショットを使用してマネージド ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Linux のスナップショットを使用してマネージド ディスクとして格納された VHD のコピーを作成する](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>次のステップ
Windows 仮想マシンのバックアップに関するチュートリアル ([Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) 用または [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md) 用) に従って、Azure Backup を試してみてください。
