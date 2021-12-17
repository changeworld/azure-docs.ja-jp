---
title: Azure 上で SAP IQ を使用して SAP BW NLS を実装する | Microsoft Docs
description: Azure 上で SAP IQ を使用して SAP BW NLS ソリューションを計画、デプロイ、構成します。
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: b4bdebe8ce06ba402df9d798e346a43dfefd89a9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261407"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>Azure 上で SAP IQ を使用した SAP BW NLS の実装ガイド

長年にわたって、SAP Business Warehouse (BW) システムを実行しているお客様は、データベース サイズが急激に増加し、それに伴ってコンピューティング コストも増加することを経験しています。 コストとパフォーマンスの最適なバランスを実現するために、お客様はニアライン ストレージ (NLS) を使用して履歴データを移行できます。 

SAP IQ に基づく NLS の実装は、プライマリ データベース (SAP HANA または AnyDB) から履歴データを移動するための SAP による標準的な方法です。 SAP IQ を統合すると、アクセス頻度の高いデータとアクセス頻度の低いデータを分離できるため、SAP BW システムでのリソース需要が減ります。 

このガイドでは、Azure 上で SAP IQ を使用して SAP BW NLS を計画、デプロイ、構成するためのガイドラインを提供します。 このガイドでは、SAP IQ NLS のデプロイに関連する一般的な Azure サービスおよび機能を取り上げており、NLS パートナー ソリューションについては説明していません。 

このガイドは、SAP IQ を使用した NLS のデプロイに関する SAP の標準ドキュメントに代わるものではありません。 むしろ、公式のインストールおよび管理ドキュメントを補完するものです。 

## <a name="solution-overview"></a>ソリューションの概要

稼働中の SAP BW システムでは、ビジネスおよび法的要件のためにデータの量が絶えず増加しています。 大量のデータは、システムのパフォーマンスに影響を及ぼし、管理作業を増加させる可能性があるため、データ エージング戦略を実装する必要があります。 

SAP BW システムのデータの量を削除せずに保持したい場合は、データ アーカイブを使用することができます。 データは最初にアーカイブまたはニアライン ストレージに移動され、その後 SAP BW システムから削除されます。 データのアーカイブ方法に応じて、データに直接アクセスすることも、データを必要に応じて再読み込みすることもできます。 

SAP BW ユーザーは、SAP IQ をニアライン ストレージ ソリューションとして使用できます。 SAP BW システムには、ニアライン ソリューションとしての SAP IQ 用のアダプターが付属しています。 NLS を実装すると、使用頻度の高いデータは SAP BW オンライン データベース (SAP HANA または AnyDB) に格納されます。 アクセス頻度の低いデータは SAP IQ に格納されるため、データの管理コストが削減され、SAP BW システムのパフォーマンスが向上します。 オンライン データとニアライン データ間の整合性を確保するために、アーカイブされたパーティションはロックされ、読み取り専用になっています。 

SAP IQ では、シンプレックスとマルチプレックスの 2 種類のアーキテクチャがサポートされています。 シンプレックス アーキテクチャでは、SAP IQ サーバーの 1 つのインスタンスが 1 つの仮想マシン上で実行されます。 ファイルはホスト マシンまたはネットワーク ストレージ デバイス上に配置できます。 

> [!Important]
> SAP NLS ソリューションの場合、SAP で使用および評価できるのはシンプレックス アーキテクチャのみです。

