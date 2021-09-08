---
title: Azure Data Box を使用して Azure File Sync にデータを移行する
description: Azure File Sync と互換性がある一括データをオフラインで移行します。ファイルの競合を回避し、サーバー上の最新の変更が含まれるご自身のファイル共有をキャッチアップすることで、ダウンタイムなしのクラウド移行を実現します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1fc3f423c1ca02c6acb995ab4469d7d33607000f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255542"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>Azure Data Box を使用して Azure File Sync にオフラインでデータを移行する

この移行に関する記事は、Azure File Sync および Azure Data Box というキーワードに当てはまる複数の記事の 1 つです。 この記事がご使用のシナリオに当てはまるかどうかを確認してください。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="この移行ガイドで説明されている 3 つの段階的なステップの表示。画像の横に詳細な説明が示されています。" lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * データ ソース: Windows Server 2012 R2 以降。Azure File Sync がインストールされ、元のファイル セットを指します。
        > * 移行ルート: Windows Server 2012 R2 以降 &rArr; Data Box &rArr; Azure File Sync &rArr; Windows Server の元のファイルの場所と同期
        > * オンプレミスのファイル キャッシュ: はい、最終的な目標は、現在の場所からファイルを同期させる Azure File Sync のデプロイです。 
        :::column-end:::
:::row-end:::

ご自身のオンプレミスの Windows Server から、別のファイル共有にデータの大部分を移動するには、Azure Data Box を使用するパスが有効です。その後、必要に応じて、元のソース サーバー上で Azure File Sync を追加できます。

使用できる移行パスはさまざまです。正しいパスに従うことが重要です。

* お使いのデータが Windows Server 2012 R2 以降にあり、AFS をそのサーバーにインストールし、元の場所を同期する。 このシナリオでは、必ずしもすべてのファイルをアップロードするのではなく、代わりに Data Box を使用し、ファイル同期を使用して継続的な変更に対応します。 この記事では、このシナリオを対象とした移行パスについて説明します。
* AFS をインストール *しない*、またはインストールできないソース、 たとえば、NAS (ネットワーク接続ストレージ) や別のサーバー上にデータがある。 新しい空のサーバーを作成し、そのサーバー上で Azure File Sync を使用します。 このシナリオの場合、この移行ガイドは適切でありません。 [NAS から Azure File Sync への DataBox を使用した移行](../files/storage-files-migration-nas-hybrid-databox.md)に関するページをご覧になるか、[移行の概要](../files/storage-files-migration-overview.md)ページで、ご自身のシナリオに合ったガイドを見つけてください。
* その他のすべてのシナリオについては、[Azure File Share 移行ガイドの表](../files/storage-files-migration-overview.md)をご確認ください。 この概要ページは、すべての移行シナリオの良い出発点になります。
 
## <a name="migration-overview"></a>移行の概要

移行プロセスは、いくつかのフェーズで構成されます。 次のことを行う必要があります。
- ストレージ アカウントとファイル共有をデプロイする。
- 1 つ以上の Azure Data Box デバイスをデプロイして、Windows Server 2012 R2 以降からデータを移動する。 
- 権限のあるアップロードで Azure File Sync を構成する。

以下のセクションでは、移行プロセスの各フェーズについて詳しく説明します。

> [!TIP]
> この記事に戻った場合は、画面の右側にあるナビゲーションを使用して、中断した移行フェーズにジャンプします。

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>フェーズ 1: 必要な Azure ファイル共有の数を決定する

この移行ガイドでは、ご自身のファイルを含むオンプレミスの直接接続ストレージ (DAS) を引き続き使用する必要があります。 Data Box はその場所から取り込まれ、Azure File Sync もその場所で設定されます。 NAS (ネットワーク接続ストレージ) は、この移行パスでは機能しません。

同期対象を決定するには、Azure File Sync の "*同期グループ*" を設定します。これにより、ファイル セットが同期する場所がそれぞれ決まります。 各同期グループには、少なくとも 1 つのサーバーの場所 ("*サーバー エンドポイント*" と呼ばれます)、1 つの Azureファイル共有 ("*クラウド エンドポイント*" と呼ばれます) があります。 

ファイル セットのサブ パスを、それぞれの Azure ファイル共有に同期させることができます。 つまり、ファイル セットを完全にカバーするように、複数の同期グループを設定します。 セクションの残りの部分では、使用できるオプションについて説明します。 ご自身のデータを再構築する必要がある場合は、このガイドの手順を続行する前に、まずは再構築を行い、Data Box を注文するか、同期を設定してください。 

