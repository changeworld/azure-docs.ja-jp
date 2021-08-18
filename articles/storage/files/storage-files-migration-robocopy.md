---
title: RoboCopy を使用して Azure ファイル共有に移行する
description: RoboCopy を使用して複数の場所から Azure ファイル共有にファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/12/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 76fb327eaf2f95bd96513f6382a00e29a54fe893
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462248"
---
# <a name="use-robocopy-to-migrate-to-azure-file-shares"></a>RoboCopy を使用して Azure ファイル共有に移行する

この移行に関する記事では、RoboCopy を使用してファイルを Azure ファイル共有に移動または移行する方法について説明します。 RoboCopy は、移行に適した機能セットを備えた、信頼できるよく知られているファイル コピー ユーティリティです。 これには SMB プロトコルが使用されます。そのため、SMB をサポートする任意のソースとターゲットの組み合わせに幅広く適用できます。

> [!div class="checklist"]
> * データ ソース: SMB プロトコルをサポートする任意のソース。ネットワーク接続ストレージ (NAS)、Windows または Linux サーバー、別の Azure ファイル共有、その他多数
> * 移行経路: ソース ストレージ &rArr; Windows マシン (RoboCopy 使用) &rArr; Azure ファイル共有

ソースとデプロイのさまざまな組み合わせに応じて、異なる多数の移行経路があります。 [移行ガイドの表](storage-files-migration-overview.md#migration-guides)を参照して、ご自身のニーズに最適な移行を見つけてください。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="azcopy-vs-robocopy"></a>AzCopy と RoboCopy
AzCopy と RoboCopy の 2 つは、基本的に異なるファイル コピー ツールです。 RoboCopy には、任意のバージョンの SMB プロトコルが使用されます。 AzCopy は、ターゲットが Azure Storage 内にある場合に限ってデータの移動に使用できる "クラウド内で生まれた" ツールです。 AzCopy は REST プロトコルに依存します。

RoboCopy は、信頼できる Windows ベースのコピー ツールとして、完全な忠実性を維持してファイルをコピーすることについては優れた優位性があります。 RoboCopy を使用すると、豊富な機能セットがあってファイルとフォルダーを完全な忠実性を維持してコピーできるため、多くの移行シナリオがサポートされます。 可能な最大限の忠実性を維持してファイルをコピーすることの重要性について詳細は、[移行の概要に関する記事にあるファイルの忠実性に関するセクション](storage-files-migration-overview.md#migration-basics)を参照してください。

一方、AzCopy は最近になって、一定の忠実性を維持したファイル コピーをサポートするように拡張され、移行ツールとして検討対象になるために必要な最初の機能が追加されたばかりです。 ただし、まだ差があり、AzCopy フラグと RoboCopy フラグを比較するときに機能について誤解が生じやすいと言えます。

たとえば、*RoboCopy /MIR* を使用すると、ソースがターゲットにミラー化されます。つまり、追加、変更、削除されたファイルが考慮されます。 *AzCopy -sync* との重要な違いは、ソース上で削除されたファイルはターゲット上で削除されないという点です。 これにより、不完全な差分コピー機能セットが作成されます。 AzCopy は常に進化しています。 Azure ファイル共有をターゲットとする移行シナリオの場合、現時点では、AzCopy は推奨されるツールではありません。 

## <a name="migration-goals"></a>移行の目標

目標は、既存のファイル共有の場所から Azure にデータを移動することです。 Azure を使用すると、Windows Server を必要とせずに使用できるネイティブの Azure ファイル共有にデータが格納されます。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

移行プロセスは、いくつかのフェーズで構成されます。 Azure ストレージ アカウントとファイル共有をデプロイする必要があります。 さらに、ネットワークを構成し、DFS 名前空間のデプロイ (DFS-N) を検討したり、既存のものを更新したりします。 実際にデータをコピーするときになったら、ダウンタイムを最小限に抑えるために、差分 RoboCopy を繰り返し実行することを検討する必要があります。最後に、新しく作成された Azure ファイル共有にユーザーをカットオーバーします。 以下のセクションでは、移行プロセスの各フェーズについて詳しく説明します。

> [!TIP]
> この記事に戻った場合は、右側にあるナビゲーションを使用して、中断した移行フェーズに移動してください。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>フェーズ 1:必要な Azure ファイル共有の数を特定する

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>フェーズ 2: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-preparing-to-use-azure-file-shares"></a>フェーズ 3: Azure ファイル共有を使用するための準備

このフェーズの情報を使用すると、Azure のサーバーとユーザーが Azure ファイル共有を利用できるようにする方法を決めることができます。 最も重要な決定事項は次のとおりです。

- **ネットワーク:** ネットワークで SMB トラフィックをルーティングできるようにします。
- **認証:** Kerberos 認証用に Azure ストレージ アカウントを構成します。 AdConnect とドメインがストレージ アカウントに参加すると、アプリとユーザーが認証に AD ID を使用できるようになります。
- **承認:** Azure ファイル共有ごとに共有レベルの ACL を使用すると、AD ユーザーとグループが特定の共有にアクセスできるようになり、Azure ファイル共有内でネイティブ NTFS ACL が引き継がれます。 ファイルとフォルダーの ACL に基づく承認は、オンプレミスの SMB 共有の場合と同様に機能します。
- **ビジネス継続性:** 既存の環境への Azure ファイル共有の統合には、多くの場合、既存の共有アドレスを保持することが伴います。 まだ [DFS 名前空間](files-manage-namespaces.md)を使用していない場合は、環境内でそれを確立することを検討してください。 ユーザーとスクリプトが使用する共有アドレスを変更せずに維持できます。 DFS-N でクライアントを Azure ファイル共有にリダイレクトすると、SMB の名前空間ルーティング サービスが提供されます。

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
* [DFS-N を構成する](files-manage-namespaces.md)
   :::column-end:::
:::row-end:::

### <a name="mounting-an-azure-file-share"></a>Azure ファイル共有のマウント

RoboCopy を使用する前に、Azure ファイル共有に SMB 経由でアクセスできるようにする必要があります。 最も簡単な方法は、ローカル ネットワーク ドライブとして、RoboCopy に使用する予定の Windows Server に共有をマウントすることです。 

> [!IMPORTANT]
> Azure ファイル共有をローカル Windows Server に正常にマウントする前に、「フェーズ 3: Azure ファイル共有を使用するための準備」を完了しておく必要があります。

準備ができたら、「[Windows で Azure ファイル共有を使用する](storage-how-to-use-files-windows.md)」のハウツー記事をご確認ください。 それから、RoboCopy を開始する Azure ファイル共有をマウントします。

## <a name="phase-4-robocopy"></a>フェーズ 4: RoboCopy

次の RoboCopy コマンドを実行すると、ソース ストレージから Azure ファイル共有に差分 (更新されたファイルおよびフォルダー) のみがコピーされます。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> RoboCopy が運用環境に影響を与えたり、多くのエラーを報告したり、予想どおりの速度で進行していない場合、[トラブルシューティングのセクションを確認](#troubleshoot-and-optimize)してください。

## <a name="phase-5-user-cut-over"></a>フェーズ 5:ユーザーのカットオーバー

RoboCopy コマンドを初めて実行する時点では、ユーザーとアプリケーションがまだ移行のソース上のファイルにアクセスしていて、それを変更する可能性があります。 RoboCopy で、ある 1 つのディレクトリを処理してから次に進んだ後、ソースの場所でユーザーがファイルを追加、変更、または削除する可能性があり、それは現在の RoboCopy の実行では処理されません。 これは正しい動作です。

最初の実行では、チャーンされたデータの大部分を Azure ファイル共有に移動します。 この最初のコピーには、時間がかかることがあります。 RoboCopy の速度に影響する可能性のある点の詳細については、[トラブルシューティング セクション](#troubleshoot-and-optimize)をご覧ください。

最初の実行が完了した後、コマンドを再度実行します。

同じ同期に対して RoboCopy を 2 回目に実行したときは、前回の実行以降に発生した変更のみを転送すればよいため、処理は短時間で完了します。 同じ共有に対してジョブを繰り返し実行できます。

ダウンタイムが許容可能と考えられる場合は、ソースの共有にユーザーがアクセスできないようにする必要があります。 これは、ユーザーがファイルとフォルダー構造およびコンテンツを変更できないようにするいずれの手順でも行えます。 たとえば、DFS-Namespace を存在しない場所に指定したり、各共有の ACL を変更したりすることなどです。

最後に 1 回 RoboCopy ラウンドを実行します。 それにより、漏れている可能性があるすべての変更が取得されます。
この最後の手順にかかる時間は、RoboCopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

前のセクションでは、ユーザーが[自分の ID で共有にアクセス](#phase-3-preparing-to-use-azure-file-shares)するように構成し、また、ユーザーが[新しい Azure ファイル共有 (DFS-N) への確立されたパスを使用する](files-manage-namespaces.md)ための戦略を確立しました。

異なるソースとターゲット共有の間でこれらのコピーのいくつかを並行して実行することができます。 これを行う場合は、システムに過剰に負荷がかからないようなネットワーク スループットおよびコアとスレッドの数の比率を維持してください。

## <a name="troubleshoot-and-optimize"></a>トラブルシューティングと最適化

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>次の手順

Azure ファイル共有については、さらに知るべきことがあります。 以下の記事では、高度なオプション、ベスト プラクティスについて説明します。トラブルシューティングのヘルプもあります。 これらの記事は、それぞれに対応する [Azure ファイル共有のドキュメント](storage-files-introduction.md)にリンクしています。

* [移行の概要](storage-files-migration-overview.md)
* [バックアップ: Azure ファイル共有スナップショット](storage-snapshots-files.md)
* [Azure Files で DFS 名前空間を使用する方法](files-manage-namespaces.md)
