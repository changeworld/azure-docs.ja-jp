---
title: Azure Backup Central Reporting コンテンツ パックの更新
description: Power BI での Azure Backup コンテンツ パックに対する更新に関する情報です
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267180"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Azure Backup Central Reporting コンテンツ パックの更新 

[Azure Backup コンテンツ パック](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)を使用すると、バックアップに関するレポートを一元的に表示できます。 コンテンツ パックは定期的に更新され、機能が追加されたり、バグが修正されたりします。 この記事では、コンテンツ パックを更新する手順、更新を遅延させる手順、および時間の経過と共に行われる更新について説明します。

## <a name="how-to-get-updates-to-the-content-pack"></a>コンテンツ パックの更新を取得する方法

### <a name="to-get-the-updated-content-pack"></a>更新されたコンテンツ パックを取得するには
コンテンツ パックを変更していない場合、コンテンツ パックのコピーは自動的に更新されます。 コンテンツ パックを変更した場合は、Power BI とメールで通知を受け取ります。 ご自身の利便性に応じて、更新されたコンテンツ パックの取得を選択できます。 

### <a name="to-delay-the-update"></a>更新を遅延するには
ベスト プラクティスは、コンテンツ パックを[カスタム ワークスペース](https://youtu.be/26zyOtyHPJM?t=1m57s)にインポートすることです。 レポートを編集できるようになっています。
上記のとおり、コンテンツ パックが変更された場合、Power BI に通知が表示されます。 コンテンツ パックの取得を後で行うことができます。 

## <a name="coming-soon"></a>近日対応予定
   
Azure Backup コンテンツ パックは、MAB および Azure VM Backup の現在のサポートに加えて、さらに多くのワークスペース (IaaS VM バックアップの SQL、SC DPM) をサポートするように更新されています。 つまり、近いうちに、すべてのバックアップ データを一元的に表示および分析できるようになります。 組織のニーズに合うように、[レポートもカスタマイズできます](https://youtu.be/26zyOtyHPJM)。

ワークロード全体でレポートをさらに意味のあるものにするため、Azure Backup コンテンツ パックで事前構成されたレポートのセットが変更されています。 今後提供される予定のレポートのセットを以下に示します。

### <a name="summary"></a>まとめ
   
![まとめ](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>課金

![課金](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>コンプライアンス

![コンプライアンス](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>バックアップ項目
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>アラート

![アラート](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>[ジョブ]

![[ジョブ]](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>次のステップ:

* [組織全体でのレポートの共有](https://youtu.be/26zyOtyHPJM)
* [Azure Backup FAQ](backup-azure-backup-faq.md)
