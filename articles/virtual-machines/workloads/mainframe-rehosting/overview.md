---
title: Azure 仮想マシンでのメインフレーム リホスト | Microsoft Docs
description: Microsoft Azure の仮想マシン (VM) を使用して、IBM Z ベースのシステムなどのメインフレーム ワークロードをリホストします。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192719"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 仮想マシンでのメインフレーム リホスト

ワークロードをメインフレーム環境からクラウドに移行すると、インフラストラクチャを最新化したり、多くの場合はコストを節約したりできます。 ワークロードの多くは、データベースの名前を更新するなど、コードを少し変更するだけで Azure に転送できます。

*メインフレーム*という用語は一般に大型のコンピューター システムを指しますが、現在デプロイされている大多数は IBM System Z サーバーか、または MVS、DOS、VSE、OS/390、z/OS を実行している IBM プラグ互換システムです。

Azure 仮想マシン (VM) は、1 つのインスタンス上で特定のアプリケーションのリソースを分離および管理するために使用されます。 IBM z/OS などのメインフレームは、この目的のために論理パーティション (LPAR) を使用します。 メインフレームでは、関連付けられた COBOL プログラムを含む CICS 領域のために 1 つの LPAR が、IBM DB2 データベースのために別の LPAR が使用されることがあります。 標準的な [Azure 上の N 層アプリケーション](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)は、Azure VM を層ごとのサブネットにセグメント化できる仮想ネットワークにデプロイします。

Azure VM は、リフトアンドシフト シナリオをサポートするメインフレーム エミュレーション環境およびコンパイラを実行できます。 開発やテストは多くの場合、メインフレームから Azure の開発/テスト環境に移行する最初のワークロードに含まれます。 次の図に示すように、エミュレートできる一般的なサーバー コンポーネントには、オンライン トランザクション処理 (OLTP)、バッチ、およびデータ インジェスト システムが含まれます。

![Azure 上のエミュレーション環境では、z/OS ベースのシステムを実行できます。](media/01-overview.png)

一部のメインフレーム ワークロードが比較的容易に Azure に移行できるのに対して、その他は、パートナー ソリューションを使用して Azure でリホストできます。 パートナー ソリューションの選択に関する詳細なガイダンスについては、[Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)が役立ちます。

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Micro Focus リホスト プラットフォームを使用して開発/テスト環境を設定する

Micro Focus Enterprise Server は、使用可能な最大のメインフレーム リホスト プラットフォームの 1 つです。 これを使用すると、Azure 上のより安価な x86 プラットフォームで z/OS ワークロードを実行できます。

開始するには、次の記事を参照してください。

