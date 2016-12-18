---
title: "Azure Site Recovery を使用した Hyper-V レプリケーション | Microsoft Docs"
description: "この記事を使用すると、Azure Site Recovery を正常にインストール、構成、管理するのに役立つ技術的な概念を理解できます。"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: mkjain
editor: 
ms.assetid: 97916915-1379-47df-8369-12ddf022c4da
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: b49771ff1e29aeb6ec582c21061085504705991b


---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>Azure Site Recovery での Hyper-V レプリケーション
この記事では、Azure Site Recovery を使用した Hyper-V サイトまたは System Center Virtual Machine Manager (VMM) サイトと Azure の間の保護を正常に構成および管理するのに役立つ、技術的な概念について説明します。

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>オンプレミスと Azure の間のレプリケーションに使用するソース環境のセットアップ
オンプレミスと Azure との間で障害復旧をセットアップする際は必ず、その作業の一環として Azure Site Recovery プロバイダーをダウンロードし、VMM サーバーにインストールしてください。 個々の Hyper-V ホストには、Azure Recovery Services エージェントをインストールします。

![VMM site deployment for replication between on-premises and Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Hyper-V によって管理されるインフラストラクチャにソース環境をセットアップする作業は、VMM によって管理されたインフラストラクチャにソース環境をセットアップする作業と似ています。 唯一の違いは、プロバイダーとエージェントのインストール先が Hyper-V ホスト自体であるという点です。 VMM 環境では、プロバイダーが VMM サーバーに、エージェントが Hyper-V ホストにインストールされます (Azure にレプリケーションする場合)。

## <a name="workflows"></a>ワークフロー
### <a name="enable-protection"></a>保護を有効にする
Azure Portal またはオンプレミスから仮想マシンの保護を実行すると、" **保護を有効にする** " という名前の Site Recovery ジョブが開始されます。 このジョブは、 **[ジョブ]** タブで監視できます。

!["Enable protection" job in the list of jobs](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

**[保護を有効にする]** ジョブによって前提条件がチェックされた後、 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) メソッドが呼び出されます。 このメソッドは、保護の適用時に構成された入力を使用して、Azure へのレプリケーションを作成します。

" **保護を有効にする** " ジョブは、 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) メソッドを呼び出すことによって、オンプレミスからの初期レプリケーションを開始します。 このメソッドは、仮想マシンの仮想ディスクを Azure に送信します。

![Details for the "Enable protection" job](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>仮想マシンの保護の最終処理
初期レプリケーションがトリガーされると [Hyper-V VM スナップショット](https://technet.microsoft.com/library/dd560637.aspx) が作成されます。 仮想ハード ディスクは、すべてのディスクが Azure にアップロードされるまで 1 つずつ処理されます。 通常、この処理の完了には、ディスク サイズと帯域幅に応じて、時間がかかります。 ネットワーク使用量の最適化については、 [Azure 保護ネットワークの帯域幅使用量のオンプレミスでの管理方法](https://support.microsoft.com/kb/3056159)に関するページを参照してください。

初期レプリケーションが完了すると、 **仮想マシンでの保護の最終処理** ジョブにより、ネットワークとレプリケーション後の設定が構成されます。 初期レプリケーションの進行中に次の処理が実行されます。

* ディスクへのすべての変更が追跡されます。
* スナップショットと Hyper-V Replica Log (HRL) ファイル用のディスク記憶域が別途消費されます。

初期レプリケーションが完了すると、Hyper-V VM スナップショットが削除されます。 この削除により、初期レプリケーションの後に行われたデータの変更が親ディスクにマージされます。

!["Finalize protection on the virtual machine" job in the list of jobs](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>差分レプリケーション
Hyper-V レプリカ レプリケーション トラッカーは、Hyper-V レプリカ レプリケーション エンジンの一部で、仮想ハード ディスクに対する変更を Hyper-V レプリカ ログ (*.hrl) ファイルとして追跡します。 HRL ファイルは、関連付けられているディスクと同じディレクトリにあります。

レプリケーション用に構成された各ディスクには、関連付けられた HRL ファイルがあります。 このログは、初期レプリケーションの完了後、顧客のストレージ アカウントに送信されます。 ログが Azure に送信される間、プライマリでの変更は同じディレクトリ内の別のログ ファイルで追跡されます。

[仮想マシンのレプリケーションの正常性の監視](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines)に関するセクションで説明されているように、初期レプリケーションまたは差分レプリケーション中の VM のレプリケーションの正常性は VM ビューで監視できます。  

### <a name="resynchronization"></a>再同期
差分レプリケーションが失敗し、完全な初期レプリケーションのコストがネットワーク帯域幅または時間の面で高くなる場合、仮想マシンは再同期の対象としてマークが付きます。 たとえば、HRL ファイルのサイズが合計ディスク サイズの 50% まで増大すると、仮想マシンは再同期の対象としてマークが付きます。 再同期では、ソースとターゲットの仮想マシン ディスクのチェックサムを計算して差分のみを送信することで、ネットワーク経由で送信されるデータの量が最小限に抑えられます。

再同期が完了すると、通常の差分レプリケーションが再開されます。 ネットワークの停止など何らかの機能不全が発生した場合は、再同期を再開することができます。

既定では、自動的にスケジュールされた再同期は営業時間外に実行するように構成されます。 仮想マシンを手動で再同期する必要がある場合、ポータルから仮想マシンを選択し、 **[再同期]**をクリックします。

![Manual resynchronization](media/site-recovery-understanding-site-to-azure-protection/image04.png)

再同期では、ソース ファイルとターゲット ファイルを固定チャンクに分割する固定ブロック チャンク アルゴリズムが使用されます。 チャンクごとにチェックサムを生成し、比較することによって、ソース側のどのブロックをターゲットに適用すべきかが判断されます。

### <a name="retry-logic"></a>再試行ロジック
レプリケーション エラーが発生した場合に備えて、組み込みの再試行ロジックがあります。 このロジックは、次の 2 つのカテゴリに分類できます。

| カテゴリ | シナリオ |
| --- | --- |
| 回復不可能なエラー |再試行は行われません。 仮想マシンのレプリケーションの状態が **[重大]** と表示され、管理者による操作が必要になります。 たとえば、次のようになります。 <ul><li>壊れた VHD チェーン</li><li>レプリカ仮想マシンについて無効な状態</li><li>ネットワーク認証エラー</li><li>認証エラー</li><li>仮想マシンが見つからない (スタンドアロン Hyper-V サーバーの場合)</li></ul> |
| 回復可能なエラー |レプリケーションを実行するたびに、指数関数的バックオフを使用した間隔で再試行が行われます。指数関数的バックオフでは、最初の試行が開始された後、再試行の間隔が指数関数的に長くなります (1、2、4、8、10 分)。 エラーが解決しない場合は、30 分ごとに再試行してください。 たとえば、次のようになります。 <ul><li>ネットワーク エラー</li><li>ディスク領域不足</li><li>メモリ不足状態</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Hyper-V 仮想マシンの保護と回復のライフ サイクル
![Hyper-V 仮想マシンの保護と回復のライフ サイクル](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>その他のリファレンス
* [VMware、VMM、Hyper-V、および物理サイトの監視とトラブルシューティング](site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft サポートを得る](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
* [Azure Site Recovery の一般的なエラーとその解決策](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)



<!--HONumber=Nov16_HO3-->


