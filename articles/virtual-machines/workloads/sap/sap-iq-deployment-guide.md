---
title: Azure で SAP IQ を使用して SAP NLS/SDA を実装する | Microsoft Docs
description: Azure で SAP IQ を使用して SAP NLS/SDA ソリューションを計画、デプロイ、構成します。
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
ms.openlocfilehash: a0641f6c00712e0d3dd32ff04d7bff9695dc4ce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781329"
---
# <a name="sap-bw-near-line-storage-nls-implementation-guide-with-sap-iq-on-azure"></a>Azure での SAP IQ を使用した SAP BW ニア ライン ストレージ (NLS) の実装ガイド

## <a name="overview"></a>概要

何年にもわたって、SAP BW システムを実行しているお客様は、データベース サイズが急激に増加し、その結果としてコンピューティング コストの増加も経験しています。 コストとパフォーマンスの完璧なバランスを実現するために、お客様はニアライン ストレージ (NLS) を使用して履歴データを移行できます。 SAP IQ に基づく NLS 実装は、プライマリ データベース (SAP HANA または anyDB) から履歴データを移動するための SAP による標準的な方法です。 ニアライン ソリューションとしての SAP IQ のアダプターが SAP BW システムに付属しています。 SAP IQ を統合すると、アクセス頻度の高いデータと頻度の低いものを分離できるため、SAP BW システムでのリソース需要が減ります。 

このガイドでは、Azure で SAP IQ を使用した SAP BW ニアライン ストレージ (NLS) の計画、デプロイ、および構成に関するガイドラインを提供します。 このガイドは、SAP IQ - NLS デプロイに関連する一般的な Azure サービスと機能を説明することを目的としており、NLS パートナー ソリューションについては説明していません。 このガイドは、SAP IQ を使用した NLS デプロイに関する SAP の標準ドキュメントを置き換えることを目的としたものではなく、公式のインストールおよび管理に関するドキュメントを補完する目的で作成されています。 

## <a name="solution-overview"></a>ソリューションの概要

稼働中の BW システムでは、データの量は絶えず増加し、このデータはビジネスおよび法的な要件のために長期間必要になります。 大量のデータは、システムのパフォーマンスに影響を及ぼし、管理作業を増加させる可能性があるため、データのエージング戦略を実行する必要があります。 SAP BW システムのデータの量を削除せずに保持したい場合は、データ アーカイブを使用することができます。 データは最初にアーカイブまたはニアラインのいずれかのストレージに移動されてから、BW システムから削除されます。 データのアーカイブ方法に応じて、データに直接アクセスするか、必要に応じてデータを再読み込みすることができます。 

SAP BW ユーザーは、SAP IQ をニアライン ストレージ (NLS) ソリューションとして使用できます。 ニアライン ソリューションとしての SAP IQ のアダプターが SAP BW システムに付属しています。 NLS を実装すると、使用頻度の高いデータは SAP BW オンライン データベース (SAP HANA または任意の DB) に格納され、アクセス頻度の低いデータは SAP IQ に格納されます。これにより、データの管理コストが削減され、SAP BW システムのパフォーマンスが向上します。 オンライン データとニアライン データ間の整合性を確保するために、アーカイブされたパーティションはロックされ、読み取り専用になっています。 

SAP IQ では、シンプレックスとマルチプレックスの 2 種類のアーキテクチャがサポートされています。 シンプレックス アーキテクチャでは、SAP IQ サーバーの 1 つのインスタンスが 1 つの仮想マシン上で実行され、ファイルはホスト マシンまたはネットワーク ストレージ デバイス上に配置できます。 

> [!Important]
> SAP-NLS ソリューションの場合、SAP で使用または評価できるのはシンプレックス アーキテクチャだけです。

