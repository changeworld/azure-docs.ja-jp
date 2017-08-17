---
title: "Site Recovery コンテナーを Recovery Services コンテナーにアップグレードする"
description: "Azure Site Recovery コンテナーを Recovery Services コンテナーにアップグレードする方法について説明します"
ddocumentationcenter: 
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
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Site Recovery コンテナーを Azure Resource Manager ベースの Recovery Services コンテナーにアップグレードする

この記事では、進行中のレプリケーションに影響を与えることなく、'Site Recovery コンテナー' を Azure Resource Manager ベースの 'Recovery Service コンテナー' にアップグレードする方法について説明します。 Azure Resource Manager の機能と利点について詳しくは、[こちら](../azure-resource-manager/resource-group-overview.md)で確認できます。

## <a name="introduction"></a>はじめに
Recovery Services コンテナーはバックアップやディザスター リカバリーのニーズをクラウドでネイティブに管理できる、Azure Resource Manager のリソースです。 これは新しい Azure Portal で使用できる統合コンテナーで、従来の Backup コンテナーと Site Recovery コンテナーに代わるものです。

Recovery Services コンテナーは次のような機能の配列を開きます。

-   Azure Resource Manager のサポート: お使いの仮想マシンや物理マシンを保護し、Azure Resource Manager のスタックにフェールオーバーできます。

-   ディスクの除外 - 一時ファイルまたは高チャーンのデータで帯域幅を使い切りたくない場合は、ボリュームをレプリケーションから除外できます。 この機能は現在、'VMware から Azure へ' および 'Hyper-V から Azure へ' で有効になっており、他のシナリオにも拡張される予定です。

- Premium およびローカル冗長ストレージ (LRS) のサポート - サーバーを Premium Storage アカウントで保護し、高い IOPS 下で顧客のアプリケーションを保護できるようになりました。 この機能は現在、'VMware から Azure へ' で有効になっています。

-   効率的な 'はじめに' エクスペリエンス - 'はじめに' エクスペリエンスの機能が拡張され、ディザスター リカバリーの設定が簡単になるようにカスタマイズされました。

- 同じコンテナーから Backup と Site Recovery を管理する - ディザスター リカバリー用にサーバーを保護し、同じコンテナーからバックアップを実行することで、管理オーバーヘッドを大幅に削減できるようになりました。

アップグレードされたエクスペリエンスと機能について詳しくは、こちらの[ブログ](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)をご覧ください。

## <a name="salient-features"></a>注目すべき機能

- **進行中のレプリケーションに影響なし**: アップグレード中およびアップグレード後に中断なく進行中のレプリケーションを継続できます。

- **追加コストなし** – 追加コストなしで新しい機能のセットをすべて使用できます。

- **データ損失なし** – 移行ではなくアップグレードであるため、既存のレプリケーション情報 (復旧ポイント、レプリケーションの設定など) はアップグレード中およびアップグレード後もそのまま残ります。


## <a name="what-happens-during-the-upgrade"></a>アップグレード中の動作

アップグレード中は、新しいサーバーを登録したり、VM のレプリケーションを有効にしたりすることは許可されません。 保護された項目の進行中のレプリケーションなど、コンテナーに対するデータの読み書きのみが関与する操作については、引き続き中断されずに行われます。

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>コンテナーのアップグレード後のオートメーションおよびツールの変更
クラシック デプロイメント モデルから Resource Manager デプロイメント モデルへのアップグレードの一環として、既存のオートメーションまたはツールを、アップグレード後も確実に機能し続けるように更新する必要があります。

## <a name="preparing-your-environment-for-vault-upgrade"></a>コンテナーをアップグレードする環境を準備する

