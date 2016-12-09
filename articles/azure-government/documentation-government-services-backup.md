---
title: Azure Government Backup | Microsoft Docs
description: "この記事では、Azure Government で使用できる Azure Backup の機能の概要を示します。"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


---
# <a name="azure-government-backup"></a>Azure Government Backup

この記事では、Azure Backup サービスの概要と、Azure Government で利用可能なバックアップ機能の一覧を示します。 Azure Backup は、Microsoft Cloud へのデータのバックアップ (または保護) に使用できる、Azure ベースのサービスです。 Azure でのデータ保護では、クラウドへのバックアップだけでなく、クラウドまたはオンプレミス インストールにデータを復元することもできます。 Azure Backup には、次のような主なメリットがあります。

- ストレージ管理の自動化
- 無制限のスケーリング
- 複数のストレージ オプション
- 無制限のデータ転送
- データの暗号化
- アプリケーションの整合性のバックアップ
- 長期保存

Azure Backup を利用するのが初めてであり、使用可能な機能の概要を確認したい方は、「[Azure Backup とは](../backup/backup-introduction-to-azure-backup.md)」をご覧ください。

> [!IMPORTANT]
> 現時点では、Azure Government Backup は Service Manager デプロイメント (別名クラシック デプロイメント モデル) をサポートしています。 Resource Manager デプロイメントはまだサポートされていません。 Azure Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの違いについては、[こちらをご覧ください](../resource-manager-deployment-model.md)。

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Azure Government Backup で使用できる Azure Backup コンポーネント

Azure Backup を使用して保護できるものとしては、ファイル、フォルダー、ボリューム、仮想マシン、アプリケーション、ワークロードがあります。 保護の対象とデータの存在場所に応じて、異なる Azure Backup コンポーネントを使用します。 以下の各セクションに、コンポーネントごとの Azure Backup パブリック ドキュメントの記事へのリンクを示します。

各記事では、クラシック バージョンのポータルでの Azure Backup コンポーネントの使用方法について説明しています。

### <a name="windows-server-and-windows-computers"></a>Windows Server および Windows コンピューター

- [Windows Server および Windows クライアント コンピューターのバックアップ](../backup/backup-configure-vault-classic.md)
- [Windows Server および Windows クライアント コンピューターの復元](../backup/backup-azure-restore-windows-server.md)
- [Windows Server および Windows クライアント コンピューターのバックアップの管理](../backup/backup-azure-manage-windows-server.md)
- [PowerShell を使用した Windows Server のバックアップ](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>Virtual Machines

- [仮想マシン環境の準備](../backup/backup-azure-vms-prepare.md)
- [仮想マシンのバックアップ](../backup/backup-azure-vms-first-look.md)
- [仮想マシンの復元](../backup/backup-azure-restore-vms.md)
- [仮想マシンの管理](../backup/backup-azure-manage-vms-classic.md)
- [PowerShell を使用した仮想マシンのバックアップ](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager

- [System Center Data Protection Manager のバックアップ](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Azure Backup Server

- [Azure Backup Server](../backup/backup-azure-microsoft-azure-backup-classic.md)

Azure Backup Server は、System Center Data Protection Manager (DPM) と同様の働きをする Azure Backup コンポーネントです。 Azure Backup Server では、単一のコンソールから Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードをクラウドに保護することができます。

## <a name="next-steps"></a>次のステップ

どこから手を付ければよいかわからない場合は、こちらの「[クラシック デプロイメント モデルを使用して Windows サーバーまたはクライアントを Azure にバックアップする](../backup/backup-configure-vault-classic.md)」からご覧ください。 このチュートリアルでは、Windows Server またはコンピューターでのバックアップ プロジェクトのを設定手順について説明しています。

Azure Backup の使い方は知っているものの料金が気になる場合は、[Backup の価格ページ](http://azure.microsoft.com/pricing/details/backup/)をご覧ください。 このページにはよく寄せられる質問のリストも記載されており、役立つ情報を得ることができます。 また、**[リージョン]** ドロップダウン メニューに Azure Government リージョンは 2 つ含まれています。



<!--HONumber=Nov16_HO3-->


