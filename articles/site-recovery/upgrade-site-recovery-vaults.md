---
title: "Azure Recovery Services コンテナーを Site Recovery コンテナーにアップグレードします。"
description: "Recovery Services コンテナーへの Azure Site Recovery コンテナーをアップグレードする方法します。"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Azure Resource Manager ベースの回復サービス コンテナーを Site Recovery コンテナーにアップグレードします。

この記事では、進行中のレプリケーションに影響を及ぼさない Recovery サービスの Azure Resource Manager ベースの資格情報コンテナーに Azure Site Recovery コンテナーをアップグレードする方法について説明します。 Azure リソース マネージャーの機能と利点の詳細については、次を参照してください。 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)です。

## <a name="introduction"></a>はじめに
Recovery Services コンテナーは、クラウドでネイティブのバックアップと障害回復を管理するための Azure Resource Manager リソースです。 新しい Azure ポータルで使用できる統合された資格情報コンテナーである、従来のバックアップに置き換えられますと Site Recovery 資格情報コンテナーとします。

Recovery Services コンテナーは、機能 (など) の配列を提供します。

* Azure リソース マネージャーのサポート: 保護し、Azure Resource Manager スタックへの仮想マシンおよび物理マシンをフェールオーバーすることができます。

* ディスクの除外: 一時ファイルまたはすべての帯域幅を使用したくないチャーンが高いデータがあれば、レプリケーションからボリュームを除外することができます。 この機能は現在有効になっている*を Azure に VMware*と*を Azure には、HYPER-V*と同様に、その他のシナリオを拡張します。

* Premium およびローカル冗長ストレージのサポート: サーバーを保護するようになりました premium storage で顧客が高位でアプリケーションを保護できるようにアカウント入力/出力操作/秒 (IOPS)。 この機能が有効な現在*を Azure に VMware*です。

* 入門のエクスペリエンスを合理化: 災害復旧のセットアップを簡単に拡張された入門エクスペリエンスは設計されています。

* バックアップと同じ資格情報コンテナーから Site Recovery の管理: 今すぐ災害復旧のためのサーバーを保護したり、同じ資格情報コンテナーから、減らすことができます、管理オーバーヘッドが大幅にバックアップを実行します。

アップグレードのエクスペリエンスと機能の詳細については、次を参照してください。、[記憶域、バックアップ、および回復のブログ](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)です。

## <a name="salient-features"></a>注目すべき機能

* 進行中のレプリケーションに影響なし: 進行中のレプリケーション中に中断することがなく続行し、アップグレードを投稿します。

* 追加コストなし: 追加コストなしで更新された機能のセット全体を取得します。

* データ損失なし: このプロセスは、アップグレードと移行ではありませんが、ための設定と既存のレプリケーションの回復ポイントはそのまま保持中に、アップグレード後にします。


## <a name="what-happens-during-the-vault-upgrade"></a>新機能は、資格情報コンテナーのアップグレード中にどうなりますか。

アップグレードの過程では、新しいサーバーを登録するか、仮想マシン (VM) のレプリケーションを有効にするなどの操作を行うことはできません。 データに対する読み取りまたは書き込みデータ、資格情報コンテナーに保護された項目の進行中のレプリケーションなど、コンテナーに関連する操作中断されません。

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>オートメーションと、アップグレード後にツールへの変更
クラシック デプロイ モデルからリソース マネージャーの配置モデルにコンテナーの種類をアップグレードするには、既存の自動化や、アップグレード後に作業を続行することを確認するツールを更新します。

### <a name="prepare-your-environment-for-the-upgrade"></a>アップグレードの環境を準備します。