- [Azure で Micro Focus Enterprise Server 4.0 および Enterprise Developer 4.0 をインストールする](./microfocus/set-up-micro-focus-on-azure.md)
- [Azure で Micro Focus Enterprise Developer 4.0 用の Micro Focus CICS BankDemo を設定する](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>Azure 上の TmaxSoft OpenFrame

TmaxSoft OpenFrame は、既存のメインフレーム資産をリフトし、それを Azure にシフトすることを容易にする、一般的なメインフレーム リホスト ソリューションです。 Azure 上の OpenFrame 環境は、開発、デモ、テスト、または運用環境のワークロードに適しています。

開始するには、「[Azure で TmaxSoft OpenFrame をインストールする](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)」の電子書籍をダウンロードします。

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>IBM Z Dev/Test 12.0 を使用して開発/テスト環境を設定する

IBM Z Development and Test Environment (IBM zD&T) は、z/OS ベースのアプリケーションの開発、テスト、およびデモに使用できる非運用環境を Azure 上に設定します。

Azure 上のエミュレーション環境は、Application Developers Controlled Distribution (ADCD) を使用して、さまざまな Z インスタンスをホストできます。 Azure および Azure Stack 上で zD&T Personal Edition、zD&T Parallel Sysplex、および zD&T Enterprise Edition を実行できます。

開始するには、次の記事を参照してください。

-   [Azure で IBM zD&T 12.0 を設定する](./ibm/install-ibm-z-environment.md)
-   [zD&T で ADCD を設定する](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>IBM DB2 pureScale を Azure に移行する

IBM DB2 pureScale 環境は、Linux オペレーティング システム上で高可用性とスケーラビリティを備えた Azure 向けデータベース クラスターを提供します。 元の環境と同一ではありませんが、Linux 上の IBM DB2 pureScale は、メインフレーム上の Parallel Sysplex 構成で実行される z/OS 向けの IBM DB2 とほぼ同等の高可用性とスケーラビリティの機能を提供します。

開始するには、「[Azure 上の IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)」を参照してください。

## <a name="considerations"></a>考慮事項

メインフレーム ワークロードを Azure IaaS (サービスとしてのインフラストラクチャ) に移行する場合は、Azure VM を含む、何種類かのオンデマンドでスケーラブルなコンピューティング リソースから選択できます。 Azure は、さまざまな [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) および [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) VM を提供しています。

### <a name="high-availability-and-failover"></a>高可用性とフェールオーバー

Azure は、コミットメント ベースのサービス レベル アグリーメント (SLA) を提供します。ここでは、ローカルまたは geo ベースのサービスのレプリケーションによって最適化された、複数ナインの可用性が既定になっています。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

Azure IaaS (VM など) の場合、フェールオーバーは、フェールオーバー クラスタリング インスタンスや[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)などの特定のシステム機能に依存します。 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) や [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) などの Azure PaaS (サービスとしてのプラットフォーム) リソースを使用する場合は、プラットフォームがフェールオーバーを自動的に処理します。

### <a name="scalability"></a>スケーラビリティ

メインフレームは通常スケールアップしますが、クラウド環境はスケールアウトします。Azure は、ユーザーのニーズを満たすために、さまざまな [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) および [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) のサイズを提供しています。 クラウドはまた、正確なユーザー仕様に一致するようにスケールアップまたはスケールダウンします。 コンピューティング能力、ストレージ、およびサービスは、使用量ベースの課金モデルのもとで、オンデマンドで[スケール調整](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling)されます。

### <a name="storage"></a>Storage

クラウドには、柔軟でスケーラブルなストレージ オプションがあり、必要な分だけ支払うことができます。 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) は、データ オブジェクトのための高度にスケーラブルなオブジェクト ストア、クラウドのためのファイル システム サービス、信頼性の高いメッセージング ストア、および NoSQL ストアを提供します。 VM の場合、マネージド ディスクとアンマネージド ディスクは、永続的でセキュリティで保護されたディスク ストレージを提供します。

### <a name="backup-and-recovery"></a>バックアップと回復

独自のディザスター リカバリー サイトを維持することは、高価な提案になる場合があります。 Azure では、[バックアップ](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)、[復旧](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)、および[冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)のための、実装が容易でコスト効率に優れたオプションをローカルまたはリージョン レベルで、または geo 冗長性経由で使用できます。

## <a name="azure-government-for-mainframe-migrations"></a>メインフレーム移行のための Azure Government

多くの公的機関は、そのメインフレーム アプリケーションをより現代的で柔軟なプラットフォームに移行したいと考えています。 Microsoft Azure Government は、グローバルな Microsoft Azure プラットフォームに基づいてはいるが、連邦政府、州政府、および地方政府のシステム向けにパッケージ化された、クラウド サービスの物理的に分離されたインスタンスです。 これにより、特に米国政府機関やそのパートナーに世界レベルのセキュリティ、保護、およびコンプライアンス サービスが提供されます。

Azure Government は、この種類の環境が必要なシステムに対して FedRAMP High Impact の Provisional Authority to Operate (P-ATO) を獲得しました。 

開始するには、[メインフレーム アプリケーション用の Microsoft Azure Government クラウド](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)をダウンロードします。

## <a name="learn-more"></a>詳細情報

メインフレーム移行を考慮している場合は、当社の[パートナー](partner-workloads.md) エコシステムを使用すると役立ちます。 パートナー ソリューションの選択に関する詳細なガイダンスについては、「[Platform Modernization Alliance (プラットフォーム最新化アライアンス)](https://www.platformmodernization.org/pages/mainframe.aspx)」を参照してください。

関連項目:

-   [メインフレーム移行](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [トラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [メインフレームから Azure への移行の解明](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