![SAP IQ ソリューションの概要](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

Azure では、SAP IQ サーバーを別の仮想マシン (VM) に実装する必要があります。 SAP IQ では、それ自体で使用するためにすべての CPU とメモリが使用されるため、他のデータベース インスタンスが既に実行されている既存のサーバーに SAP IQ ソフトウェアをインストールすることはお勧めできません。 1 つの SAP IQ サーバーを複数の SAP-NLS 実装に使用できます。 

## <a name="support-matrix"></a>サポート マトリックス

このセクションでは、SAP IQ-NLS ソリューションのサポート マトリックスの概要について説明します。詳細については、このドキュメントで説明します。 また、お使いの SAP IQ リリースに基づく最新情報については、[SAP 製品の可用性マトリックス (PAM)](https://userapps.support.sap.com/sap/support/pam) を確認してください。 

- **オペレーティング システム**: SAP IQ はオペレーティング システム レベルでのみ認定されています。 SAP IQ の認定オペレーティング システムは、Azure インフラストラクチャで実行するための互換性がある場合に限り、Azure 環境で実行できます。 詳細については、SAP ノート [2133194](https://launchpad.support.sap.com/#/notes/2133194) をご覧ください。

- **SAP BW 互換性**: SAP IQ のニアライン ストレージは、Unicode で既に実行されている SAP BW システムに対してのみリリースされます。 SAP BW に関する情報が含まれている SAP ノート [1796393](https://launchpad.support.sap.com/#/notes/1796393) に従ってください。

- **ストレージ**: Azure では、Premium マネージド ディスク (Windows/Linux)、Azure 共有ディスク (Windows のみ)、および Azure NetApp Files (NFS - Linux のみ) が SAP IQ によってサポートされています。 

## <a name="sizing"></a>サイズ変更

SAP IQ のサイズ設定は、CPU、メモリ、およびストレージに限定されます。 Azure での SAP IQ の一般的なサイズ設定のガイドラインは、SAP ノート [1951789](https://launchpad.support.sap.com/#/notes/1951789) に記載されています。 ガイドラインに従うことで得られる推奨サイズは、SAP の認定済み Azure 仮想マシンの種類にマップする必要があります。 SAP ノート [1928533](https://launchpad.support.sap.com/#/notes/1928533) には、サポートされる SAP 製品と Azure VM の種類の一覧があります。 

> [!Tip]
>
> 生産性の高いシステムには、E シリーズ仮想マシンを使用することをお勧めします。これはそのコアとメモリの比率のためです。 

SAP ノート [1951789](https://launchpad.support.sap.com/#/notes/1951789) に記載されている SAP IQ サイズ設定ガイドとサイズ設定ワークシートは、SAP IQ データベースのネイティブ使用のために開発されており、<SID>IQ データベースの計画のためのリソースを反映していないので、SAP-NLS のリソースが使用されない可能性があります。

## <a name="azure-resources"></a>Azure リソース

### <a name="choosing-regions"></a>リージョンの選択

Azure で SAP システムを既に実行している場合、リージョンはおそらく既に特定されています。 SAP IQ は、NLS ソリューションを実装する SAP BWのリージョンと同じリージョンにデプロイする必要があります。 ただし、SAP IQ アーキテクチャを決定するために、Azure NetApp Files（NFS - Linux のみ）などの SAP IQ に必要なサービスがこれらのリージョンで利用可能であることを調査する必要があります。 ご利用のリージョンでのサービスの可用性は、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)サイトで確認できます。

### <a name="availability-sets"></a>可用性セット 

Azure インフラストラクチャで SAP システムの冗長性を実現するには、アプリケーションを可用性セットまたは可用性ゾーンにデプロイする必要があります。 技術的には、SAP IQ の高可用性は IQ マルチプレックス アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を満たしていません。 SAP IQ シンプレックス アーキテクチャの高可用性を実現するには、カスタム ソリューションを備えた 2 ノード クラスターを構成する必要があります。 この 2 ノード SAP IQ クラスターは、可用性セットまたは可用性ゾーンにデプロイできますが、デプロイ方法はノードに接続される Azure ストレージによって決まります。 現在、Azure 共有 Premium ディスクと Azure NetApp Files ではゾーン デプロイがサポートされていないため、SAP IQ のデプロイ オプションは、可能性セットのみになります。 

### <a name="virtual-machines"></a>仮想マシン

SAP IQ のサイズ設定に基づいて、Azure for SAP 製品でサポートされている Azure 仮想マシンに要件をマップする必要があります。 最初に SAP ノート [1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照することをお勧めします。ここには、Windows および Linux で SAP 製品に対してサポートされている Azure VM の種類が一覧で示されています。 また、サポートされている VM の種類の選択肢だけでなく、それらの VM の種類が特定のリージョンで使用できるかどうかを確認する必要がある点にも留意してください。 使用できる VM の種類は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」のページで確認できます。 価格モデルの選択については、「[SAP ワークロード用の Azure 仮想マシン](planning-guide.md#azure-virtual-machines-for-sap-workload)」を参照してください。 

生産性の高いシステムには、E シリーズ仮想マシンを使用することをお勧めします。これはそのコアとメモリの比率のためです。

### <a name="storage"></a>記憶域

Azure ストレージには、お客様が使用できるさまざまなストレージの種類があります。その詳細については、「[Azure で利用できるディスクの種類](../../disks-types.md)」を参照してください。 ストレージの種類の中には、SAP シナリオでの使用が制限されているものがあります。 しかし、いくつかの Azure Storage の種類が、特定の SAP ワークロードのシナリオ用として適切であるか、または最適化されています。 詳細については、[SAP ワークロードの Azure Storage の種類](planning-guide-storage.md)に関するガイドを参照してください。SAP に適したさまざまなストレージ オプションの概要が説明されています。 Azure 上の SAP IQ の場合、オペレーティング システム (Windows または Linux) とデプロイ方法 (スタンドアロンまたは高可用性) に基づいて、次の Azure ストレージを使用できます。

- Azure マネージド ディスク

  Azure によって管理されるブロック レベルのストレージ ボリュームです。 SAP IQ シンプレックス デプロイには、Azure マネージド ディスクを使用できます。 さまざまな種類の [Azure マネージド ディスク](../../managed-disks-overview.md) が利用可能ですが、SAP IQ には [Premium SSD](../../disks-types.md#premium-ssd) を使用することをお勧めします。 

- Azure 共有ディスク

  [Azure 共有ディスク](../../disks-shared.md)は、マネージド ディスクを複数の仮想マシン (VM) に同時に接続できるようにする Azure マネージド ディスクの新機能です。 共有されたマネージド ディスクでは、SMB または NFS を使用してアクセスできるフル マネージドのファイルシステムをネイティブに提供していません。 [Windows Server フェールオーバー クラスター](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC) のような、クラスター ノードの通信と書き込みロックを処理するクラスター マネージャーを使用する必要があります。 Windows に SAP IQ シンプレックス アーキテクチャ用の高可用性ソリューションをデプロイするには、WSFC によって管理される 2 つのノード間で Azure 共有ディスクを使用できます。 Azure 共有ディスクを使用した SAP IQ デプロイ アーキテクチャについては、「[Azure 共有ディスクを使用して SAP IQ NLS HA ソリューションを Windows Server にデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)」の記事で説明されています。

- Azure NetApp Files

  Linux での SAP IQ のデプロイでは、[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) をファイル システム (NFS プロトコル) として使用して、スタンドアロンまたは高可用性ソリューションとしてインストールできます。 このストレージ オファリングはすべてのリージョンで使用できるわけではないため、最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」のサイトを参照してください。 Azure NetApp Files を使用した SAP IQ デプロイ アーキテクチャについては、「[Azure NetApp Files を使用して SUSE Linux Enterprise Server で SAP IQ-NLS HA Solution をデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)」の記事で説明されています。

次の表に、オペレーティング システムに基づいてストレージの種類ごとの推奨事項を示します。 

| ストレージの種類        | Windows | Linux |
| ------------------- | ------- | ----- |
| Azure マネージド ディスク | はい     | はい   |
| Azure 共有ディスク  | はい     | いいえ    |
| Azure NetApp Files  | いいえ      | はい   |

### <a name="networking"></a>ネットワーク

Azure には、オンプレミス システムや、別の仮想ネットワーク内のシステムへの接続など、SAP IQ をニアライン ストレージとして使用する SAP BW システムで実現できるすべてのシナリオのマッピングを可能にするネットワーク インフラストラクチャが用意されています。 詳細については、[SAP ワークロード用の Microsoft Azure ネットワーク](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)に関するページを参照してください。

## <a name="deploy-sap-iq-on-windows"></a>Windows に SAP IQ をデプロイする

### <a name="server-preparation-and-installation"></a>サーバーの準備とインストール

SAP の最新のガイドに従って、SAP IQ を使用して Windows に NLS を実装するためのサーバーを準備します。 最新情報については、SAP が公開している最初のガイダンス ドキュメントを参照してください。これは、SAP ノート「[2780668 - SAP First Guidance - BW NLS Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668)」にあります。 これには、SAP BW システムの前提条件に関する包括的な情報、IQ ファイル システム レイアウト、インストール、構成後、および IQ との BW NLS の統合について説明されています。

### <a name="high-availability-deployment"></a>高可用性のデプロイ

SAP IQ では、シンプレックスとマルチプレックスの両方のアーキテクチャがサポートされています。 NLS ソリューションの場合、シンプレックス サーバー アーキテクチャのみが使用可能であり、評価されます。 シンプレックスは、1 つの仮想マシンで実行されている SAP IQ サーバーの 1 つのインスタンスです。 技術的には、SAP IQ の高可用性はマルチプレックス サーバー アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を満たしていません。 シンプレックス サーバー アーキテクチャに対して、SAP は高可用性構成で SAP IQ を実行するための機能や手順を提供していません。 

Windows でシンプレックス サーバー アーキテクチャに対して SAP IQ の高可用性を設定するには、カスタム ソリューションを設定する必要があります。これには、Microsoft Windows Server フェールオーバー クラスターや共有ディスクなどの追加の構成が必要です。 Windows 上の SAP IQ に対するこのようなカスタム ソリューションの 1 つが、ブログ記事「[Azure 共有ディスクを使用して SAP IQ NLS HA ソリューションを Windows Server にデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)」で詳しく説明されています。

### <a name="back-up-and-restore"></a>バックアップと復元

Azure では、[IQ の管理: バックアップ、復元、データの復旧](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)に関するページで SAP によって説明されているように、SAP IQ データベースのバックアップをスケジュールできます。 SAP IQ には、さまざまな種類のデータベース バックアップが用意されています。各バックアップの種類の詳細については、[バックアップ シナリオ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)に関するページを参照してください。

- 完全バックアップ - データベースの完全なコピーを作成します。 
- 増分バックアップ - 任意の種類の前回のバックアップ以降に行われたすべてのトランザクションをコピーします。 
- 完全バックアップ以降の増分 - 前回の完全バックアップ以降にデータベースに行われたすべての変更をバックアップします。
- 仮想バックアップ - IQ ストアのテーブル データとメタデータを除くすべてのデータベースをコピーします。

お使いの IQ データベースのサイズに応じて、任意のバックアップ シナリオからデータベースのバックアップをスケジュールできます。 ただし、SAP が提供する NLS インターフェイスで SAP IQ を使用している場合に、IQ データベースのバックアップ プロセスを自動化すると、プライマリ データベースと SAP IQ データベース間のデータ移動プロセスに関してデータ損失のない一貫性のある状態に SAP IQ データベースを復旧できます。 SAP ノート「[2741824 - How to setup backup automation for SAP IQ Cold Store/Near-line Storage](https://launchpad.support.sap.com/#/notes/2741824)」を参照してください。これには、SAP IQ ニアライン ストレージの自動化の設定に関する詳細が記載されています。 

大規模な IQ データベースの場合は、SAP IQ で仮想バックアップを使用できます。 仮想バックアップの詳細については、[仮想バックアップ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)および [SAP Sybase IQ での仮想バックアップの概要](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)に関するページと、SAP ノート「[2461985 - How to Backup Large SAP IQ Database](https://launchpad.support.sap.com/#/notes/0002461985)」を参照してください。

ネットワーク ドライブ (SMB プロトコル) を使用して Windows 上の SAP IQ サーバーをバックアップおよび復元する場合は、必ず UNC パスを使用してバックアップしてください。 バックアップまたは復元に UNC パスを使用する場合は、3 つのバックスラッシュ "\\\\\" が必要です。

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Linux に SAP IQ をデプロイする

### <a name="server-preparation-and-installation"></a>サーバーの準備とインストール

SAP の最新のガイドに従って、SAP IQ を使用して Linux に NLS を実装するためのサーバーを準備します。 最新情報については、SAP が公開している最初のガイダンス ドキュメントを参照してください。これは、SAP ノート「[2780668 - SAP First Guidance - BW NLS Implementation with SAP IQ](https://launchpad.support.sap.com/#/notes/0002780668)」にあります。 これには、SAP BW システムの前提条件に関する包括的な情報、IQ ファイル システム レイアウト、インストール、構成後、および IQ との BW NLS の統合について説明されています。

### <a name="high-availability-deployment"></a>高可用性のデプロイ

SAP IQ では、シンプレックスとマルチプレックスの両方のアーキテクチャがサポートされています。 NLS ソリューションの場合、シンプレックス サーバー アーキテクチャのみが使用可能であり、評価されます。 シンプレックスは、1 つの仮想マシンで実行されている SAP IQ サーバーの 1 つのインスタンスです。 技術的には、SAP IQ の高可用性はマルチプレックス サーバー アーキテクチャを使用して実現できますが、マルチプレックス アーキテクチャは NLS ソリューションの要件を十分に満たしていません。 シンプレックス サーバー アーキテクチャに対して、SAP は高可用性構成で SAP IQ を実行するための機能や手順を提供していません。 

Linux でシンプレックス サーバー アーキテクチャに対して SAP IQ の高可用性を設定するには、カスタム ソリューションを設定する必要があります。これには、pacemaker などの追加の構成が必要です。 Linux 上の SAP IQ に対するこのようなカスタム ソリューションの 1 つが、ブログ記事「[Azure NetApp Files を使用して SUSE Linux Enterprise Server で SAP IQ-NLS HA Solution をデプロイする](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)」で詳しく説明されています。

### <a name="back-up-and-restore"></a>バックアップと復元

Azure では、[IQ の管理: バックアップ、復元、データの復旧](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)に関するページで SAP によって説明されているように、SAP IQ データベースのバックアップをスケジュールできます。 SAP IQ には、さまざまな種類のデータベース バックアップが用意されています。各バックアップの種類の詳細については、[バックアップ シナリオ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)に関するページを参照してください。

- 完全バックアップ - データベースの完全なコピーを作成します。 
- 増分バックアップ - 任意の種類の前回のバックアップ以降に行われたすべてのトランザクションをコピーします。 
- 完全バックアップ以降の増分 - 前回の完全バックアップ以降にデータベースに行われたすべての変更をバックアップします。
- 仮想バックアップ - IQ ストアのテーブル データとメタデータを除くすべてのデータベースをコピーします。

IQ データベースのサイズに応じて、データベースのバックアップをスケジュールできます。 ただし、SAP が提供する NLS インターフェイスで SAP IQ を使用している場合に、IQ データベースのバックアップ プロセスを自動化すると、プライマリ データベースと SAP IQ データベース間のデータ移動プロセスに関してデータ損失のない状態に SAP IQ データベースを復旧できます。 SAP ノート「[2741824 - How to setup backup automation for SAP IQ Cold Store/Near-line Storage](https://launchpad.support.sap.com/#/notes/2741824)」を参照してください。これには、SAP IQ ニアライン ストレージの自動化の設定に関する詳細が記載されています。 

大規模な IQ データベースの場合は、SAP IQ で仮想バックアップを使用できます。 仮想バックアップの詳細については、[仮想バックアップ](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)および [SAP Sybase IQ での仮想バックアップの概要](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)に関するページと、SAP ノート「[2461985 - How to Backup Large SAP IQ Database](https://launchpad.support.sap.com/#/notes/0002461985)」を参照してください。

## <a name="disaster-recovery"></a>障害復旧

このセクションでは、SAP IQ - NLS ソリューションにディザスター リカバリー (DR) 保護を提供するための戦略について説明します。 これにより、SAP の DR アプローチ全体の主要なリソースである [SAP のディザスター リカバリー](../../../site-recovery/site-recovery-sap.md)に関するドキュメントを補完します。 このドキュメントで説明されているプロセスは、抽象的なレベルで示されています。 しかし、正確な手順を検証し、DR 戦略の徹底的なテストを行う必要があります。 

SAP IQ については、SAP ノート [2566083](https://launchpad.support.sap.com/#/notes/0002566083) を参照してください。これには、DR 環境を安全に実装するための方法が説明されています。 Azure では、SAP IQ DR 戦略に [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用することもできます。 SAP IQ DR の戦略は、Azure でのデプロイ方法によって異なります。また、お使いの SAP BW システムと一致している必要があります。 

- SAP IQ のスタンドアロン デプロイ

  アプリケーション レベルの冗長性も高可用性も備えていないスタンドアロン システムとして SAP IQ をインストールしたが、ビジネスには DR のセットアップが必要な場合。 スタンドアロンの IQ システムでは、仮想マシンに接続されているすべてのディスク (Azure マネージド ディスク) がローカルになります。 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用すると、セカンダリ リージョンでスタンドアロンの SAP IQ 仮想マシンをレプリケートできます。 災害や障害が発生したときに、レプリケートされた環境に簡単にフェールオーバーして作業を続けることができるように、サーバーとアタッチされているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされます。 Azure DR リージョンへの SAP IQ VM のレプリケートを開始するには、[仮想マシンの Azure へのレプリケート](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)に関する記事のガイダンスに従います。 

- SAP IQ の高可用性デプロイ

  IQ バイナリとデータベース ファイルが Azure 共有ディスク (Windows のみ) または Azure NetApp Files (Linux のみ) などのネットワーク ドライブにある、高可用性システムとして SAP IQ をインストールした場合。 このようなセットアップでは、DR サイトで同じ高可用性 SAP IQ が必要であるか、またはビジネス要件を満たすにはスタンドアロン SAP IQ で十分であるかを判断する必要があります。 DR サイトでスタンドアロンの SAP IQ が必要な場合は、[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用してセカンダリ リージョンでプライマリ SAP IQ 仮想マシンをレプリケートできます。 これを使用すると、サーバーと、ローカルに接続されているすべてのマネージド ディスクがセカンダリ リージョンにレプリケートされますが、Azure 共有ディスクまたは Azure NetApp Files などのネットワーク ドライブはレプリケートされません。 Azure 共有ディスクまたはネットワーク ドライブからデータをコピーするには、任意のファイルベースのコピー ツールを使用して、Azure リージョン間でデータをレプリケートします。 別のリージョンにある Azure NetApp Files ボリュームをコピーする方法の詳細については、「[Azure NetApp Files についての FAQ](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)」を参照してください。

## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