* [PowerShell をインストールまたは 5 およびそれ以降のバージョンにアップグレードします。](https://www.microsoft.com/download/details.aspx?id=50395)
* [最新のバージョンの Azure PowerShell の MSI をインストールします。](https://github.com/Azure/azure-powershell/releases)
* [Recovery Services コンテナーのアップグレード スクリプトをダウンロードします。](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>前提条件
回復サービスの Azure Resource Manager ベースの資格情報コンテナーを Site Recovery 資格情報コンテナーからアップグレードするには、次の要件を満たす必要があります。

* 最小のエージェントのバージョン: 5.1.1700.0 をする必要があります、サーバーにインストールされている Azure Site Recovery Provider のバージョンまたはそれ以降。

* サポートされている構成: 記憶域エリア ネットワーク (SAN) または SQL Server AlwaysOn 可用性グループで、資格情報コンテナーを構成することはできません。 その他のすべての構成はサポートされています。

    >[!NOTE]
    >アップグレード後、PowerShell を使用してのみ、記憶域のマッピングを管理できます。

* サポートされている配置シナリオ: 資格情報コンテナーにすることはできません、*を Azure に VMware*従来の配置モデルです。 続行する前に、まず、強化された配置モデルに移動します。

* 管理に関連するアクティブのユーザーによって開始されたジョブには、operations 平面なし: アップグレード中に、管理の平面へのアクセスは制限されているためアップグレードを開始する前にすべての管理面アクションを完了します。 このプロセスには、進行中のレプリケーションが含まれていません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**このアップグレードによって、進行中のレプリケーションが影響を与える**

いいえ。 進行中のレプリケーションを続行中に、アップグレード後に中断します。

**サイト対サイト VPN および IP の設定などのネットワーク設定の変更点**

アップグレードでは、ネットワーク設定に影響しません。 Azure-への内部設置型のすべての接続は、そのまま残ります。

**新機能により、近い将来にアップグレードしない場合、自分の資格情報コンテナーにどうなりますか。**

以前の Azure ポータルの Site Recovery コンテナーのサポートが廃止される予定 2017 年 9 月を開始します。 新しいポータルに移動するアップグレード機能を使用することを強くお勧めします。

**既存のツールにとって、この移行計画はどのような意味がありますか?**  

リソース マネージャーの配置モデルに、ツールを更新すると、アップグレード計画を考慮する必要がありますのある最も重要な変更の 1 つです。 復旧サービス コンテナーは、リソース マネージャーの配置モデルに基づきます。 

**管理面のダウンタイムにどれくらい最後ですか?**

アップグレード通常約 15 ~ 30 分間は、最大 1 時間かかる場合があります、します。

**アップグレード後にロールバックすることはできますか?**

いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

**自分のサブスクリプションまたはリソースをアップグレードするかどうかを参照してくださいを検証したことができますか。**

はい。 プラットフォームでサポートされるアップグレード オプションで、アップグレードの最初の手順は、リソースにアップグレードできることを検証するには 検証に失敗した場合は、適切なエラー メッセージまたは警告が表示されます。

**アップグレードの問題を報告する方法は?**

アップグレード中にエラーが発生した場合、エラーに記載されている操作 ID に注意してください。 Microsoft サポートは、問題の解決能動的に動作します。 使用するサブスクリプション ID、資格情報コンテナー名では、操作 id です。 サポート チームに問い合わせることも サポートは、問題をできるだけ早く解決するのには機能します。 明示的に、Microsoft によってするように指示されたを除き、操作を再試行できません。

## <a name="run-the-script"></a>スクリプトを実行する

PowerShell では、次のコマンドを実行します。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: アップグレードする場合、コンテナーに関連付けられているサブスクリプション ID です。

* VaultName: アップグレードする場合、コンテナーの名前です。

* 場所: アップグレードする場合、コンテナーの場所です。

* ResourceType: HyperVRecoveryManagerVault を Site Recovery 資格情報コンテナーします。

* TargetResourceGroupName: リソース グループを配置するアップグレードされた資格情報コンテナーを選択します。 TargetResourceGroupName には、Azure リソース マネージャーまたは新規のいずれかの既存のリソース グループを指定できます。 指定されている TargetResourceGroupName が存在しない場合は、資格情報コンテナーと同じ場所に、アップグレードの一部として作成されます。 詳細についてを参照してください「リソース グループ」の[Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md#resource-groups)です。

    >[!NOTE]
    >リソース グループの名前付けは、特定の制約が適用されます。 資格情報コンテナーのアップグレードの失敗を防ぐためには、必ず名前付け規則を慎重に確認します。
    >
    >For example:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 SubscriptionId-1234-54123-354354-56416-8645-VaultName gen2dr-場所「北ヨーロッパ」- ResourceType hypervrecoverymanagervault - TargetResourceGroupName abc

代わりに、次のスクリプトを実行することができます。 必要なパラメーターの値を入力します。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell スクリプトによって、資格情報を入力することを求められます。 それらを 2 回入力、1 回、クラシック デプロイ モデルとアカウントの 1 回、Azure リソース マネージャー アカウント。

2. 資格情報を入力したら、スクリプトは、インフラストラクチャのセットアップが前に説明した要件を満たしているかどうかを確認するチェックを実行します。

3. 前提条件がチェックされ、確認後、は、資格情報コンテナーのアップグレードを続行を求められます。 アップグレード プロセスは、資格情報コンテナーのアップグレードを開始します。 アップグレード全体は、15 ~ 30 分かかります。

4. アップグレードが正常に完了したら後、新しい Azure ポータルでアップグレードされた資格情報コンテナーにアクセスできます。

## <a name="post-upgrade-vault-management"></a>アップグレード後の資格情報コンテナーの管理

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Recovery Services コンテナーの Azure Site Recovery を使用してレプリケートします。

* リージョン別に Azure VM を保護できるようになります。 詳細については、次を参照してください。 [Azure Vm を Azure Site Recovery とリージョン間でレプリケート](site-recovery-azure-to-azure.md)です。

* Azure への VMware 仮想マシンのレプリケーションの詳細については、次を参照してください。 [Site Recovery と Azure への VMware 仮想マシンのレプリケート](vmware-walkthrough-overview.md)です。

* (VMM) なしの HYPER-V で Vm を Azure にレプリケートする方法の詳細については、次を参照してください。 [(VMM) なしのレプリケートの Hyper-v 仮想マシンを Azure](hyper-v-site-walkthrough-overview.md)です。

* (VMM) の HYPER-V で Vm を Azure にレプリケートする方法の詳細については、次を参照してください。[を使用して、Azure ポータルで azure Site Recovery の VMM クラウド内のレプリケートの Hyper-v 仮想マシン](vmm-to-azure-walkthrough-overview.md)です。

* セカンダリ サイトへの (VMM) でのハイパー Vm のレプリケーションの詳細については、次を参照してください。 [Azure ポータルを使用してセカンダリ VMM サイトの VMM クラウド内のレプリケートの Hyper-v 仮想マシン](site-recovery-vmm-to-vmm.md)です。

* セカンダリ サイトへの VMware 仮想マシンのレプリケーションの詳細については、次を参照してください。 [Replicate オンプレミス VMware バーチャル マシンまたは Azure クラシック ポータルでセカンダリ サイトへの物理サーバー](site-recovery-vmware-to-vmware.md)です。

### <a name="view-your-replicated-items"></a>レプリケートされたアイテムを表示します。

次の図は、資格情報コンテナーのキーのエンティティを表示する、Recovery Services コンテナー ダッシュ ボード ページを示します。 資格情報コンテナーで保護されたエンティティの一覧を表示するには選択**Site Recovery** > **レプリケートされたアイテム**です。


![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/replicateditems.png)

次の図は、レプリケートされたアイテムを表示するためのワークフローと**フェールオーバー**コマンドでフェールオーバーを開始します。

![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>レプリケーション設定を表示します。

Site Recovery コンテナーでコピーの頻度、回復ポイントの保有期間、アプリケーションの整合性スナップショットの頻度およびその他のレプリケーション設定で、各保護グループを構成します。 Recovery Services コンテナーでは、これらの設定は、レプリケーション ポリシーとして構成されます。 ポリシーの名前は、保護グループの名前または*primarycloud_Policy*です。

レプリケーション ポリシーの詳細については、次を参照してください。 [VMware Azure へのレプリケーション ポリシーを管理](site-recovery-setup-replication-settings-vmware.md)です。
