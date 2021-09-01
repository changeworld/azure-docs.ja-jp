---
title: Azure Migrate での評価のトラブルシューティング
description: Azure Migrate での評価に関するヘルプ。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: 88b85ac7849ada7eb854b84fe38787de49ad2b41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737677"
---
# <a name="troubleshoot-assessment"></a>評価に関するトラブルシューティング

この記事は、[[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) による評価および依存関係の視覚化で発生した問題をトラブルシューティングする際に役立ちます。

## <a name="assessment-readiness-issues"></a>アセスメントの準備状態

評価の準備状態を修正するには、次のようにします。

**問題点** | **解決策**
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用して、そのような VM の移行を処理することができます。 それにより、移行中に VM のブートの種類が BIOS に変換されます。
条件付きでサポートされる Windows オペレーティング システム | オペレーティング システムはサポート期間が終了しており、[Azure でのサポート](/troubleshoot/azure/virtual-machines/server-software-support)のためにはカスタム サポート契約 (CSA) が必要です。 Azure に移行する前にアップグレードを検討してください。 Azure への移行に向けた [Windows Server 2003 が実行されるサーバーの準備](prepare-windows-server-2003-migration.md)に関する情報について確認します。
サポートされていない Windows オペレーティング システム | Azure では、[特定の Windows OS バージョン](/troubleshoot/azure/virtual-machines/server-software-support)のみがサポートされています。 Azure に移行する前にサーバーのアップグレードを検討してください。
条件付きで動作が保証されている Linux OS | Azure では、[特定の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみがサポートされます。 Azure に移行する前にサーバーのアップグレードを検討してください。 詳細については、[こちらも](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)参照してください。
動作が保証されていない Linux OS | サーバーは Azure で起動する可能性がありますが、オペレーティング システムは Azure ではサポートされていません。 Azure に移行する前に、[サポートされている Linux バージョン](../virtual-machines/linux/endorsed-distros.md)へのアップグレードを検討してください。
オペレーティング システムが不明です | VM のオペレーティング システムが、vCenter Server で "その他" として指定されました。 この動作により、Azure Migrate による VM の Azure 対応性の検証はブロックされます。 サーバーを移行する前に、オペレーティング システムが Azure によって[サポートされている](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements)ことを確認してください。
サポートされていないビット バージョン | 32 ビット オペレーティング システムの VM は Azure で起動する可能性がありますが、Azure に移行する前に、64 ビットにアップグレードすることをお勧めします。
Microsoft Visual Studio のサブスクリプションが必要です | サーバーで実行されている Windows クライアント オペレーティング システムは、Visual Studio のサブスクリプションによってのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりません | そのサーバーに必要なストレージ パフォーマンス (1 秒あたりの入力/出力操作数 (IOPS) とスループット) が Azure VM のサポート範囲を超えています。 移行前に、そのサーバーのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりません | そのサーバーに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのサーバーのネットワーク要件を緩和します。
指定した場所で VM が見つかりません | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります | VM に接続されている 1 つ以上のディスクで、Azure の要件が満たされていません。<br/><br/> Azure Migrate: Discovery and assessment では、Ultra Disk のディスク制限 (64 TB) に基づいてディスクが評価されます。<br/><br/> VM に接続されている各ディスクについて、ディスクのサイズが (Ultra SSD ディスクでサポートされている) 64 TB 未満であることを確認します。<br/><br/> そうでない場合は、Azure に移行する前にディスクのサイズを減らすか、または Azure で複数のディスクを使用して[ストライピングし](../virtual-machines/premium-storage-performance.md#disk-striping)、ストレージの上限を高くします。 各ディスクで必要とされるパフォーマンス (IOPS とスループット) が Azure の[仮想マシンの管理ディスク](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)でサポートされていることを確認してください。
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、サーバーから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、サーバーから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure Migrate: Discovery and assessment では、最大 64 TB のサイズ (Ultra Disk) のディスクがサポートされます。 移行の前にディスクを 64 TB 未満に圧縮するか、または Azure で複数のディスクを使用して[ストライピングし](../virtual-machines/premium-storage-performance.md#disk-striping)、ストレージの上限を高くします。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのサーバーのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
オファー、通貨、予約インスタンスに対する VM サイズが見つかりませんでした | 選択された RI、オファー、通貨の組み合わせに対して VM サイズが見つからなかったため、サーバーは不適切とマークされました。 評価のプロパティを編集して有効な組み合わせを選択し、評価を再計算します。 
条件付きで対応しているインターネット プロトコル | Azure VMware Solution (AVS) の評価にのみ適用されます。 AVS では、IPv6 インターネット アドレスのファクターはサポートされていません。 サーバーが IPv6 を使用して検出される場合の修復については、AVS チームにお問い合わせください。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>不明とマークされるインポート ベースの AVS 評価で推奨される移行ツール

CSV ファイルを介してインポートされたサーバーの場合、AVS 評価での既定の移行ツールは不明です。 ただし VMware 環境内のサーバーの場合は、VMware Hybrid Cloud Extension (HCX) ソリューションを使用することをお勧めします。 [詳細については、こちらを参照してください](../azure-vmware/configure-vmware-hcx.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM が Azure VM 評価で "条件付きで対応" になる

VMware VM および Hyper-V VM の場合、Azure VM 評価の既知のギャップのため、Linux VM が "条件付きで対応" とマークされます。 

- ギャップのため、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できません。
- たとえば、RHEL 6.10 の場合、現在の Azure VM 評価では、RHEL 6 のみが OS バージョンとして検出されます。 これは、Hyper-V ホストの vCenter Server では、Linux VM オペレーティング システム用のカーネル バージョンが提供されていないためです。
- 特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Azure VM 評価において条件付き対応とマークされます。
- オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート](../virtual-machines/linux/endorsed-distros.md)のページを参照して特定できます。
- ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

このギャップは、VMware VM で[アプリケーション検出](./how-to-discover-applications.md)を有効にすることで対処できます。 Azure VM 評価では、指定されたゲスト資格情報を使用して VM から検出されたオペレーティング システムが使用されます。 このオペレーティング システムのデータでは、Windows VM と Linux VM の両方の場合に適切な OS 情報が示されます。

## <a name="operating-system-version-not-available"></a>オペレーティング システムのバージョンを入手できない

物理サーバーの場合、オペレーティング システムのマイナーバージョン情報が入手可能であるはずです。 入手できない場合は、Microsoft サポートにお問い合わせください。 VMware 環境内のサーバーの場合、Azure Migrate では、vCenter Server でその VM に対して指定されているオペレーティング システム情報を使用します。 ただし、vCenter Server は、オペレーティング システムのマイナー バージョンを提供しません。 マイナー バージョンを検出するには、[アプリケーション検出](./how-to-discover-applications.md)を設定する必要があります。 Hyper-V VM の場合、オペレーティング システムのマイナーバージョンの検出はサポートされていません。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 評価でオンプレミスより大きい Azure SKU

Azure VM 評価では、評価の種類に基づいて、現在のオンプレミスの割り当てより多くのコアとメモリを備えた Azure VM SKU が推奨される場合があります。

- VM SKU の推奨は、評価のプロパティによって異なります。
- これは、Azure VM 評価で実行する評価の種類によって影響を受けます: "*パフォーマンスベース*" または "*オンプレミス*"。
- "パフォーマンス ベース" の評価では、Azure VM 評価によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 また、有効な使用率を決定するときに快適性係数が追加されます。
- オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスの割り当てに基づいてターゲット SKU が推奨されます。

これが推奨に与える影響を確認するため、次の例を見てみましょう。

オンプレミスの VM は、4 つのコアと 8 GB のメモリを備え、CPU 使用率は 50%、メモリ使用率は 50%、指定された快適性係数は 1.3 です。

- 評価が **オンプレミス** の場合は、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
- 評価がパフォーマンスベースの場合は、CPU およびメモリの有効な使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づき、4 コア (最も近いサポートされるコア数) と 8 GB メモリ (最も近いサポートされるメモリ サイズ) の最も安価な VM SKU が推奨されます。
- 評価のサイズ決定に関する[詳細を参照](concepts-assessment-calculation.md#types-of-assessments)してください。

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM の評価で、オンプレミスのものより大きい Azure ディスク SKU が推奨されるのはなぜですか?

Azure VM 評価では、評価の種類に基づいて、より大きいディスクが推奨される場合があります。

- ディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
- サイズ設定基準が **パフォーマンス ベース** で、ストレージの種類が **自動** に設定されている場合は、ターゲット ディスクの種類 (Standard HDD、Standard SSD、Premium、または Ultra Disk) が決定されるときに、ディスクの IOPS とスループットの値が考慮されます。 その後、そのディスクの種類からディスク SKU が推奨され、この推奨ではオンプレミスのディスクのサイズ要件が考慮されます。
- サイズ設定基準が **パフォーマンスベース** で、ストレージの種類が **Premium** である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ設定基準が **オンプレミス** で、ストレージの種類が **Standard HDD**、**Standard SSD**、**Premium**、または **Ultra Disk** である場合は、同じロジックを使ってディスクのサイズ設定が行われます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Azure VM 評価では Premium ディスク (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 オンプレミスのディスクで必要なサイズは 32 GB でしたが、オンプレミスのディスクの高い IOPS 要件により、Azure VM 評価ではさらに大きいディスクが推奨されます。

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>評価レポートで一部または全部の VM のパフォーマンス データが見つからないのはなぜですか?

"パフォーマンス ベース" の評価では、Azure Migrate アプライアンスでオンプレミス VM のパフォーマンス データを収集できない場合、評価レポートのエクスポートに "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" と表示されます。 次の点を確認してください。

- 評価を作成している期間中に VM の電源がオンになっていたかどうか
- メモリ カウンターのみが取得されず、Hyper-V VM を評価しようとしている場合は、これらの VM で動的メモリが有効になっているかどうかを確認してください。 現在、既知の問題のため、そのような VM のメモリ使用率は、Azure Migrate アプライアンスで収集できません。
- すべてのパフォーマンス カウンターが不足している場合は、評価のためのポート アクセス要件が満たされていることを確認します。 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、[Hyper-V](./migrate-support-matrix-hyper-v.md#port-access)、および[物理](./migrate-support-matrix-physical.md#port-access)評価のためのポート アクセス要件の詳細を参照してください。
注 - いずれかのパフォーマンス カウンターを取得できない場合、Azure Migrate: Discovery and assessment はオンプレミスの割り当てられたコアまたはメモリにフォールバックし、それに応じて VM サイズが推奨されます。

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Azure VM や AVS の評価レポートに、一部またはすべてのサーバーに関するパフォーマンス データがないのはなぜですか?

"パフォーマンス ベース" の評価では、Azure Migrate アプライアンスでオンプレミスサーバーのパフォーマンス データを収集できない場合、評価レポートのエクスポートに "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" と表示されます。 次の点を確認してください。

- 評価を作成している期間中にサーバーの電源がオンになっていたかどうか
- メモリ カウンターのみ取得されず、Hyper-V 環境内のサーバーを評価しようとしていたかどうか。 このシナリオでは、サーバーの動的メモリを有効にし、評価を "再計算" して最新の変更内容を反映してください。 アプライアンスは、サーバーで動的メモリが有効になっている場合にのみ、Hyper-V 環境内のサーバーのメモリ使用率値を収集できます。

- すべてのパフォーマンス カウンターがない場合は、ポート 443 (HTTPS) での発信接続が許可されていることを確認します。

    > [!Note]
    > いずれかのパフォーマンス カウンターを取得できない場合、Azure Migrate: Discovery and assessment はオンプレミスの割り当てられたコアまたはメモリにフォールバックし、それに応じて VM サイズが推奨されます。

## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Azure SQL の評価に、一部またはすべての SQL インスタンスまたはデータベースに関するパフォーマンス データがないのはなぜですか?

パフォーマンス データが収集されるようにするには、次のことを確認してください。

- 評価を作成している期間中に SQL サーバーの電源がオンになっていたかどうか
- Azure Migrate で SQL エージェントの接続状態が "接続済み" かどうか、また、最後のハートビートも確認します 
- 検出された SQL インスタンスのブレードで、すべての SQL インスタンスの Azure Migrate の接続状態が "接続済み" かどうか
- すべてのパフォーマンス カウンターがない場合は、ポート 443 (HTTPS) での発信接続が許可されていることを確認します

パフォーマンス カウンターのいずれかが欠落している場合、Azure SQL 評価により、そのインスタンスまたはデータベースに対して最小限の Azure SQL 構成が推奨されます。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>評価の信頼度レーティングが低いのはなぜですか?

信頼度評価は、評価を計算するために必要な[使用可能データ ポイント](./concepts-assessment-calculation.md#ratings)の割合に基づいて、"パフォーマンス ベース" の評価に対して計算されます。 評価の信頼度レーティングが低い理由は以下のとおりです。

- 評価を作成するための期間用の環境をプロファイルしませんでした。 たとえば、パフォーマンス期間を 1 週間に設定した評価を作成する場合は、すべてのデータポイントが収集されるまで、検出を始めてから少なくとも 1 週間待つ必要があります。 その期間待つことができない場合は、パフォーマンス期間を短くし、評価を **再計算** してください。
- 評価期間内に一部または全部のサーバーのパフォーマンス データを評価で収集できません。 高い信頼度レーティングを得るために、次のことを確認してください。 
    - 評価期間中、サーバーの電源がオンになっている
    - ポート 443 でのアウトバウンド接続が許可されている
    - Hyper-V サーバーで、動的メモリが有効になっている
    - Azure Migrate のエージェントの接続状態が "接続済み" である、また、最後のハートビートも確認します
    - Azure SQL 評価の検出された SQL インスタンスのブレードで、すべての SQL インスタンスの Azure Migrate の接続状態が "接続済み" である

    評価を **再計算** し、信頼性評価に最新の変更を反映してください。

- Azure VM および AVS の評価では、検出の開始後に作成されたサーバーはほとんどありませんでした。 たとえば、過去 1 か月間のパフォーマンス履歴の評価を作成しているのに、ほんの 1 週間前にいくつかのサーバーが環境内に作成されたとします。 この場合、新しいサーバーのパフォーマンス データは期間全体を通しては利用できず、信頼度レーティングが低くなります。 [詳細情報](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL の評価の場合、検出が開始された後で、いくつかの SQL インスタンスまたはデータベースが作成されました。 たとえば、過去 1 か月間のパフォーマンス履歴の評価を作成しているのに、ほんの 1 週間前にいくつかの SQL インスタンスまたはデータベースが環境内に作成されたとします。 この場合、新しいサーバーのパフォーマンス データは期間全体を通しては利用できず、信頼度レーティングが低くなります。 [詳細情報](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>オペレーティング システムのライセンスは Azure VM 評価に含まれていますか?

現在、Azure VM 評価では、Windows サーバーのオペレーティング システムのライセンス コストのみが考慮されます。 Linux サーバーのライセンス コストは現在は考慮されません。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Azure VM 評価ではパフォーマンス ベースのサイズ設定はどのように動作しますか?

Azure VM 評価では、オンプレミスのサーバーのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 パフォーマンスベースのデータの収集方法を[確認](concepts-assessment-calculation.md#calculate-sizing-performance-based)してください。

## <a name="can-i-migrate-my-disks-to-ultra-disk-using-azure-migrate"></a>Azure Migrate を使用してディスクを Ultra Disk に移行できますか?

いいえ。 現時点では、Azure Migrate と Azure Site Recovery の両方で、Ultra Disk への移行はサポートされていません。 Ultra Disk をデプロイする手順については、[こちら](https://docs.microsoft.com/azure/virtual-machines/disks-enable-ultra-ssd?tabs=azure-portal#deploy-an-ultra-disk)を参照してください

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>Ultra Disk でプロビジョニングされた IOPS とスループットがオンプレミスの IOPS とスループットを超えるのはなぜですか?

Ultra Disk は、[公式価格のページ](https://azure.microsoft.com/pricing/details/managed-disks/)に従い、プロビジョニングされたサイズ、プロビジョニングされた IOPS、プロビジョニングされたスループットに基づいて課金されます。 例: 200 GiB、20,000 IOPS、1,000 MB/秒の Ultra Disk をプロビジョニングし、20 時間後に削除した場合、256 GiB のディスク サイズ プランにマップされ、20 時間分の 256 GiB、20,000 IOPS、1,000 MB/秒に対して課金されます。

プロビジョニングされる IOPS = (検出されたスループット) * 1024/256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>Ultra Disk の推奨事項では待機時間が考慮されますか?

いいえ。現時点では、サイズ設定とコスト計算には、ディスク サイズ、合計スループット、合計 IOPS が使用されます。

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>M シリーズで Ultra Disk がサポートされているのを確認できますが、Ultra Disk が推奨された評価では、"この場所に VM が見つかりません" と表示されるのはなぜですか?

Ultra Disk をサポートするすべての VM のサイズが、Ultra Disk でサポートされているすべてのリージョンに存在とは限らないため、このようになる可能性があります。 このサーバーについて VM のサイズを取得するには、ターゲット評価リージョンを変更してください。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>予約インスタンス、VM アップタイム、割引 (%) の無効な組み合わせで作成されたことを示す警告が評価で表示されるのはなぜですか?

[予約済みインスタンス] を選択すると、[割引 (%)] および [VM のアップタイム] プロパティは適用されません。 これらのプロパティの無効な組み合わせで評価が作成されたため、編集ボタンと再計算ボタンは無効になっています。 新しい評価を作成してください。 [詳細については、こちらを参照してください](./concepts-assessment-calculation.md#whats-an-assessment)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>物理サーバー上の一部のネットワーク アダプターのパフォーマンス データが表示されません

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーでは、製品のギャップのため、Azure Migrate では現在、物理と仮想両方のネットワーク アダプターが検出されます。 ネットワーク スループットは、検出された仮想ネットワーク アダプターでのみキャプチャされます。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>物理サーバーに対して推奨される Azure VM SKU が大きすぎます

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーの場合、Azure Migrate では現在、物理と仮想両方のネットワーク アダプターが検出されます。 そのため、 検出されるネットワーク アダプターの数は実際より多くなります。 Azure VM 評価によって必要な数のネットワーク アダプターをサポートできる Azure VM が選択されるため、これによりサイズが大きすぎる VM になる可能性があります。 [詳細情報](./concepts-assessment-calculation.md#calculating-sizing)で、 サイズ設定に対するネットワーク アダプターの数の影響について確認してください。 これは、今後対処される製品のギャップです。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>物理サーバーの準備カテゴリが "準備ができていません" になります

Hyper-V の仮想化が有効になっている物理サーバーの場合、準備カテゴリが誤って "準備ができていません" とマークされることがあります。 これらのサーバーでは、製品のギャップのため、Azure Migrate では現在、物理と仮想両方のアダプターが検出されます。 そのため、 検出されるネットワーク アダプターの数は実際より多くなります。 オンプレミスに合わせた評価とパフォーマンス ベースの評価のどちらでも、Azure VM 評価によって必要な数のネットワーク アダプターをサポートできる Azure VM が選択されます。 ネットワーク アダプターの数が 32 を超えていることが検出された場合、 これは Azure VM でサポートされる NIC の最大数であり、サーバーは "準備ができていません" とマークされます。  [詳細情報](./concepts-assessment-calculation.md#calculating-sizing)で、 サイズ設定に対する NIC の数の影響について確認してください。

## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>検出される NIC の数が、物理サーバーの実際の数より多くなります

これは、物理サーバーで Hyper-V 仮想化が有効になっている場合に発生する可能性があります。 これらのサーバーの場合、Azure Migrate では現在、物理と仮想両方のアダプターが検出されます。 そのため、 検出される NIC の数は実際より多くなります。

## <a name="capture-network-traffic"></a>ネットワーク トラフィックをキャプチャする

次のようにネットワーク トラフィック ログを収集します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、 **[ナビゲーション時にエントリをクリア]** の設定をオフにします。
3. **[ネットワーク]** タブを選択し、ネットワーク トラフィックのキャプチャを開始します。
   - Chrome では、 **[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 赤い円が表示されない場合は、黒い円を選択して開始します。
   - Microsoft Edge または Internet Explorer では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンを選択します。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
   - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** を選択します。 この操作により、ログが .har ファイルとして圧縮されエクスポートされます。
   - Microsoft Edge または Internet Explorer で、 **[キャプチャしたトラフィックのエクスポート]** オプションを選択します。 この操作により、ログが圧縮されてエクスポートされます。
6. **[コンソール]** タブを選択し、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
   - Chrome では、コンソール ログのどこかを右クリックします。 **[名前を付けて保存]** を選択し、ログをエクスポートして zip 圧縮します。
   - Microsoft Edge または Internet Explorer では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>評価でのオペレーティング システムのデータはどこから検出されますか?

- VMware VM の場合、既定では、vCenter によって提供されるオペレーティング システムのデータです。
   - VMware Linux VM の場合は、アプリケーション検出が有効になっていると、OS の詳細はゲスト VM からフェッチされます。 評価に含まれる OS の詳細を確認するには、[検出済みサーバー] ビューに移動し、[オペレーティング システム] 列の値をマウスでポイントします。 ポップアップするテキストで、表示されている OS データが vCenter Server から収集されたものか、VM の資格情報を使用してゲスト VM から収集されたものかを確認できます。
   - Windows VM の場合は、オペレーティング システムの詳細は常に vCenter Server からフェッチされます。
- Hyper-V VM の場合は、オペレーティング システムのデータは Hyper-V ホストから収集されます。
- 物理サーバーの場合は、サーバーからフェッチされます。

## <a name="common-web-apps-discovery-errors"></a>Web アプリの検出に関する一般的なエラー

Azure Migrate: Discovery and assessment ツールを使用すると、検出された ASP.NET Web アプリについて、Azure App Service への移行を評価できます。 始めるには、[評価](tutorial-assess-webapps.md)に関するチュートリアルを参照してください。

Azure App Service の評価に関する一般的なエラーを次の表にまとめます。

| **Error** | **原因** | **推奨される操作** |
|--|--|--|
|**アプリケーション プールのチェック。**|IIS サイトで、次のアプリケーション プールが使用されています: {0}。|Azure App Service では、App Service アプリケーションごとに複数のアプリケーション プールを構成することはサポートされていません。 ワークロードを 1 つのアプリケーション プールに移動し、その他のアプリケーション プールを削除します。|
|**アプリケーション プール ID のチェック。**|サイトのアプリケーション プールは、サポートされていないユーザー ID の種類として実行されています: {0}|Azure App Service では、LocalSystem または SpecificUser のアプリケーション プール ID の種類の使用はサポートされていません。 ApplicationPoolIdentity として実行するようにアプリケーション プールを設定します。|
|**認可のチェック。**|次のサポートされていない認証の種類が見つかりました: {0}|Azure App Service でサポートされている認証の種類と構成は、オンプレミスの IIS とは異なります。 サイトで、サポートされていない認証の種類を無効にします。 移行が完了した後、Azure App Service でサポートされている認証の種類の 1 つを使用してサイトを構成できます。|
|**認可のチェックが不明。**|すべてのサイト構成で有効になっている認証の種類を特定できません。|認証の種類を特定できません。 すべての構成エラーを修正し、すべてのサイト コンテンツの場所に Administrators グループからアクセスできることを確認します。|
|**構成エラーのチェック。**|次の構成エラーが見つかりました: {0}|移行の準備状況は、該当するすべての構成を読み取ることなく判断することはできません。 すべての構成エラーを修正し、構成が有効でアクセス可能であることを確認します。|
|**コンテンツ サイズ チェック。**|サイトのコンテンツが、移行が成功するために許容される最大値 2 GB を超えている可能性があります。|移行が成功するには、サイトのコンテンツを 2 GB 未満にする必要があります。 Azure Storage のような静的コンテンツについて、サイトを非ファイル システム ベースのストレージ オプションの使用に切り替えることができるかどうかを評価します。|
|**コンテンツ サイズのチェックが不明。**|ファイルのコンテンツ サイズを特定できません。通常、これはアクセスの問題を示します。|サイトを移行するには、コンテンツにアクセスできる必要があります。 サイトでコンテンツに UNC 共有が使用されていないこと、およびすべてのサイト コンテンツの場所に Administrators グループからアクセスできることを確認します。|
|**グローバル モジュールのチェック。**|次のサポートされていないグローバル モジュールが検出されました: {0}|Azure App Service では、限られたグローバル モジュールがサポートされています。 GlobalModules セクションのサポートされていないモジュールと、関連付けられているすべての構成を削除します。|
|**ISAPI フィルターのチェック。**|次のサポートされていない ISAPI フィルターが検出されました: {0}|カスタム ISAPI フィルターの自動構成はサポートされていません。 サポートされていない ISAPI フィルターを削除します。|
|**ISAPI フィルターのチェックが不明。**|すべてのサイト構成で、ISAPI フィルターの存在を特定できません。|カスタム ISAPI フィルターの自動構成はサポートされていません。 すべての構成エラーを修正し、すべてのサイト コンテンツの場所に Administrators グループからアクセスできることを確認します。|
|**場所タグのチェック。**|applicationHost.config ファイルで次の場所のパスが見つかりました: {0}|移行方法では、applicationHost.config で場所のパスの構成を移動することはサポートされていません。場所のパスの構成は、サイトのルートの web.config ファイル、またはそれが適用される特定のアプリケーションに関連付けられている web.config ファイルに移動します。|
|**プロトコルのチェック。**|次のサポートされていないプロトコルを使用するバインドが見つかりました: {0}|Azure App Service では、HTTP プロトコルと HTTPS プロトコルのみがサポートされています。 HTTP または HTTPS ではないプロトコルを使用しているバインドを削除します。|
|**仮想ディレクトリのチェック。**|次の仮想ディレクトリが UNC 共有でホストされています: {0}|移行では、UNC 共有でホストされているサイト コンテンツの移行はサポートされていません。 ローカル ファイル パスにコンテンツを移動するか、Azure Storage のような非ファイル システム ベースのストレージ オプションに変更することを検討します。 共有構成を使用している場合は、コンテンツ パスを変更する前に、サーバーの共有構成を無効にします。|
|**HTTPS のバインドのチェック。**|アプリケーションで HTTPS が使用されています。|App Service で HTTPS を構成するには、追加の手動手順が必要です。 移行後の追加手順で、証明書を Azure App Service のサイトに関連付ける必要があります。|
|**TCP ポートのチェック**|次のサポートされていないポートでバインドが見つかりました: {0}|Azure App Services でサポートされているポートは、80 と 443 のみです。 サイトへの要求を行うクライアントで、80 または 443 を使用するようの要求のポートを更新する必要があります。|
|**フレームワークのチェック。**|次の .NET 以外のフレームワークまたはサポートされていない .NET Framework のバージョンが、このサイトで使用されている可能性があることが検出されました: {0}|.NET 以外のサイトのフレームワークは、移行で検証されません。 App Service では複数のフレームワークがサポートされていますが、移行オプションが異なります。 .NET 以外のフレームワークがサイトで使用されていないことを確認するか、または代替移行オプションの使用を検討してください。|

## <a name="next-steps"></a>次のステップ

評価を[作成](how-to-create-assessment.md)するか[カスタマイズ](how-to-modify-assessment.md)します。
