<properties
	pageTitle="Azure Site Recovery を使用した Hyper-V レプリケーションについて | Microsoft Azure" 
	description="この記事を使用すると、Azure Site Recovery を正常にインストール、構成、管理するのに役立つ技術的な概念を理解できます。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/12/2016" 
	ms.author="anbacker"/>
 

# Azure Site Recovery を使用した Hyper-V レプリケーションについて

この記事では、Azure Site Recovery を使用した HYPER-V サイトまたは VMM サイトと Azure 間の保護を正常に構成および管理するのに役立つ、技術的な概念について説明します。

## コンポーネントについて

### オンプレミスと Azure の間のレプリケーションのための Hyper-V サイトまたは VMM サイトのデプロイ
 
オンプレミスと Azure の間で DR を設定するとき、Azure Site Recovery プロバイダーをダウンロードして VMM サーバーにインストールし、Azure Recovery Services エージェントを各 Hyper-V ホストにインストールする必要があります。

![オンプレミスと Azure の間のレプリケーションのための VMM サイトのデプロイ](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Hyper-V サイトのデプロイは VMM のデプロイと同じです。プロバイダーとエージェントが Hyper-V ホスト自体にインストールされるという点だけ異なります。

## ワークフローについて

### 保護の有効化
ポータルまたはオンプレミスから仮想マシンを保護すると、*保護の有効化*という名前の ASR ジョブが開始され、[ジョブ] タブで監視できます。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

*保護の有効化*ジョブは、保護中に構成された入力を使用して Azure にレプリケーションを作成する [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) を呼び出す前に前提条件をチェックします。*保護の有効化*ジョブは、仮想マシンの仮想ディスクを Azure に送信する [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) を呼び出すことによって、オンプレミスから初期レプリケーションを開始します。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### 保護の最終処理
初期レプリケーションがトリガーされると [HYPER-V VM スナップショット](https://technet.microsoft.com/library/dd560637.aspx)が作成されます。仮想ハード ディスクは、すべてのディスクが Azure にアップロードされるまで 1 つずつ処理されます。通常、この処理の完了には、ディスク サイズと帯域幅に応じて、時間がかかります。ネットワーク使用量の最適化については、[Azure 保護ネットワークの帯域幅使用量のオンプレミスでの管理方法](https://support.microsoft.com/kb/3056159)に関するページを参照してください。初期レプリケーションが完了すると、*仮想マシンでの保護の最終処理*ジョブにより、ネットワークとレプリケーション後の設定が構成されます。初期レプリケーションの処理中は、次の「差分レプリケーション」セクションで説明するように、ディスクに対するすべての変更が追跡されます。追加のディスク記憶域は、初期レプリケーションの処理中にスナップショット ファイルと HRL ファイルに使用されます。初期レプリケーションが完了すると、HYPER-V VM スナップショットが削除されます。その結果、初期レプリケーション後のデータ変更が親ディスクにマージされます。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### 差分レプリケーション
HYPER-V レプリカ レプリケーション トラッカーは、HYPER-V レプリカ レプリケーション エンジンの一部で、仮想ハード ディスクに対する変更を HYPER-V レプリケーション ログ (*.hrl) として追跡します。HRL ファイルは、関連付けられているディスクと同じディレクトリにあります。レプリケーション用に構成された各ディスクには、関連付けられた HRL ファイルがあります。このログは、初期レプリケーションの完了後、顧客のストレージ アカウントに送信されます。ログが Azure に送信される間、プライマリでの変更は同じディレクトリ内の別のログ ファイルで追跡されます。

[仮想マシンのレプリケーションの正常性の監視](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine)に関するセクションで説明されているように、初期レプリケーションまたは差分レプリケーション中の VM のレプリケーションの正常性は VM ビューで監視できます。

### 再同期 
差分レプリケーションが失敗し、完全な初期レプリケーションのコストがネットワーク帯域幅または完了に要する時間の面で高くなる場合、仮想マシンは再同期の対象としてマークが付きます。たとえば、HRL ファイルのサイズが合計ディスク サイズの 50% まで増大すると、仮想マシンは再同期の対象としてマークが付きます。再同期では、ソースとターゲットの仮想マシン ディスクのチェックサムを計算して差分のみを送信することで、ネットワーク経由で送信されるデータの量が最小限に抑えられます。

再同期が完了したら、通常の差分レプリケーションを再開する必要があります。再同期を再開できるのは、障害 (ネットワークの停止、VMM のクラッシュなど) が発生した場合です。

既定では、*自動的にスケジュールされた再同期*は営業時間外に構成されます。仮想マシンを手動で再同期する必要がある場合、ポータルから仮想マシンを選択し、[再同期] をクリックします。

![オンプレミスの Hyper-V に関する問題のトラブルシューティング](media/site-recovery-understanding-site-to-azure-protection/image04.png)

再同期では、固定ブロック チャンク アルゴリズムが使用されています。このアルゴリズムでは、ソース ファイルとターゲット ファイルを固定チャンクに分割されます。つまり、チャンクごとにチェックサムが生成され、それを比較することで、ソースのどのブロックをターゲットに適用する必要があるかが特定されます。

### 再試行ロジック
レプリケーション エラーが発生した場合に備えて、組み込みの再試行ロジックがあります。このロジックは、次に示すように 2 つのカテゴリに分類できます。

| カテゴリ | シナリオ |
|---------------------------|----------------------------------------------|
| 回復不可能なエラー | 再試行は行われません。仮想マシンのレプリケーションの状態が [重大] と表示され、管理者による操作が必要になります。次に例を示します。<ul><li>VHD チェーンの破損</li><li>レプリカ仮想マシンが無効な状態</li><li>ネットワーク認証エラー</li><li>承認エラー</li><li>スタンドアロン HYPER-V サーバーの場合に仮想マシンが見つからない</li></ul>|
| 回復可能なエラー | レプリケーションを実行するたびに、指数関数的バックオフを使用した間隔で再試行が行われます。指数関数的バックオフでは、最初の試行が開始された後、再試行の間隔が指数関数的に長くなります (1、2、4、8、10 分)。エラーが解決しない場合は、30 分ごとに再試行してください。次に例を示します。<ul><li>ネットワーク エラー</li><li>ディスク領域不足</li><li>メモリ不足</li></ul>|

## HYPER-V 仮想マシンの保護と回復のライフ サイクルについて

![HYPER-V の仮想マシンの保護と回復のライフ サイクルについて](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## その他のリファレンス

- [VMware、VMM、Hyper-V、および物理サイトの監視とトラブルシューティング](./site-recovery-monitoring-and-troubleshooting.md)
- [Microsoft サポートを得る](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [ASR の一般的なエラーとその解決策](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=AcomDC_0921_2016-->