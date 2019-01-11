---
title: よくある質問 - Azure Site Recovery を使用した Azure から Azure へのディザスター リカバリー | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して Azure VM のディザスター リカバリーを別の Azure リージョンに設定する場合のよくある質問をまとめています
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969949"
---
# <a name="common-questions---azure-to-azure-replication"></a>よくある質問 - Azure から Azure へのレプリケーション

この記事では、Azure VM のディザスター リカバリーを別の Azure リージョンにデプロイするときによくある質問に回答します。 この記事の内容について質問がある場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="general"></a>全般
### <a name="how-is-site-recovery-priced"></a>Site Recovery の価格
詳しくは、「[Site Recovery の価格](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)」をご覧ください。

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Azure VM で Site Recovery を構成する方法の ベスト プラクティスとは?
1. [Azure から Azure へのアーキテクチャを理解する](azure-to-azure-architecture.md)
2. [サポートされている構成とサポートされていない構成を確認する](azure-to-azure-support-matrix.md)
3. [Azure VM のディザスター リカバリーを設定する](azure-to-azure-how-to-enable-replication.md)
4. [テスト フェールオーバーの実行](azure-to-azure-tutorial-dr-drill.md)
5. [プライマリ リージョンへのフェールオーバーとフェールバック](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Azure Disk Encryption が有効になっている VM をレプリケートできますか?
はい、レプリケートできます。 Azure Disk Encryption (ADE) が有効になっている VM のレプリケーションを有効にするには、[この記事](azure-to-azure-how-to-enable-replication-ade-vms.md)を参照してください。 現在、Azure Site Recovery では、Windows OS を実行していて、Azure AD アプリでの暗号化が有効にされている Azure VM のみがサポートされていることに注意してください。

### <a name="can-i-replicate-vms-to-another-subscription"></a>別のサブスクリプションに VM をレプリケートできますか?
はい、同じ Azure Active Directory テナント内で別のサブスクリプションに Azure VM をレプリケートできます。
[サブスクリプション間](https://azure.microsoft.com/blog/cross-subscription-dr)で DR を構成するのは簡単です。 レプリケーション時に別のサブスクリプションを選択できます。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>ゾーンにピン留めされた Azure VM を別のリージョンにレプリケートできますか?
はい、[ゾーンにピン留めされた VM](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) を別のリージョンにレプリケートすることができます。

### <a name="can-i-exclude-disks"></a>ディスクを除外することはできますか?

はい、PowerShell を使用して保護時にディスクを除外できます。 ディスクを除外するには、[PowerShell のガイダンス](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)を参照してください。

###<a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?
Azure VM を別の Azure リージョンにレプリケートするときは、レプリケーションは継続的に行われます。 詳細を理解するには、[Azure から Azure ](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)のレプリケーション アーキテクチャを確認してください。

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>同じリージョン内で仮想マシンをレプリケートできますか? VM を移行するのにこれが必要です。
Azure から Azure の DR ソリューションを使用して、同じリージョン内で VM をレプリケートすることはできません。

### <a name="can-i-replicate-vms-to-any-azure-region"></a>VM を任意の Azure リージョンにレプリケートできますか?
Site Recovery を使用して、同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。 詳細については、Site Recovery [リージョンのサポート マトリックス](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)をご覧ください。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery にはインターネット接続が必要ですか?

いいえ、Site Recovery にはインターネット接続は必要ありませんが、この[記事](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)で説明されているように、Site Recovery URL および IP 範囲にアクセスするには必要です。

## <a name="replication-policy"></a>レプリケーション ポリシー

### <a name="what-is-a-replication-policy"></a>レプリケーション ポリシーとは何ですか?
復旧ポイントのリテンション履歴と、アプリ整合性スナップショットの頻度の設定を定義します。 既定では、Azure Site Recovery によって、既定の設定 (復旧ポイントのリテンション期間が "24 時間"、アプリ整合性スナップショットの頻度が "60 分") で新しいレプリケーション ポリシーが作成されます。

### <a name="what-is-crash-consistent-recovery-point"></a>クラッシュ整合性復旧ポイントとは何ですか?
クラッシュ整合性復旧ポイントは、VM がクラッシュしたか、スナップショットの作成時に電源コードがサーバーから引き抜かれたときの、ディスク上のデータを表します。 これには、スナップショットの作成時にメモリ内にあったものは一切含まれません。 現在、ほとんどのアプリケーションは、クラッシュ整合性のスナップショットから十分に復旧できます。 クラッシュ整合性復旧ポイントは通常、データベースのないオペレーティング システムや、ファイル サーバー、DHCP サーバー、プリント サーバーなどのアプリケーションにとっては十分です。

### <a name="what-is-application-consistent-recovery-point"></a>アプリケーション整合性復旧ポイントとは何ですか? 
アプリケーション整合性復旧ポイントは、クラッシュ整合スナップショットと同じデータをキャプチャするアプリケーション整合性のスナップショットから作成され、メモリ内のすべてのデータと処理中のすべてのトランザクションが追加されます。 これらの追加コンテンツのため、アプリケーション整合性スナップショットは、最も複雑で実行時間も最も長くかかります。 アプリケーション整合性の復旧ポイントは、SQL などのデータベース オペレーティング システムで推奨されます。

### <a name="how-are-recovery-points-generated-and-saved"></a>復旧ポイントはどのように生成されて保存されますか?
Site Recovery で復旧ポイントがどのように生成されるかを理解するため、レプリケーション ポリシーの例を見てみましょう。このポリシーには、24 時間の復旧ポイントの保有期間と 1 時間のアプリ整合性の頻度のスナップショットが設定されています。
Site Recovery は、5 分ごとにクラッシュ整合性ポイントを作成します。ユーザーがこの頻度の変更を制御することはできません。 そのため、ユーザーは直近の 1 時間の 12 のクラッシュ整合性ポイントと 1 つのアプリケーション整合性ポイントから選択できます。 時間が経過すると、1 時間を経過した復旧ポイントは Site Recovery によってすべて取り除かれ、1 時間に 1 つの復旧ポイントのみが保存されます。
下のスクリーンショットで説明します。


1. 直近 1 時間未満では、5 分ごとの復旧ポイントがあります。
2. 1 時間を超えると、1 時間に 1 つの復旧ポイントのみが保持されていることがわかります。

  ![復旧ポイントの生成](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>過去のどの時点まで遡って復旧できますか?
使用できる最も古い復旧ポイントは 72 時間です。

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>24 時間のレプリケーション ポリシーがあり、24 時間以上前に何らかの問題により復旧ポイントが生成されなかった場合はどうなりますか。 以前の復旧ポイントは取り除かれますか?
いいえ、この場合、以前のすべての復旧ポイントが Site Recovery によって保持されます。 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM でレプリケーションを有効にした後で、レプリケーション ポリシーを変更するにはどうしたらよいですか? 
[Site Recovery コンテナー]、[Site Recovery インフラストラクチャ]、[レプリケーション ポリシー] の順に移動します。 編集するポリシーを選択し、変更を保存します。 変更は既存のすべてのレプリケーションにも適用されます。 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>すべての復旧ポイントが VM の完全なコピーですか、それとも差分ですか?
初回レプリケーションの場合、生成される最初の復旧ポイントには完全なコピーが含まれ、以降の復旧ポイントには差分変更が含まれます。

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>復旧ポイントのリテンション期間を長くすると、ストレージ コストは増えますか?
はい、リテンション期間を 24 時間から 72 時間に増やすと、Site Recovery により追加の 48 時間分の復旧ポイントが保存されるため、ストレージ料金が発生します。 たとえば、1 つの復旧ポイントに 10 GB の差分変更が含まれ、GB あたりのコストが $0.16/月の場合、1 か月あたり $1.6 * 48 の追加料金が発生します。

## <a name="failover"></a>フェールオーバー

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または Site Recovery [PowerShell](azure-to-azure-powershell.md) を使用してフェールオーバーをトリガーすることもできます。 

### <a name="can-i-retain-public-ip-address-after-failover"></a>フェールオーバー後にパブリック IP アドレスを保持できますか?

生産アプリケーションのパブリック IP アドレスは**フェールオーバー時に保持されません**。 フェールオーバー プロセスの一部として起動したワークロードは、ターゲット リージョンで利用可能なAzure パブリック IP リソースを割り当てる必要があります。 この手順は、手動でまたは復旧計画に基づき自動で行われます。 復旧計画を使用してパブリック IP アドレスを割り当てるには、[この記事](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan)を参照してください。  

### <a name="can-i-retain-private-ip-address-during-failover"></a>フェールオーバー中にプライベート IP アドレスを保持することはできますか?
はい、プライベート IP アドレスを保持することができます。 既定では、Azure VM のディザスター リカバリーを有効にすると、Site Recovery によって、ソース リソースの設定に基づいてターゲット リソースが作成されます。 Azure VM が静的 IP アドレスで構成されている場合、Site Recovery は、使用中でなければ同じ IP アドレスをターゲット VM にプロビジョニングしようとします。 さまざまな条件でプライベート IP アドレスを保持するには、[この記事](site-recovery-retain-ip-azure-vm-failover.md)を参照してください。

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>フェールオーバー後は、サーバーはソース VM と同じ IP アドレスを持ちません。 新しい IP アドレスが割り当てられるのはなぜですか?

Site Recovery は、フェールオーバー時に、ベスト エフォート ベースで IP アドレスを指定しようとします。 他の仮想マシンでその IP アドレスが取得されている場合、次に使用可能な IP アドレスがターゲットとして設定されます。 Site Recovery でアドレス指定がどのように処理されるかの詳しい説明については、[この記事](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)を参照してください。

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>最新 (最低 RPO) の復旧ポイントは何を意味しますか?
このオプションは、Site Recovery サービスに送信されたすべてのデータを最初に処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 このオプションは、フェールオーバー後に作成された VM は、フェールオーバーがトリガーされた時点で Site Recovery にレプリケートされたすべてのデータを保持しているため、RPO (目標復旧時点) を最も低くすることができます。

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>最新 (最低 RPO) の復旧ポイントは、フェールオーバー RTO に影響しますか?
はい、Site Recovery はフェールオーバーする前に保留中のすべてのデータを処理するため、このオプションは他と比較して RTO がより高くなります。

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>復旧ポイントで最後に処理されたオプションは何を意味しますか?
Site Recovery によって処理された最新の復旧ポイントに、計画内のすべての VM をフェールオーバーします。 特定の VM の最新の復旧ポイントを参照するには、VM 設定の [Latest Recovery Points]\(最新の復旧ポイント\) を確認します。 このオプションを使用すると、未処理のデータの処理に時間がかからないため、RTO (目標復旧時間) を低くできます。

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>2 つの Azure リージョン間でレプリケートしているときに、プライマリ リージョンに予期しない障害が発生するとどうなりますか?
停止後、フェールオーバーをトリガーできます。 Site Recovery では、フェールオーバーを実行するためにプライマリ リージョンからの接続を必要としません。

## <a name="recovery-plan"></a>復旧計画

### <a name="what-is-a-recovery-plan-"></a>復旧計画とは何ですか?
Site Recovery での復旧計画は、VM のフェールオーバーの復旧を調整します。 これは、復旧を常に正確で、繰り返し可能、さらに自動化されるようにするのに役立ちます。 復旧計画は、ユーザーの次のニーズに対応します。

- 一緒にフェールオーバーする仮想マシンのグループを定義する。
- アプリケーションが正確に動作するように、仮想マシン間の依存関係を定義する。
- 仮想マシンのフェールオーバー以外のタスクも実行できるように、カスタムの手動操作と共に復旧を自動化する。

[こちら](site-recovery-create-recovery-plans.md) をご覧ください。

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>復旧計画ではシーケンス処理はどのように実行されますか?

復旧計画では、シーケンス処理を実行するために複数のグループを作成できます。 すべてのグループが一度にフェールオーバーされます。これは、別のグループに続いて同じグループの一部である VM は一緒にフェールオーバーされることを意味します。 [復旧計画を使用してアプリケーションをモデル化する](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps)方法を確認してください。 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>復旧計画の RTO を検索するにはどうしたらよいですか?
復旧計画の RTO を確認するには、復旧計画をテスト フェールオーバーして、Site Recovery ジョブに移動します。
たとえば次に示すように、SAP 復旧計画のテストでは、すべての仮想マシンをフェールオーバーして指定されたアクションを実行するのに 8 分 59 秒を要しました。

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>復旧計画に Automation Runbook を追加できますか?
はい、Azure Automation Runbooks を復旧計画に組み込むことができます。 [詳細情報](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>再保護とフェールバック 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>プライマリ リージョンからディザスター リカバリー リージョンへのフェールオーバーを実行すると、DR リージョン内の VM は自動的に保護されますか?
いいえ、あるリージョンから別のリージョンに Azure VM を[フェールオーバー](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)すると、その VM は DR リージョン内で保護されていない状態で起動されます。 プライマリ リージョンに VM をフェールバックするには、セカンダリ リージョン内の VM を[再保護](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)する必要があります。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>再保護時に、Site Recovery ではセカンダリ リージョンからプライマリ リージョンに完全なデータがレプリケートされますか?
状況によって異なります。たとえば、ソース リージョンの VM が存在している場合、ソース ディスクとターゲット ディスクの間の変更のみが同期されます。 両方のディスクを比較することによって差分が計算され、転送されます。 これが完了するまでには通常、数時間かかります。 再保護中に発生することについての詳細は、[この記事]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)を参照してください。

### <a name="how-much-time-does-it-take-to-failback"></a>フェールバックにはどのくらい時間がかかりますか?
再保護が完了すると、プライマリ リージョンからセカンダリ リージョンへのフェールオーバーにかかる時間と同じくらいです。 

## <a name="security"></a>セキュリティ
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Site Recovery サービスにレプリケーション データが送信されますか。
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンでの実行内容に関するどのような情報を持つこともありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  
Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
はい、転送中の暗号化と [Azure での暗号化](https://docs.microsoft.com/azure/storage/storage-service-encryption)の両方がサポートされています。

## <a name="next-steps"></a>次の手順
* サポート要件を[確認する](azure-to-azure-support-matrix.md)。
* Azure から Azure へのレプリケーションを[設定する](azure-to-azure-tutorial-enable-replication.md)。