1.  この[リンク](https://www.microsoft.com/download/details.aspx?id=50395)を使用して PowerShell をインストールまたはバージョン 5 以上にアップグレードする

2.  [こちら](https://github.com/Azure/azure-powershell/releases)から最新の Azure PowerShell MSI をインストールする

3.  コンテナーをアップグレードするスクリプトを[ダウンロード](https://aka.ms/vaultupgradescript)する

## <a name="prerequisites-for-upgrade"></a>アップグレードの前提条件
Site Recovery のコンテナーを Azure Resource Manager ベースの Recovery Service のコンテナーにアップグレードするには、次の前提条件を満たす必要があります。

- エージェントの最小バージョン: アップグレードするには、サーバー上にインストールされている Azure Site Recovery プロバイダーのバージョンが 5.1.1700.0 以上である必要があります。

- サポートされている構成: コンテナーが SAN、SQL Always 可用性グループで構成されていないこと。 その他のすべての構成はサポートされています。

>[!NOTE]
> ストレージのマッピングはアップグレード後に PowerShell を介してのみ管理できます。

- サポートされているデプロイ シナリオ - コンテナーが従来の 'VMware から Azure へ' のデプロイメント モデルでないこと。  続行するにはまず拡張されたデプロイメント モデルに移動する必要があります。

- ユーザーによって開始された管理プレーン操作が関与するアクティブなジョブがない: アップグレード中には管理プレーンへのアクセスが制限されているため、管理プレーンのすべてのアクションを完了してからアップグレードをトリガーする必要があります。 これには進行中のレプリケーションは含まれません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- このアップグレードは進行中のレプリケーションに影響しますか?

  いいえ。 進行中のレプリケーションはアップグレード中およびアップグレード後も中断されずに続行されます。

- サイト間 VPN、IP 設定などのネットワーク設定はどうなりますか?

  アップグレードはネットワーク設定には影響せず、Azure からオンプレミスへの接続はすべてそのまま残ります。
- 近日中にアップグレードする予定がない場合、コンテナーはどうなりますか?

  以前の Azure Portal における Site Recovery コンテナーのサポートは 9 月より廃止されます。 このため、アップグレード機能を使用して新しいポータルに移行することを強くお勧めします。

- 既存のツールにとって、この移行計画はどのような意味がありますか?  

  Recovery Services コンテナーがベースにしている Resource Manager デプロイメント モデルへのツールの更新は、アップグレード プランで考慮する必要がある最も重要な変更の 1 つです。

- 管理プレーンのダウンタイムはどれくらいですか?

  アップグレードには 15 ～ 30 分ほどかかります。 最大で 1 時間かかります。

- アップグレード後にロールバックすることはできますか?

  いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

- サブスクリプションまたはリソースがアップグレードが可能かどうかを確認できますか?

  はい。 プラットフォームでサポートされるアップグレード オプションでは、アップグレードの最初の手順として、リソースをアップグレードできるかどうかを検証します。 前提条件の検証に失敗すると、該当するエラーや警告を受け取ります。

- アップグレードの問題を報告するにはどうすればよいですか?

  アップグレード中にエラーが発生した場合は、エラーに示されている OperationId をメモしてください。 Microsoft サポートは、積極的に問題の解決に取り組みます。 お客様からサポート チームに問い合わせることもできます。その際、サブスクリプション ID、コンテナーの名前、および OperationId をお知らせください。 できるだけ早く問題を解決するように努力します。 Microsoft による明確な指示がない限り、操作をやり直さないでください。

## <a name="how-to-run-the-script"></a>スクリプトはどのように実行できますか?

PowerShell プロンプトから、次のコマンドを実行します。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionID: アップグレードするコンテナーに関連付けられているサブスクリプション ID
- VaultName: アップグレードするコンテナーの名前
- Location: アップグレードするコンテナーの場所
- ResourceType: Site Recovery コンテナーの HyperVRecoveryManagerVault
- TargetResourceGroupName: アップグレードされたコンテナーを配置するリソース グループ TargetResourceGroupName には Azure Resource Manager 内の既存の ResourceGroup を指定することも、新しいものにもできます。 指定された TargetResourceGroupName が存在しない場合、アップグレードの一環としてコンテナーと同じ場所に作成されます。 リソース グループについて詳しくは、[こちら](../azure-resource-manager/resource-group-overview.md#resource-groups)をクリックしてください。

>[!NOTE]
>リソース グループの名前には制約があります。 これに従わないとコンテナーのアップグレードが失敗する場合があります。

例:

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


代わりに以下のスクリプトを実行すると、必要なすべてのパラメーターの入力が求められます。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  PowerShell スクリプトによって、資格情報を入力することを求められます。 資格情報は 2 回入力します。1 回目の入力は ASM アカウント用であり、2 回目は Azure Resource Manager アカウント用です。

2.  資格情報が入力されると、スクリプトで前提条件チェックが実行され、インフラストラクチャの設定がこのドキュメントの前半で説明した前提条件を満たしていることを判定します。

3.  前提条件のチェックが完了すると、コンテナーのアップグレードを続行するかどうかの確認が求められます。 確認すると、アップグレート プロセスが開始され、コンテナーがアップグレードされます。 アップグレードの完了までに 15 ～ 30 分かかります。

4.  アップグレードが正常に完了すると、新しい Azure Portal でアップグレードされたコンテナーにアクセスできます。

## <a name="management-experience-post-upgrade"></a>アップグレード後の管理エクスペリエンス

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Recovery Services コンテナーで Azure Site Recovery を使用してレプリケートする方法

- リージョン別に Azure VM を保護できるようになります。 詳しくは、[こちら](site-recovery-azure-to-azure.md)のドキュメントをご覧ください。

- Azure に VMware VM をレプリケートする方法について詳しくは、[こちら](vmware-walkthrough-overview.md)のドキュメントをご覧ください。

- Azure に Hyper-VM (VMM なし) をレプリケートする方法について詳しくは、[こちら](hyper-v-site-walkthrough-overview.md)のドキュメントをご覧ください。

- Azure に Hyper-VM (VMM あり) をレプリケートする方法について詳しくは、[こちら](vmm-to-azure-walkthrough-overview.md)のドキュメントをご覧ください。

- セカンダリ サイトに Hyper-VM (VMM あり) をレプリケートする方法について詳しくは、[こちら](site-recovery-vmm-to-vmm.md)のドキュメントをご覧ください。

- セカンダリ サイトに VMware VM をレプリケートする方法について詳しくは、[こちら](site-recovery-vmware-to-vmware.md)のドキュメントをご覧ください。

### <a name="how-to-view-your-replicated-items"></a>レプリケートされたアイテムを表示する方法

以下の画面は Recovery Services コンテナーのダッシュボード ページを示します。ここにはコンテナーのキー エンティティが表示されます。 コンテナーで保護されたエンティティの一覧を表示するには、**[Site Recovery]** -> **[レプリケートされたアイテム]** をクリックします。


![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/replicateditems.png)

以下の画面は、レプリケートされたアイテムを表示するワークフローと、フェールオーバーを開始する方法を示します。

![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>レプリケーションの設定を表示する方法

Site Recovery コンテナーでは、各保護グループがレプリケーション設定 (コピーの頻度、復旧ポイントのリテンション期間、アプリケーションの整合性スナップショットの作成頻度など) で構成されています。 Recovery Services コンテナーでは、これらの設定はレプリケーション ポリシーとして構成されています。 ポリシーの名前は保護グループの名前 または ’primarycloud_Policy’ です。

レプリケーション ポリシーについて詳しくは、[こちら](site-recovery-setup-replication-settings-vmware.md)をご覧ください。

