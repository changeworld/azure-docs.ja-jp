---
title: オンプレミスの NAS を Azure ファイル共有に移行する
description: オンプレミスのネットワーク接続ストレージ (NAS) の場所から、Azure ファイル共有に、Azure DataBox によってファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491682"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>DataBox を使用してネットワーク接続ストレージ (NAS) から Azure ファイル共有に移行する

これは、NAS および Azure DataBox のキーワードが関係する移行に関する記事の 1 つです。 この記事がご使用のシナリオに当てはまるかどうかを確認してください。

> [!div class="checklist"]
> * データ ソース: ネットワーク接続ストレージ (NAS)
> * 移行ルート: NAS &rArr; DataBox &rArr; Azure ファイル共有
> * オンプレミスでのファイルのキャッシュ: いいえ。最終的な目標は、Azure ファイル共有をクラウドで直接使用することです。 Azure File Sync を使用する計画はありません。

自分のシナリオが異なる場合は、[移行ガイドの表](storage-files-migration-overview.md#migration-guides)を参照してください。

この記事では、NAS アプライアンスから機能している Azure ファイル共有に移行するために必要な計画、デプロイ、およびネットワークの構成について詳しく説明します。 このガイドでは、一括データ転送 (オフライン データ転送) に Azure DataBox を使用します。

## <a name="migration-goals"></a>移行の目標

目標は、お使いの NAS アプライアンス上の共有を Azure に移行し、Windows Server を必要とせずに使用できるネイティブの Azure ファイル共有にすることです。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

移行プロセスは、いくつかのフェーズで構成されます。 Azure ストレージ アカウントとファイル共有をデプロイし、ネットワークを構成し、Azure DataBox を使用して移行し、RoboCopy を使用して変更をキャッチアップし、最後に新しく作成された Azure ファイル共有にユーザーをカットオーバーする必要があります。 以下のセクションでは、移行プロセスの各フェーズについて詳しく説明します。

> [!TIP]
> この記事に戻った場合は、右側にあるナビゲーションを使用して、中断した移行フェーズに移動してください。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>フェーズ 1:必要な Azure ファイル共有の数を特定する

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>フェーズ 2: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>フェーズ 3: 必要な Azure DataBox アプライアンスの数を決定する

前のフェーズを完了したときにのみ、この手順を開始してください。 この時点で、Azure ストレージ リソース (ストレージ アカウントとファイル共有) が作成されているはずです。 DataBox を注文するときに、DataBox でのデータの移動先となるストレージ アカウントを指定する必要があります。

このフェーズでは、前のフェーズからの移行プランの結果を、使用可能な DataBox オプションの制限にマッピングする必要があります。 これらの考慮事項は、選択する必要がある DataBox オプションと、それらのうちいくつが NAS 共有を Azure ファイル共有に移動するために必要になるかについて計画を立てるのに役立ちます。

必要なデバイスの種類と数を決定する際、次の重要な制限事項を考慮してください。

* すべての Azure DataBox は、最大 10 個のストレージ アカウントにデータを移動できます。 
* DataBox の各オプションには、それら独自の使用可能な容量があります。 「[DataBox のオプション](#databox-options)」を参照してください。

作成することを決定したストレージ アカウントの数とそれぞれの共有については、移行プランを参照してください。 次に、NAS 上でそれぞれの共有のサイズを確認します。 この情報を組み合わせることで、どのアプライアンスからどのストレージ アカウントにデータを送信するかを最適化し、決定することができます。 2 つの DataBox デバイスから、ファイルを同じストレージ アカウントに移動することはできますが、1 つのファイル共有のコンテンツを 2 つの DataBox に分割しないでください。

### <a name="databox-options"></a>DataBox のオプション

標準的な移行の場合、次に示す 3 種類の DataBox のオプションから、いずれか、または組み合わせて選択する必要があります。 

* DataBox Disks: 容量が 8 TiB の SSD ディスクが 1 台から最大 5 台 (最大で合計 40 TiB) が Microsoft からお客様に発送されます。 暗号化とファイル システムのオーバーヘッドにより、使用可能な容量は約 20% 少なくなります。 詳細については、[DataBox Disks のドキュメント](../../databox/data-box-disk-overview.md)を参照してください。
* DataBox: これが最も一般的なオプションです。 NAS と同様に機能する、耐久性のある DataBox アプライアンスがお客様に発送されます。 使用可能な容量は 80 TiB です。 詳細については、[DataBox のドキュメント](../../databox/data-box-overview.md)をご覧ください。
* DataBox Heavy: このオプションは、NAS と同様に機能する、耐久性のある車輪付きの DataBox アプライアンスで、容量は 1 PiB です。 暗号化とファイル システムのオーバーヘッドにより、使用可能な容量は約 20% 少なくなります。 詳細については、[DataBox Heavy のドキュメント](../../databox/data-box-heavy-overview.md)を参照してください。

## <a name="phase-4-provision-a-temporary-windows-server"></a>フェーズ 4: 一時的な Windows Server をプロビジョニングする

Azure DataBox が到着するのを待っている間に、RoboCopy ジョブを実行するために必要な 1 つ以上の Windows Server を既にデプロイすることができます。 

- これらのサーバーの 1 回目の使用では、ファイルを DataBox にコピーします。
- これらのサーバーの 2 回目の使用では、DataBox が輸送中に NAS アプライアンスで発生した変更をキャッチアップします。 この方法では、ソース側のダウンタイムが最小限に抑えられます。

RoboCopy ジョブが動作する速度は主に次の要因に依存します。

* ソース ストレージとターゲット ストレージの IOPS
* これらの間で使用可能なネットワーク帯域幅 </br> 詳細については、トラブルシューティング セクションの「[IOPS と帯域幅に関する考慮事項](#iops-and-bandwidth-considerations)」をご覧ください。
* 名前空間内のファイルとフォルダーを迅速に処理する機能 </br> 詳細については、トラブルシューティング セクションの「[処理速度](#processing-speed)」をご覧ください。
* RoboCopy 実行間の変更の数 </br> 詳細については、トラブルシューティング セクションの「[不要な作業を回避する](#avoid-unnecessary-work)」をご覧ください。

一時的な Windows Server に提供する RAM とスレッド数を決定する際には、参照される詳細を念頭に置くことが重要です。

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>フェーズ 5: Azure ファイル共有を使用するための準備

時間を節約するには、DataBox が到着するのを待っている間、このフェーズを続行してください。 このフェーズの情報を使用すると、Azure のサーバーとユーザーが Azure ファイル共有を利用できるようにする方法を決めることができます。 最も重要な決定事項は次のとおりです。

- **ネットワーク:** ネットワークで SMB トラフィックをルーティングできるようにします。
- **認証:** Kerberos 認証用に Azure ストレージ アカウントを構成します。 AdConnect とドメインがストレージ アカウントに参加すると、アプリとユーザーが認証に AD ID を使用できるようになります。
- **承認:** Azure ファイル共有ごとに共有レベルの ACL を使用すると、AD ユーザーとグループが特定の共有にアクセスできるようになり、Azure ファイル共有内でネイティブ NTFS ACL が引き継がれます。 ファイルとフォルダーの ACL に基づく承認は、オンプレミスの SMB 共有の場合と同様に機能します。
- **ビジネス継続性:** 既存の環境への Azure ファイル共有の統合には、多くの場合、既存の共有アドレスを保持することが伴います。 まだ DFS 名前空間を使用していない場合は、環境内でそれを確立することを検討してください。 ユーザーとスクリプトが使用する共有アドレスを変更せずに維持できます。 移行後に DFS 名前空間ターゲットを Azure ファイル共有にリダイレクトすることによって、SMB の名前空間ルーティングサービスとして DFS-N を使用します。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        このビデオは、簡単な 5 つのステップでインフォメーション ワーカーやアプリに直接 Azure ファイル共有を安全に公開する方法についてのガイドとデモです。</br>
        ビデオでは、いくつかのトピックに関する専用のドキュメントが参照されています。

* [ID の概要](storage-files-active-directory-overview.md)
* [ストレージ アカウントにドメインを参加させる方法](storage-files-identity-auth-active-directory-enable.md)
* [Azure ファイル共有のネットワークの概要](storage-files-networking-overview.md)
* [パブリックおよびプライベート エンドポイントを構成する方法](storage-files-networking-endpoints.md)
* [S2S VPN を構成する方法](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN を構成する方法](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN を構成する方法](storage-files-configure-p2s-vpn-linux.md)
* [DNS 転送を構成する方法](storage-files-networking-dns.md)
* [DFS-N を構成する](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>フェーズ 6: ファイルを DataBox にコピーする

DataBox が届いたら、NAS アプライアンスから認識できる経路に DataBox をセットアップする必要があります。 注文した種類の DataBox のセットアップ ドキュメントに従ってください。

* [Data Box を設定する](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk を設定する](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy を設定する](../../databox/data-box-heavy-quickstart-portal.md)

DataBox の種類によっては、DataBox コピー ツールが使用できる場合もあります。 現時点では、これらはファイルを完全な忠実性で DataBox にコピーするものではないため、Azure ファイル共有への移行には推奨されません。 代わりに RoboCopy を使用してください。

DataBox が届くと、注文時に指定したストレージ アカウントごとに事前プロビジョニングされた SMB 共有が利用できます。

* ファイルが Premium の Azure ファイル共有に配置される場合は、Premium の "ファイル ストレージ" のストレージ アカウントごとに 1 つの SMB 共有が存在します。
* ファイルが Standard ストレージ アカウントに配置される場合、Standard の (GPv1 および GPv2) ストレージ アカウントごとに 3 つの SMB 共有が存在します。 `_AzFiles` で終わるファイル共有のみが、移行に関連します。 すべてのブロックおよびページの BLOB 共有を無視します。

Azure DataBox のドキュメントの手順に従ってください。

1. [Data Box に接続する](../../databox/data-box-deploy-copy-data.md)
1. Data Box にデータをコピーする
1. [Azure の出発点として DataBox を準備する](../../databox/data-box-deploy-picked-up.md)

リンクされた DataBox ドキュメントには、RoboCopy コマンドが指定されています。 ただし、このコマンドは、ファイルとフォルダーの完全な忠実性を維持するのに適していません。 代わりに次のコマンドを使用します。

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* 個々の RoboCopy フラグの詳細については、後の [RoboCopy セクション](#robocopy)の表をご覧ください。
* スレッド数 `/MT:n` を適切にサイズ変更する方法、RoboCopy 速度を最適化する方法、および RoboCopy をデータ センターの良好な近傍にする方法の詳細については、[RoboCopy のトラブルシューティング セクション](#troubleshoot)をご覧ください。


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>フェーズ 7: NAS からのキャッチアップ RoboCopy を実行する

すべてのファイルとフォルダーが計画された Azure ファイル共有に配置されたことを DataBox が報告したら、このフェーズに進むことができます。
キャッチアップ RoboCopy は、DataBox のコピーが開始されてから、NAS 上のデータが変更されている可能性がある場合にのみ必要です。 アーカイブの目的で共有を使用する特定のシナリオでは、移行が完了するまで、NAS での共有の変更を停止することができます。 移行中に NAS 共有を読み取り専用に設定することによって、ビジネス要件に対応することもできます。

移行中に共有を読み取り/書き込み可能にする必要があり、小規模なダウンタイム期間で行う必要がある場合、このキャッチアップ RoboCopy の手順は、ユーザーが Azure ファイル共有に直接フェールオーバーする前に完了することが重要です。

この手順では、共有を DataBox.にフォークした時点よりも後に NAS の最新の変更内容を使用してクラウド共有をキャッチアップするために、RoboCopy ジョブを実行します。
このキャッチアップ RoboCopy は、NAS 共有で発生したチャーンの量によって、短時間で終了したり、しばらく時間がかかったりすることがあります。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

1. NAS アプライアンス上で最初の場所を特定します。
1. 一致する Azure ファイル共有を特定します。
1. Azure ファイル共有を一時的な Windows Server のローカル ネットワーク ドライブとしてマウントする
1. 次のように、RoboCopy を使用してコピーを開始します。

### <a name="mounting-an-azure-file-share"></a>Azure ファイル共有のマウント

RoboCopy を使用する前に、Azure ファイル共有に SMB 経由でアクセスできるようにする必要があります。 最も簡単な方法は、ローカル ネットワーク ドライブとして、RoboCopy に使用する予定の Windows Server に共有をマウントすることです。 

> [!IMPORTANT]
> Azure ファイル共有をローカル Windows Server に正常にマウントする前に、「Azure ファイル共有を使用するための準備」フェーズを完了しておく必要があります。

準備ができたら、「[Windows で Azure ファイル共有を使用する](storage-how-to-use-files-windows.md)」のハウツー記事を確認し、NAS のキャッチアップ RoboCopy を開始する Azure ファイル共有をマウントします。

### <a name="robocopy"></a>RoboCopy

次の RoboCopy コマンドを実行すると、NAS ストレージから Azure ファイル共有に差分 (更新されたファイルおよびフォルダー) のみがコピーされます。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> RoboCopy が運用環境に影響を与えたり、多くのエラーを報告したり、予想どおりの速度で進行していない場合、[トラブルシューティングのセクションを確認](#troubleshoot)してください。

### <a name="user-cut-over"></a>ユーザーのカットオーバー

RoboCopy コマンドを初めて実行するときは、ユーザーとアプリケーションがまだ NAS 上のファイルにアクセスしていて、それを変更する可能性があります。 RoboCopy があるディレクトリを処理し、次のディレクトリに移動した後、ソースの場所 (NAS) のユーザーがファイルを追加、変更、または削除し、現在の RoboCopy の実行では処理されない可能性があります。 これは正しい動作です。

最初の実行では、チャーンされたデータの大部分を Azure ファイル共有に移動します。 この最初のコピーには、時間がかかることがあります。 RoboCopy の速度に影響する可能性のある点の詳細については、[トラブルシューティング セクション](#troubleshoot)をご覧ください。

最初の実行が完了した後、コマンドを再度実行します。

同じ同期に対して RoboCopy を 2 回目に実行したときは、前回の実行以降に発生した変更のみを転送すればよいため、処理は短時間で完了します。 同じ共有に対してジョブを繰り返し実行できます。

ダウンタイムを許容できる場合は、NAS ベースの共有へのユーザー アクセスを削除する必要があります。 これは、ユーザーがファイルとフォルダー構造およびコンテンツを変更できないようにするいずれの手順でも行えます。 たとえば、自分の DFS 名前空間を存在しない場所に指定したり、共有のルート ACL を変更します。

最後に 1 回 RoboCopy ラウンドを実行します。 それにより、漏れている可能性があるすべての変更が取得されます。
この最後の手順にかかる時間は、RoboCopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 NAS SMB 共有と同じ共有レベルのアクセス許可を設定してください。 エンタープライズ クラスのドメイン参加 NAS を使用していた場合、ユーザーの SID は Active Directory に存在するユーザーと自動的に一致し、RoboCopy によってファイルとメタデータが完全に忠実にコピーされます。 NAS でローカル ユーザーを使用していた場合は、これらのユーザーを Windows Server のローカル ユーザーとして作成し直し、RoboCopy によって Windows Server に移動された既存の SID を、新しい Windows Server ローカル ユーザーの SID にマップする必要があります。

これで、共通のルートまたはボリュームへの共有または共有のグループの移行は完了しました。 (フェーズ 1 からのマッピングによって異なります)

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

## <a name="troubleshoot"></a>トラブルシューティング

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>次の手順

Azure ファイル共有については、さらに知るべきことがあります。 以下の記事では、高度なオプション、ベスト プラクティスについて説明します。トラブルシューティングのヘルプもあります。 これらの記事は、それぞれに対応する [Azure ファイル共有のドキュメント](storage-files-introduction.md)にリンクしています。

* [移行の概要](storage-files-migration-overview.md)
* [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [直接アクセスに関するネットワークの考慮事項](storage-files-networking-overview.md)
* [バックアップ: Azure ファイル共有スナップショット](storage-snapshots-files.md)
