---
title: Azure Site Recovery の最新情報 | Microsoft Docs
description: Azure Site Recovery で導入された新機能の概要を紹介します
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211119"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery の最新情報

[Azure Site Recovery](site-recovery-overview.md) サービスは、継続的に更新され、改善されています。 最新情報を入手し続ける助けになるように、この記事では、最新のリリース、新機能、および新しいコンテンツに関する情報を提供します。 このページは定期的に更新されます。

Site Recovery の機能について提案がおありの場合は、ぜひ[フィードバックをお聞かせください](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>Linux サポート

[更新プログラム ロールアップ 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) では、Site Recovery のエージェントとプロバイダーに対する更新プログラムが提供されます。 更新プログラムでは、次のように Linux のサポートが追加されます。

- **Azure VM のディザスター リカバリー**: Ubuntu、Debian、および SUSE 用の RedHat Workstation 6/7 の新しいカーネル バージョン。
- **Azure への VMware VM/物理サーバーのディザスター リカバリー**: RedHat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10/7.6 の新しいカーネルバージョン (Ubuntu、Debian、SUSE 用)。



## <a name="q4-2018"></a>Q4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリー用の料金計算ツール

Azure VM のディザスター リカバリーでは、VM のライセンス コストのほか、ネットワークとストレージのコストが発生します。 Azure では、これらのコストの把握に役立つ[料金計算ツール](https://aka.ms/a2a-cost-estimator)を提供しています。 Site Recovery では、6 個の VM、12 個の Standard HDD ディスク、6 個の Premium SSD ディスクを使用する 3 層アプリに基づいたサンプル デプロイに値段を付ける、[価格見積もりの例](https://aka.ms/a2a-cost-estimator)が提供されるようになりました。 このサンプルは、1 日に Standard では 10 GB、Premium では 20 GB のデータ変更を想定しています。 特定のデプロイについては、変数を変更してコストを見積もることができます。 VM の数、マネージド ディスクの数と種類、VM 全体で予想される総データ変更率を指定します。 さらに、帯域幅コストを見積もるための圧縮率を適用できます。 お知らせを[読む](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)

### <a name="support-for-azure-vms-in-zones"></a>ゾーンでの Azure VM のサポート

Azure 可用性ゾーンは、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 Azure VM のレプリケーションを有効にして、フェールオーバーのターゲットを、1 つの VM インスタンス、可用性セット内の VM、または可用性ゾーン内の VM に設定できるようになりました。 この設定は、レプリケーションに影響を与えません。 お知らせを[読む](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)
 
### <a name="disaster-recovery-for-encrypted-vms"></a>暗号化された VM のディザスター リカバリー

Site Recovery は、Azure AD アプリによる Azure Disk Encryption (ADE) で暗号化された Azure VM をサポートしています。 [詳細情報](azure-to-azure-how-to-enable-replication-ade-vms.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>高速ネットワークを使用した VM のためのディザスター リカバリー

高速ネットワークによって、VM へのシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが向上します。 Azure VM のレプリケーションを有効にすると、Site Recovery は、高速ネットワークが有効になっているかどうかを検出します。 有効な場合はフェールオーバー後に、[Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) と [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) の両方について、Site Recovery によってターゲットのレプリカ Azure VM 上に高速ネットワークが自動的に構成されます。 [詳細情報](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="automatic-updates-for-the-mobility-service-extension"></a>モビリティ サービス拡張機能の自動更新

Site Recovery では、モビリティ サービス拡張機能に対する自動更新のオプションが追加されました。 モビリティ サービス拡張機能は、Site Recovery によってレプリケートされる各 Azure VM にインストールされます。 レプリケーションを有効にするときには、拡張機能に対する更新の管理を Site Recovery に許可するかどうかを選択します。 更新では VM の再起動は必要とされず、レプリケーションに影響はありません。 [詳細情報](azure-to-azure-autoupdate.md)。

### <a name="support-for-standard-ssd-disks"></a>Standard SSD ディスクのサポート

Azure では、一貫したパフォーマンスは必要であっても、ディスク IOPS は高くない Web サーバーなどのアプリのために、コスト効率が高いストレージ ソリューションを提供するため、[Standard ソリッド ステート ドライブ (SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) ディスクが導入されました。 これは、Premium SSD と Standard HDD ディスクの要素を兼ね備えています。 Site Recovery では、Standard SSD ディスクを使用して Azure VM のディザスター リカバリーが提供されます。 既定では、ディスクの種類は、ターゲット リージョンへのフェールオーバー後に保持されます。

### <a name="support-for-azure-storage-firewall"></a>Azure ストレージ ファイアウォールのサポート

アカウントに対してファイアウォール規則を有効にすると、特定の一連のネットワークにアクセスする Azure ストレージ アカウントをセキュリティで保護できます。 既定では、内部ネットワークとインターネットからのトラフィックを拒否するようにストレージ アカウントを構成し、特定の VNet からのトラフィックへのアクセスを許可します。 Site Recovery は、ファイアウォールが有効なストレージ アカウントで、アンマネージド ディスクがある VM のセカンダリ リージョンへのレプリケーションをサポートしています。 ターゲット リージョンでは、アンマネージド ディスクについては、ファイアウォールが有効になっているストレージ アカウントを選択できます。 ソース VM が配置されているネットワークのみにネットワーク アクセスを制限することで、キャッシュ ストレージ アカウントへのアクセスを制限することもできます。 信頼できる Microsoft サービスについては、[アクセスを許可する](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)必要があることに注意してください。

## <a name="q3-2018"></a>Q3 2018 

### <a name="linux-support"></a>Linux サポート

#### <a name="update-rollup-28"></a>更新プログラム ロールアップ 28

[更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) では、Site Recovery のエージェントとプロバイダーに対する更新プログラムが提供されます。 更新プログラムでは、次のように Linux のサポートが追加されます。

- **Azure VM のディザスター リカバリー**: Red Hat Enterprise Linux 6.10、CentOS 6.10、レガシ BIOS 互換モードで GUID パーティション テーブル (GPT) のパーティション スタイルを使用する Linux ベースの VM がサポートされるようになりました。
- **Azure への VMware VM/物理サーバーのディザスター リカバリー**: Red Hat Enterprise Linux 6.10、CentOS 6.10、レガシ BIOS 互換モードで GUID パーティション テーブル (GPT) のパーティション スタイルを使用する Linux ベースの VM がサポートされるようになりました。

#### <a name="update-rollup-27"></a>更新プログラム ロールアップ 27

[更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) では、Site Recovery のエージェントとプロバイダーに対する更新プログラムが提供されます。 更新プログラムでは、次のように Linux のサポートが追加されます。

- **Azure VM のディザスター リカバリー**: Red Hat Enterprise Linux 7.5
- **Azure への VMware VM/物理サーバーのディザスター リカバリー**: SUSE Linux Enterprise Server 12、Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Windows Server 2016 で実行されている Azure VM のサポート

Windows Server 2016 で実行される Azure VM は、Azure Site Recovery によって、Azure リージョン間でレプリケートすることができます。

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>記憶域スペース ダイレクトを実行している Azure VM のサポート

[記憶域スペース ダイレクト](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (Windows Server 2016 以降で使用可能) は、ドライブを記憶域プールにグループ化し、その後、プールの容量を使用して記憶域スペースを作成します。 記憶域スペースは、スタンドアロン VM、各クラスター ノードでローカル ストレージを使用する [Azure VM のゲスト クラスター](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)、またはクラスター全体にわたる共有ストレージで使用できます。

## <a name="next-steps"></a>次の手順

[Azure の更新情報](https://azure.microsoft.com/updates/?product=site-recovery)に関するページで、更新プログラムの最新情報を常に把握してください。