![SAP IQ ソリューションの概要を示す図。](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

Azure では、SAP IQ サーバーを別の仮想マシン (VM) 上に実装する必要があります。 SAP IQ ではすべての CPU とメモリを独自の用途に使用するため、他のデータベース インスタンスが既に実行されている既存のサーバーに SAP IQ ソフトウェアをインストールすることはお勧めしません。 1 台の SAP IQ サーバーを複数の SAP NLS 実装に使用できます。 

## <a name="support-matrix"></a>サポート マトリックス

SAP IQ NLS ソリューションのサポート マトリックスには、以下が含まれています。

- **オペレーティング システム**: SAP IQ はオペレーティング システム レベルでのみ認定されています。 SAP IQ 認定オペレーティング システムは、Azure インフラストラクチャで動作するように互換性がある限り、Azure 環境内で実行できます。 詳細については、[SAP ノート 2133194](https://launchpad.support.sap.com/#/notes/2133194) をご覧ください。

- **SAP BW 互換性**: SAP IQ のニアライン ストレージは、Unicode で既に実行されている SAP BW システムに対してのみリリースされます。 [SAP ノート 1796393](https://launchpad.support.sap.com/#/notes/1796393) には、SAP BW に関する情報が含まれています。

- **ストレージ**: Azure では、Premium マネージド ディスク (Windows と Linux)、Azure 共有ディスク (Windows のみ)、Azure NetApp Files (Linux のみ) が SAP IQ によってサポートされています。 

お使いの SAP IQ リリースに基づく最新情報については、[製品の可用性マトリックス](https://userapps.support.sap.com/sap/support/pam)をご覧ください。 

## <a name="sizing"></a>サイズ変更

SAP IQ のサイズ設定は、CPU、メモリ、およびストレージに限定されます。 Azure での SAP IQ の一般的なサイズ設定のガイドラインは、[SAP ノート 1951789](https://launchpad.support.sap.com/#/notes/1951789) に記載されています。 ガイドラインに従って導き出した推奨サイズ設定は、SAP の認定済み Azure 仮想マシンの種類にマップする必要があります。 [SAP ノート 1928533](https://launchpad.support.sap.com/#/notes/1928533) には、サポートされる SAP 製品と Azure VM の種類の一覧があります。 

[SAP ノート 1951789](https://launchpad.support.sap.com/#/notes/1951789) に記載されている SA IQ のサイズ設定ガイドとサイズ設定ワークシートは、SAP IQ データベースのネイティブな使用のために開発されました。 それらは SAP IQ データベースの計画のためのリソースを反映していないため、SAP NLS 用のリソースが使用されない可能性があります。

## <a name="azure-resources"></a>Azure リソース

### <a name="regions"></a>リージョン

Azure 上で SAP システムを既に実行している場合、リージョンはおそらく既に特定されています。 SAP IQ は、NLS ソリューションを実装する SAP BW システムと同じリージョンにデプロイする必要があります。 

SAP IQ のアーキテクチャを決定するには、Azure NetApp Files (Linux 専用の NFS) など、SAP IQ に必要なサービスがそのリージョンで確実に利用できる必要があります。 ご利用のリージョンでサービスが使えることを確認する場合は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」の Web ページをご覧ください。

### <a name="availability-sets"></a>可用性セット 

Azure インフラストラクチャ内で SAP システムの冗長性を実現するには、お使いのアプリケーションを可用性セットまたは可用性ゾーンにデプロイする必要があります。 SAP IQ の高可用性は SAP IQ マルチプレックス アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を満たしていません。 

SAP IQ シンプレックス アーキテクチャでの高可用性を実現するには、カスタム ソリューションを使用して 2 ノード クラスターを構成する必要があります。 この 2 ノード SAP IQ クラスターは、可用性セットまたは可用性ゾーンにデプロイできますが、そのデプロイ方法はノードに接続される Azure ストレージの種類によって決まります。 現在、Azure 共有 Premium ディスクと Azure NetApp Files ではゾーンへのデプロイがサポートされていません。 このため、SAP IQ のデプロイ オプションは、可用性セットのみになります。 

### <a name="virtual-machines"></a>仮想マシン

SAP IQ のサイズ設定に基づいて、Azure 仮想マシンに要件をマップする必要があります。 このアプローチは、Azure for SAP 製品でサポートされています。 最初に [SAP ノート 1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照することをお勧めします。ここには、Windows および Linux で SAP 製品に対してサポートされている Azure VM の種類が一覧で示されています。 

サポートされている VM の種類の選択肢だけでなく、それらの VM の種類が特定のリージョンで使用できるかどうかも確認する必要があります。 使用できる VM の種類は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」の Web ページで確認できます。 価格モデルを選択する場合は、「[SAP ワークロード用の Azure 仮想マシン](planning-guide.md#azure-virtual-machines-for-sap-workload)」をご覧ください。 

> [!TIP]
> 運用システムには、E シリーズの仮想マシンを使用することをお勧めします (そのコアとメモリの比率のため)。

### <a name="storage"></a>ストレージ

Azure Storage には、お客様が使用できるさまざまな種類のストレージがあります。 その詳細については、「[Azure で利用できるディスクの種類](../../disks-types.md)」の記事をご覧ください。 

Azure のストレージの種類の中には SAP シナリオでの使用が制限されているものもありますが、その他の種類は、特定の SAP ワークロードのシナリオ用として適切であるか、または最適化されています。 詳細については、「[SAP ワークロードの Azure Storage の種類](planning-guide-storage.md)」のガイドをご覧ください。 これには、SAP に適したストレージ オプションが明確に示されています。 

Azure 上の SAP IQ には、次の種類の Azure ストレージを使用できます。 どれを選択するかは、オペレーティング システム (Windows または Linux) やデプロイ方法 (スタンドアロンまたは高可用性) によって異なります。

- Azure Managed Disks

  [マネージド ディスク](../../managed-disks-overview.md)とは、Azure によって管理されるブロック レベルのストレージ ボリュームです。 SAP IQ シンプレックス デプロイには、マネージド ディスクを使用できます。 さまざまな種類のマネージド ディスクが利用可能できますが、SAP IQ には [Premium SSD](../../disks-types.md#premium-ssds) を使用することをお勧めします。 

- Azure 共有ディスク

  [共有ディスク](../../disks-shared.md)とは、1 つのマネージド ディスクを複数の VM に同時に接続できるようにする Azure マネージド ディスクの新機能です。 共有されたマネージド ディスクでは、SMB または NFS 経由でアクセスできるフル マネージドのファイル システムをネイティブに提供していません。 [Windows Server フェールオーバー クラスター](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC) のような、クラスター ノードの通信と書き込みロックを処理するクラスター マネージャーを使用する必要があります。 
  
  Windows で SAP IQ シンプレックス アーキテクチャの高可用性ソリューションをデプロイする場合は、WSFC で管理される 2 つのノード間で Azure 共有ディスクを使用できます。 Azure 共有ディスクを使用した SAP IQ デプロイ アーキテクチャについては、「[Deploy SAP IQ NLS HA Solution using Azure shared disk on Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)」(Windows Server で Azure 共有ディスクを使用して SAP IQ NLS HA ソリューションをデプロイする) の記事で説明されています。

- Azure NetApp Files

  Linux での SAP IQ のデプロイでは、[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) をファイル システム (NFS プロトコル) として使用して、スタンドアロンまたは高可用性ソリューションをインストールできます。 このストレージ オファリングは、一部のリージョンでは利用できません。 最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」の Web ページをご覧ください。 Azure NetApp Files を使用した SAP IQ デプロイ アーキテクチャについては、「[Deploy SAP IQ-NLS HA Solution using Azure NetApp Files on SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)」(SUSE Linux Enterprise Server で Azure NetApp Files を使用して SAP IQ-NLS HA ソリューションをデプロイする) の記事で説明されています。

次の表に、オペレーティング システムに基づいたストレージの種類ごとの推奨事項を示します。 

| ストレージの種類        | Windows | Linux |
| ------------------- | ------- | ----- |
| Azure Managed Disks | はい     | はい   |
| Azure 共有ディスク  | はい     | いいえ    |
| Azure NetApp Files  | いいえ      | はい   |

### <a name="networking"></a>ネットワーク

Azure には、SAP IQ をニアライン ストレージとして使用する SAP BW システムで実現できるすべてのシナリオのマッピングを可能にするネットワーク インフラストラクチャが用意されています。 これらのシナリオには、オンプレミスのシステムへの接続や、別の仮想ネットワーク内のシステムへの接続などが含まれています。 詳細については、[SAP ワークロード用の Microsoft Azure ネットワーク](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)に関するページをご覧ください。

## <a name="deploy-sap-iq-on-windows"></a>Windows に SAP IQ をデプロイする

### <a name="server-preparation-and-installation"></a>サーバーの準備とインストール

SAP IQ を使用して Windows に NLS を実装するためにサーバーを準備する場合は、[SAP ノート 2780668 (SAP First Guidance - BW NLS Implementation with SAP IQ)](https://launchpad.support.sap.com/#/notes/0002780668) で最新情報を確認できます。 これには、SAP BW システムの前提条件、SAP IQ ファイル システム レイアウト、インストール、構成後のタスク、および SAP IQ との SAP BW NLS の統合に関する包括的な情報が含まれています。

### <a name="high-availability-deployment"></a>高可用性のデプロイ

SAP IQ では、シンプレックスとマルチプレックスの両方のアーキテクチャがサポートされています。 NLS ソリューションの場合、シンプレックス サーバー アーキテクチャのみが使用可能であり、評価されます。 シンプレックスは、1 つの仮想マシンで実行されている SAP IQ サーバーの 1 つのインスタンスです。 

技術的には、SAP IQ の高可用性はマルチプレックス サーバー アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を満たしていません。 シンプレックス サーバー アーキテクチャに対して、SAP は高可用性構成で SAP IQ を実行するための機能や手順を提供していません。 

Windows でシンプレックス サーバー アーキテクチャに対して SAP IQ の高可用性を設定するには、カスタム ソリューションを設定する必要があり、このためには Windows Server フェールオーバー クラスターや共有ディスクなどの追加の構成が必要です。 Windows 上の SAP IQ に対するこのようなカスタム ソリューションの 1 つが、「[Deploy SAP IQ NLS HA solution using Azure shared disk on Windows Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)」(Windows Server で Azure 共有ディスクを使用して SAP IQ NLS HA ソリューションをデプロイする) で詳しく説明されています。

### <a name="backup-and-restore"></a>バックアップと復元

Azure では、「[SAP IQ Administration: Backup, Restore, and Data Recovery](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)」(SAP IQ の管理: バックアップ、復元、データの復旧) のページで説明されているように、SAP IQ データベースのバックアップをスケジュールできます。 SAP IQ には、次の種類のデータベース バックアップが用意されています。 各バックアップの種類の詳細については、[バックアップ シナリオ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)に関するページをご覧ください。

- **完全バックアップ**: データベースの完全なコピーを作成します。 
- **増分バックアップ**: 任意の種類の前回のバックアップ以降に行われたすべてのトランザクションをコピーします。 
- **完全バックアップ以降の増分**: 前回の完全バックアップ以降にデータベースに行われたすべての変更をバックアップします。
- **仮想バックアップ**: SAP IQ ストアのテーブル データとメタデータを除くすべてのデータベースをコピーします。

お使いの SAP IQ データベースのサイズに応じて、任意のバックアップ シナリオからデータベースのバックアップをスケジュールできます。 ただし、SAP が提供する NLS インターフェイスで SAP IQ を使用している場合は、SAP IQ データベースのバックアップ プロセスを自動化することをお勧めします。 自動化では、プライマリ データベースと SAP IQ データベースの間で移動されたデータを失うことなく、常に SAP IQ データベースを一貫性のある状態に確実に復旧できます。 SAP IQ ニアライン ストレージの自動化の設定について詳しくは、[SAP ノート 2741824 (How to setup backup automation for SAP IQ Cold Store/Near-line Storage)](https://launchpad.support.sap.com/#/notes/2741824) をご覧ください。 

大規模な SAP IQ データベースには、仮想バックアップを使用できます。 詳細については、[仮想バックアップ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)に関するページおよび「[Introduction Virtual Backup in SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)」(SAP Sybase IQ での仮想バックアップの概要) をご覧ください。 また、[SAP ノート 2461985 (How to Backup Large SAP IQ Database)](https://launchpad.support.sap.com/#/notes/0002461985) もご覧ください。

ネットワーク ドライブ (SMB プロトコル) を使用して Windows 上の SAP IQ サーバーのバックアップと復元を行う場合は、必ず UNC パスをバックアップに使用してください。 バックアップと復元に UNC パスを使用する場合は、3 つのバックスラッシュ (`\\\`) が必要です。

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Linux に SAP IQ をデプロイする

### <a name="server-preparation-and-installation"></a>サーバーの準備とインストール

SAP IQ を使用して Linux に NLS を実装するためにサーバーを準備する場合は、[SAP ノート 2780668 (SAP First Guidance - BW NLS Implementation with SAP IQ)](https://launchpad.support.sap.com/#/notes/0002780668) で最新情報を確認できます。 これには、SAP BW システムの前提条件、SAP IQ ファイル システム レイアウト、インストール、構成後のタスク、および SAP IQ との SAP BW NLS の統合に関する包括的な情報が含まれています。

### <a name="high-availability-deployment"></a>高可用性のデプロイ

SAP IQ では、シンプレックスとマルチプレックスの両方のアーキテクチャがサポートされています。 NLS ソリューションの場合、シンプレックス サーバー アーキテクチャのみが使用可能であり、評価されます。 シンプレックスは、1 つの仮想マシンで実行されている SAP IQ サーバーの 1 つのインスタンスです。 

技術的には、SAP IQ の高可用性はマルチプレックス サーバー アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を満たしていません。 シンプレックス サーバー アーキテクチャに対して、SAP は高可用性構成で SAP IQ を実行するための機能や手順を提供していません。

Linux でシンプレックス サーバー アーキテクチャに対して SAP IQ の高可用性を設定するには、カスタム ソリューションを設定する必要があり、このためには Pacemaker などの追加の構成が必要です。 Linux 上の SAP IQ に対するこのようなカスタム ソリューションの 1 つが、「[Deploy SAP IQ-NLS HA solution using Azure NetApp Files on SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)」(SUSE Linux Enterprise Server で Azure NetApp Files を使用して SAP IQ-NLS HA ソリューションをデプロイする) で詳しく説明されています。

### <a name="backup-and-restore"></a>バックアップと復元

Azure では、「[SAP IQ Administration: Backup, Restore, and Data Recovery](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)」(SAP IQ の管理: バックアップ、復元、データの復旧) のページで説明されているように、SAP IQ データベースのバックアップをスケジュールできます。 SAP IQ には、次の種類のデータベース バックアップが用意されています。 各バックアップの種類の詳細については、[バックアップ シナリオ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)に関するページをご覧ください。

- **完全バックアップ**: データベースの完全なコピーを作成します。 
- **増分バックアップ**: 任意の種類の前回のバックアップ以降に行われたすべてのトランザクションをコピーします。 
- **完全バックアップ以降の増分**: 前回の完全バックアップ以降にデータベースに行われたすべての変更をバックアップします。
- **仮想バックアップ**: SAP IQ ストアのテーブル データとメタデータを除くすべてのデータベースをコピーします。

お使いの SAP IQ データベースのサイズに応じて、任意のバックアップ シナリオからデータベースのバックアップをスケジュールできます。 ただし、SAP が提供する NLS インターフェイスで SAP IQ を使用している場合は、SAP IQ データベースのバックアップ プロセスを自動化することをお勧めします。 自動化では、プライマリ データベースと SAP IQ データベースの間で移動されたデータを失うことなく、常に SAP IQ データベースを一貫性のある状態に確実に復旧できます。 SAP IQ ニアライン ストレージの自動化の設定について詳しくは、[SAP ノート 2741824 (How to setup backup automation for SAP IQ Cold Store/Near-line Storage)](https://launchpad.support.sap.com/#/notes/2741824) をご覧ください。 

大規模な SAP IQ データベースには、仮想バックアップを使用できます。 詳細については、[仮想バックアップ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)に関するページおよび「[Introduction Virtual Backup in SAP Sybase IQ](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)」(SAP Sybase IQ での仮想バックアップの概要) をご覧ください。 また、[SAP ノート 2461985 (How to Backup Large SAP IQ Database)](https://launchpad.support.sap.com/#/notes/0002461985) もご覧ください。

## <a name="disaster-recovery"></a>障害復旧

ここでは、SAP IQ NLS ソリューションのディザスター リカバリー (DR) 対策について説明します。 これは、SAP の DR アプローチ全体の主要なリソースを表す [SAP のディザスター リカバリーの設定](../../../site-recovery/site-recovery-sap.md)に関する記事を補足するものです。 その記事で説明されているプロセスは、抽象的なレベルで示されています。 正確な手順を検証し、DR 戦略のテストを十分に行う必要があります。 

SAP IQ については、[SAP ノート 2566083](https://launchpad.support.sap.com/#/notes/0002566083) をご覧ください。これには、DR 環境を安全に実装するための方法が説明されています。 Azure では、SAP IQ DR 戦略に [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用することもできます。 SAP IQ DR の戦略は、Azure でのデプロイ方法によって異なります。また、お使いの SAP BW システムと一致している必要があります。 

### <a name="standalone-deployment-of-sap-iq"></a>SAP IQ のスタンドアロン デプロイ

アプリケーション レベルの冗長性も高可用性も備えていないスタンドアロン システムとして SAP IQ をインストールしてあるが、ビジネスで DR のセットアップが必要となった場合は、仮想マシンに接続されているすべてのディスク (Azure マネージド ディスク) がローカルになります。 
  
[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用すると、スタンドアロンの SAP IQ 仮想マシンをセカンダリ リージョンにレプリケートできます。 災害や障害が発生した場合にレプリケート環境に簡単にフェールオーバーして作業を続けることができるように、サーバーと、接続されているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされます。 Azure DR リージョンへの SAP IQ VM のレプリケートを開始するには、[仮想マシンの Azure へのレプリケート](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)に関する記事のガイダンスに従います。 

### <a name="highly-available-deployment-of-sap-iq"></a>SAP IQ の高可用性デプロイ

SAP IQ のバイナリとデータベース ファイルが Azure 共有ディスク (Windows のみ) 上、または Azure NetApp Files (Linux のみ) のようなネットワーク ドライブ上に置かれている高可用性システムとして SAP IQ をインストールしてある場合は、以下を確認する必要があります。

- DR サイト上に同様の高可用性 SAP IQ システムが必要かどうか。
- スタンドアロンの SAP IQ インスタンスでビジネス要件が十分に満たされるか。 
  
DR サイト上にスタンドアロンの SAP IQ が必要な場合は、[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用してプライマリの SAP IQ 仮想マシンをセカンダリ リージョンにレプリケートできます。 これにより、サーバーと、ローカルに接続されているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされますが、Azure 共有ディスクまたはネットワーク ドライブ (Azure NetApp Files など) はレプリケートされません。 
  
Azure 共有ディスクまたはネットワーク ドライブのデータをコピーする場合は、任意のファイルベースのコピー ツールを使用して、Azure リージョン間でデータをレプリケートできます。 Azure NetApp Files のボリュームを別のリージョンにコピーする方法について詳しくは、[Azure NetApp Files についての FAQ](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region) に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
