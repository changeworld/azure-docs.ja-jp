---
title: Azure Stack ファイルとアプリケーションをバックアップする | Microsoft Docs
description: Azure Backup を使用して、Azure Stack ファイルとアプリケーションを Azure Stack 環境にバックアップし、復元します。
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196714"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Azure Stack 上のファイルとアプリケーションのバックアップ
Azure Backup を使用して、Azure Stack 上のファイルとアプリケーションを保護 (バックアップ) することができます。 ファイルとアプリケーションをバックアップするには、Microsoft Azure Backup Server を Azure Stack 上で動作する仮想マシンとしてインストールします。 同じ仮想ネットワーク内の任意の Azure Stack サーバー上で実行されているすべてのアプリケーションを保護できます。 Azure Backup Server をインストールしたら、Azure ディスクを追加して、短期バックアップ データに使用できるローカル ストレージを増やしてください。 Azure Backup Server は、長期保有には Azure Storage を使用します。

> [!NOTE]
> Azure Backup Server と System Center Data Protection Manager (DPM) は似ていますが、DPM は Azure Stack での使用がサポートされていません。
>


## <a name="azure-backup-server-protection-matrix"></a>Azure Backup Server の保護マトリックス
Azure Backup Server は、以下の Azure Stack 仮想マシン ワークロードを保護します。

| 保護されたデータ ソース | 保護と回復 |
| --------------------- | ----------------------- |
| Windows Server Semi Annual Channel - Datacenter/Enterprise/Standard | ボリューム、ファイル、フォルダー |
| Windows Server 2016 - Datacenter/Enterprise/Standard | ボリューム、ファイル、フォルダー |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | ボリューム、ファイル、フォルダー |
| Windows Server 2012 - Datacenter/Entprise/Standard | ボリューム、ファイル、フォルダー |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | ボリューム、ファイル、フォルダー |
| SQL Server 2016 | データベース |
| SQL Server 2014 | データベース |
| SQL Server 2012 SP1 | データベース |
| SharePoint 2013 | ファーム、データベース、フロントエンド、Web サーバー |
| SharePoint 2010 | ファーム、データベース、フロントエンド、Web サーバー |


## <a name="install-azure-backup-server"></a>Azure Backup Server のインストール
Azure Stack 仮想マシンに Azure Backup Server をインストールするには、「[Azure Backup Server を使用してワークロードをバックアップするための準備](backup-azure-microsoft-azure-backup.md)」を参照してください。 Azure Backup Server をインストールおよび構成する前に、次の点に注意してください。

### <a name="determining-size-of-virtual-machine"></a>仮想マシンのサイズを決める
Azure Backup 仮想マシン上で Azure Backup Server を実行するには、サイズ A2 以上を使用します。 仮想マシン サイズを選択する場合、[Azure Stack VM サイズ計算ツール](https://www.microsoft.com/download/details.aspx?id=56832)をダウンロードすることをお勧めします。

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 仮想マシン上の仮想ネットワーク
Azure Stack ワークロードで使用されるすべての仮想マシンは、同じ Azure 仮想ネットワークと Azure サブスクリプションに属している必要があります。

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>ローカル ディスクと Azure にバックアップ データを保存する
Azure Backup Server は、運用の復旧のために、仮想マシンに接続された Azure ディスクにバックアップ データを保存します。 ディスクと記憶域スペースが仮想マシンに接続されると、記憶域は Azure Backup Server で管理されます。 バックアップ データ記憶域のサイズは、各 [Azure Stack 仮想マシン](../azure-stack/user/azure-stack-storage-overview.md)に接続されているディスクの数とサイズによって変わります。 Azure Stack VM の各サイズには、仮想マシンに接続できるディスクの最大数があります。 たとえば、A2 は 4 ディスクです。 A3 は 8 ディスクです。 A4 は 16 ディスクです。 ここでも、ディスクのサイズと数によってバックアップ ストレージ プール全体が決まります。

> [!IMPORTANT]
> Azure Backup Server に接続されたディスク上に、5 日間を超える運用の復旧 (バックアップ) データを保持することは推奨**されません**。
>

バックアップ データを Azure に保存すると、Azure Stack 上のバックアップ インフラストラクチャが減ります。 5 日間を超えるデータの場合は、Azure に保存するようにしてください。

Azure にバックアップ データを保存するには、Recovery Services コンテナーを作成または使用します。 Azure Backup Server ワークロードのバックアップを準備するときに、[Recovery Services コンテナーを構成](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)します。 構成が完了した後は、バックアップ ジョブが実行されるたびに、コンテナーに復旧ポイントが作成されます。 各 Recovery Services コンテナーは、最大 9999 個の復旧ポイントを保持します。 作成された復旧ポイントの数と保持期間に応じて、バックアップ データを長年にわたって保持することができます。 たとえば、毎月の復旧ポイントを作成して 5 年間保持することができます。
 
### <a name="using-sql-server"></a>SQL Server の使用
Azure Backup Server データベースにリモート SQL Server を使用する場合は、SQL Server を実行する Azure Stack VM のみを選択します。

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM のパフォーマンス
他の仮想マシンと共有する場合、ストレージ アカウントのサイズと IOPS の制限は、Azure Backup Server 仮想マシンのパフォーマンスに影響を与えます。 そのため、Azure Backup Server 仮想マシンには別のストレージ アカウントを使用するようにします。 Azure Backup Server 上で実行される Azure Backup エージェントには、次のための一時記憶域が必要です。
    - 自身の使用 (キャッシュ場所)、
    - クラウドから復元されたデータ (ローカル ステージング領域)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup の一時ディスク記憶域を構成する
各 Azure Stack 仮想マシンには一時的なディスク記憶域があり、ユーザーはボリューム `D:\` として利用できます。 Azure Backup が必要とするローカルのステージング領域は、`D:\` に内に配置するように構成できます。また、キャッシュ場所は `C:\` に配置できます。 このようにすると、Azure Backup Server 仮想マシンに接続されたデータ ディスクから記憶域を切り離す必要がありません。

### <a name="scaling-deployment"></a>展開のスケーリング
展開をスケーリングするには、次の選択肢があります。
  - スケールアップ - Azure Backup Server 仮想マシンのサイズを A シリーズから D シリーズに増やし、[Azure Stack 仮想マシンの手順に従って](../azure-stack/user/azure-stack-manage-vm-disks.md)ローカル記憶域を増やします。
  - データをオフロードする - 古いデータを Azure Backup Server に送信し、Azure Backup Server に接続された記憶域に最新のデータのみを保持します。
  - スケールアウト - Azure Backup Server をさらに追加してワークロードを保護します。

## <a name="see-also"></a>関連項目
Azure Backup Server を使用して他のワークロードを保護する方法については、次のいずれかの記事を参照してください。
- [SharePoint ファームのバックアップ](backup-azure-backup-sharepoint-mabs.md)
- [SQL Server のバックアップ](backup-azure-sql-mabs.md)