> [!CAUTION]
> 移行を開始する前に、ファイルとフォルダーを、長期的にどうあるべきかを考えた構造にしておくことが必須です。 移行中、不必要にフォルダーを再構築することは避けます。 Azure Data Box を使用して Azure にファイルを初期一括転送を行うプラスの効果が少なくなってしまいます。

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>フェーズ 2: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>フェーズ 3: 必要な Azure Data Box アプライアンスの数を決定する

この手順は、必ず前のフェーズが完了してから開始してください。 この時点で、Azure ストレージ リソース (ストレージ アカウントとファイル共有) が作成されているはずです。 Data Box を注文するときは、Data Box によるデータの移動先のストレージ アカウントを指定する必要があります。

このフェーズでは、前のフェーズからの移行プランの結果を、使用可能な Data Box オプションの制限にマップする必要があります。 これらの考慮事項は、選択する Data Box オプションと、それらのうちいくつが NAS 共有を Azure ファイル共有に移動するために必要になるかについて計画を立てるのに役立ちます。

必要なデバイスの数とその種類を決定するには、次の重要な制限事項を考慮してください。

* どの Azure Data Box アプライアンスでも、最大 10 個のストレージ アカウントにデータを移動できます。 
* 各 Data Box オプションには、それ自身が使用できる容量が付属しています。 「[Data Box のオプション](#data-box-options)」を参照してください。

移行プランを参照して、作成することを決定したストレージ アカウントの数とそれぞれの共有を確認してください。 次に、NAS 上でそれぞれの共有のサイズを確認します。 この情報を組み合わせることで、どのアプライアンスからどのストレージ アカウントにデータを送信するかを最適化し、決定することができます。 2 つの Data Box デバイスから、ファイルを同じストレージ アカウントに移動することはできますが、1 つのファイル共有のコンテンツを 2 つの Data Box に分割しないでください。

### <a name="data-box-options"></a>Data Box のオプション

標準的な移行の場合、次に示す Data Box のオプションから、いずれか、または組み合わせて選択します。 

* **Data Box Disk**。
  容量がそれぞれ 8 TiB の SSD ディスクが 1 台から最大 5 台 (最大で合計 40 TiB) が Microsoft からお客様に発送されます。 暗号化とファイル システムのオーバーヘッドにより、使用できる容量は約 20% 少なくなります。 詳細については、[Data Box Disk のドキュメント](../../databox/data-box-disk-overview.md)を参照してください。
* **Data Box**。
  これが最も一般的なオプションです。 NAS と同じように動作するラグド Data Box アプライアンスが Microsoft からお客様に発送されます。 使用可能な容量は 80 TiB です。 詳細については、[Data Box のドキュメント](../../databox/data-box-overview.md)を参照してください。
* **Data Box Heavy**。
  このオプションは、NAS と同じように機能する、車輪付きのラグド Data Box アプライアンスです。 容量は 1 PiB です。 暗号化とファイル システムのオーバーヘッドにより、使用できる容量は約 20% 少なくなります。 詳細については、[Data Box Heavy のドキュメント](../../databox/data-box-heavy-overview.md)を参照してください。

## <a name="phase-4-copy-files-onto-your-data-box"></a>フェーズ 4: ファイルを Data Box にコピーする

Data Box が届いたら、NAS アプライアンスから認識できる経路にそれをセットアップする必要があります。 注文した Data Box の種類のセットアップ ドキュメントに従ってください。

* [Data Box のセットアップ](../../databox/data-box-quickstart-portal.md)。
* [Data Box Disk のセットアップ](../../databox/data-box-disk-quickstart-portal.md)。
* [Data Box Heavy のセットアップ](../../databox/data-box-heavy-quickstart-portal.md)。

Data Box の種類によっては、Data Box コピー ツールが使用できる場合があります。 現時点では、これらは完全な忠実性でファイルをコピーするものではないため、Azure ファイル共有への移行にはお勧めしません。 代わりに Robocopy を使用してください。

Data Box が届くと、注文時に指定したストレージ アカウントごとに事前プロビジョニングされた SMB 共有が利用できます。

* ファイルが Premium の Azure ファイル共有に配置される場合は、Premium の "ファイル ストレージ" のストレージ アカウントごとに 1 つの SMB 共有が存在します。
* ファイルが Standard ストレージ アカウントに配置される場合、Standard の (GPv1 および GPv2) ストレージ アカウントごとに 3 つの SMB 共有が存在します。 `_AzFiles` で終わるファイル共有のみが、移行に関連します。 すべてのブロックおよびページの BLOB 共有を無視します。

Azure Data Box のドキュメントの手順に従ってください。

1. [Data Box に接続します](../../databox/data-box-deploy-copy-data.md)。
1. データを Data Box にコピーします。
1. [Azure にアップロードするために Data Box を準備します](../../databox/data-box-deploy-picked-up.md)。

リンク先の Data Box ドキュメントには、Robocopy コマンドが指定されています。 このコマンドは、ファイルとフォルダーの完全な忠実性を維持するのには適していません。 代わりに次のコマンドを使用します。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>フェーズ 5: Azure File Sync クラウド リソースをデプロイする

このガイドを続行する前に、すべてのファイルが適切な Azure ファイル共有に到達するまで待ちます。 Data Box データの発送と取り込みの処理には時間がかかります。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>フェーズ 6: Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>フェーズ 7: 既存の Windows Server 上で Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows Server インスタンスを準備して、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [![サーバーエンド ポイントの作成ウィザードの Azure portal セクションが表示されます。Azure ファイル共有にデータをシードするシナリオに対応するチェックボックスが強調表示されています。このチェックボックスは、前に Data Box にコピーした場所と同じオンプレミスの場所に AFS を接続する場合にオンにします。](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        **サーバー エンドポイントの作成** ウィザードで、フォルダー パスの下のチェックボックスを利用します。 このオプションは、Azure ファイル共有内の構造と同じファイルおよびフォルダー構造を指すパスを入力した場合にのみ選択します (ファイルとフォルダーは、Data Box によってこの名前空間に移動されます)。 </br> </br> フォルダー階層が一致しない場合、その不一致は、自動的に解決できない差異として示されます。 不一致を避けなければ、Data Box プロセスに投資しても、メリットを得られません。 Azure ファイル共有内のすべてのデータが削除され、 すべてのデータを、ローカル サーバーからアップロードする必要が出てきます。 サーバーからの Azure Data Box による一括移行、および最新の変更によるクラウド共有のシームレスな更新のメリットを得るには、ディレクトリ構造が一致している必要があります。
:::column-end:::
:::row-end:::

> [!NOTE]
> このチェックボックスを有効にすると、**初期同期** モードが "*Azure ファイル共有内のファイルとフォルダーが、このサーバーのパス内のコンテンツで上書きされる*" ように設定されます。 このオプションは、同期グループ内の最初のサーバー エンドポイントに対してのみ使用できます。

この新しいサーバー エンドポイントに対して権限のあるアップロードを構成したら、必要に応じて、クラウドを使った階層化を有効にすることができます。

クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようにする Azure File Sync の機能です。 ローカル環境で関心のあるデータも、アクセスのパフォーマンスを上げるためにローカルにキャッシュされます。 クラウドを使った階層化は省略可能です。 これは Azure File Sync サーバー エンドポイントごとに個別に設定できます。 この機能を使用して、オンプレミスで固定のストレージ フットプリントを実現しながら、ユーザーにローカル パフォーマンス キャッシュを提供し、よりクールなデータをクラウドに格納します。

詳細については、「[クラウドの階層化の概要](../file-sync/file-sync-cloud-tiering-overview.md)」を参照してください。また、さまざまな[クラウド階層化ポリシー](../file-sync/file-sync-cloud-tiering-policy.md)を詳しく確認し、ローカル サーバー上でのキャッシュ/階層化の内容を微調整することもできます。

## <a name="complete-your-migration"></a>移行を完了する

サーバー エンドポイントの作成後、同期は機能しています。 ただし、同期では Azure Data Box を介して Azure ファイル共有に移動したファイルとフォルダーを列挙 (検出) する必要があります。 名前空間のサイズによっては、最新のサーバー変更がクラウドに同期されるまでに時間がかかることがあります。 ユーザーには影響がなく、引き続きサーバー上のデータを処理することができます。 この戦略により、ダウンタイムなしのクラウド移行が実現します。

同期のために構成する必要があるすべての Azure ファイル共有またはサーバーの場所について、同期グループを作成する手順と、一致するサーバー フォルダーをサーバー エンドポイントとして追加する手順を繰り返します。 Azure Data Box を使用して、お使いのファイルを複数の Azure ファイル共有に移動しました。 ご自身のオンプレミス データを、これらの Azure ファイル共有に接続するためのサーバー エンドポイントをすべて作成したら、移行は完了です。

## <a name="next-steps"></a>次のステップ

Azure ファイル共有と Azure File Sync については、さらに知るべきことがあります。以下の記事は、詳細なオプションおよびベスト プラクティスの理解に役立ちます。 また、トラブルシューティングに役立つ情報も提供します。 これらの記事には、必要に応じて [Azure ファイル共有のドキュメント](storage-files-introduction.md)へのリンクが含まれています。

* [移行の概要](storage-files-migration-overview.md)
* [Azure File Sync のデプロイの計画](../file-sync/file-sync-planning.md)
* [ファイル共有を作成する](storage-how-to-create-file-share.md)
* [Azure File Sync のトラブルシューティング](../file-sync/file-sync-troubleshoot.md)
