---
title: Data Box による Azure File Sync へのオンプレミス NAS 移行
description: Azure Data Box を介した Azure File Sync を使用して、オンプレミスのネットワーク接続ストレージ (NAS) の場所からハイブリッド クラウド デプロイにファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb7132e0a7b9dc59ac6b047d431acf0e740aba0a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430830"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>Data Box を使用して Azure File Sync によってネットワーク接続ストレージ (NAS) からハイブリッド クラウド デプロイに移行する

この移行に関する記事は、NFS、Azure File Sync、Azure Data Box というキーワードに当てはまるいくつかの記事の 1 つです。 この記事がご使用のシナリオに当てはまるかどうかを確認してください。

> [!div class="checklist"]
> * データ ソース: ネットワーク接続ストレージ (NAS)
> * 移行ルート: NAS &rArr; Data Box &rArr; Azure ファイル共有 &rArr; Windows Server と同期
> * オンプレミスでのファイルのキャッシュ: はい、最終的な目標は Azure File Sync のデプロイです

自分のシナリオが異なる場合は、[移行ガイドの表](storage-files-migration-overview.md#migration-guides)を参照してください。

Azure File Sync は、ダイレクト接続ストレージ (DAS) の場所で機能します。 ネットワーク接続ストレージ (NAS) の場所への同期はサポートされていません。
そのため、ファイルを移行する必要があります。 この記事では、その移行の計画と実装について説明します。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>移行の目標

目標は、NAS アプライアンス上にある共有を Windows Server に移行することです。 その後、ハイブリッド クラウド デプロイに Azure File Sync を使用します。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

移行プロセスは、いくつかのフェーズで構成されます。 次のことを行う必要があります。
- Azure ストレージ アカウントとファイル共有をデプロイします。
- Windows Server を実行しているオンプレミス コンピューターをデプロイします。 
- Azure File Sync を構成します。
- Robocopy を使用してファイルを移行します。
- カットオーバーを実行します。

以下のセクションでは、移行プロセスの各フェーズについて詳しく説明します。

> [!TIP]
> この記事に戻った場合は、画面の右側にあるナビゲーションを使用して、中断した移行フェーズにジャンプします。

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>フェーズ 1: 必要な Azure ファイル共有の数を決定する

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

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>フェーズ 4: オンプレミスに適切な Windows Server インスタンスをプロビジョニングする

Azure Data Box デバイスが届くのを待っている間に、Azure File Sync で使用する 1 つ以上の Windows Server インスタンスのニーズに関する確認を始めることができます。

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも Windows Server 2012 R2) のインスタンスを作成します。 また、Windows Server フェールオーバー クラスターもサポートされています。
* ダイレクト接続ストレージをプロビジョニングまたは追加します。 (サポートされていない NAS ではなく、DAS です)。

デプロイする Windows Server インスタンスのリソース構成 (コンピューティングと RAM) は、主に同期するファイルとフォルダーの数によって変わります。 ご心配な場合は、より高いパフォーマンス構成を使用することをお勧めします。

[同期する必要がある項目の数に基づいて Windows Server インスタンスをサイズ指定する方法を参照してください。](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 前記のリンク先の記事には、サーバー メモリ (RAM) の範囲に関する表が含まれています。 対象のサーバーに対する範囲の下限の数字を使用できますが、初期同期にはかなり長い時間がかかることが予想されます。

## <a name="phase-5-copy-files-onto-your-data-box"></a>フェーズ 5: ファイルを Data Box にコピーする

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


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>フェーズ 6: Azure File Sync クラウド リソースをデプロイする

このガイドを続行する前に、すべてのファイルが適切な Azure ファイル共有に到達するまで待ちます。 Data Box データの発送と取り込みの処理には時間がかかります。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>フェーズ 7: Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>フェーズ 8: Windows Server インスタンスで Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows Server インスタンスを準備して、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

クラウドを使った階層化機能を有効にし、最初のダウンロード セクションで **[Namespace only]\(名前空間のみ\)** を選択します。

> [!IMPORTANT]
> クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようにする Azure File Sync の機能です。 ローカル環境で関心のあるデータも、アクセスのパフォーマンスを上げるためにローカルにキャッシュされます。 クラウドを使った階層化は省略可能です。 これは Azure File Sync サーバー エンドポイントごとに個別に設定できます。 この機能を使用する必要があるのは、すべてのクラウド データを保持するのに十分なローカル ディスク容量が Windows Server インスタンスになく、クラウドからすべてのデータをダウンロードすることは避けたい場合です。

同期のために構成する必要があるすべての Azure ファイル共有またはサーバーの場所について、同期グループを作成する手順と、一致するサーバー フォルダーをサーバー エンドポイントとして追加する手順を繰り返します。 名前空間の同期が完了するまで待ちます。 次のセクションでは、同期を確実に完了する方法について説明します。

> [!NOTE]
> サーバー エンドポイントの作成後、同期は機能しています。 ただし、同期では Data Box を介して Azure ファイル共有に移動したファイルとフォルダーを列挙 (検出) する必要があります。 名前空間のサイズによっては、クラウドの名前空間がサーバーに表示されるまでにかなりの時間がかかることがあります。

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>フェーズ 9: 名前空間がサーバーに完全に表示されるまで待機する

移行の次の手順に進む前に、サーバーにクラウド共有から名前空間が完全にダウンロードされるまで待ちます。 サーバーへのファイルの移動の開始が早すぎると、不要なアップロードや、さらにはファイル同期の競合が発生するリスクがあります。

サーバーで初回ダウンロード同期が完了したかどうかを確認するには、同期している Windows Server インスタンスでイベント ビューアーを開き、Azure File Sync テレメトリ イベント ログを使用します。
テレメトリ イベント ログは、イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にあります。

最新の 9102 イベントを検索します。 同期セッションが完了すると、イベント ID 9102 がログされます。 イベント テキストには、ダウンロード同期の方向のフィールドがあります。 (`HResult` は 0 にする必要があり、ファイルをダウンロードする必要があります。)
 
サーバーで名前空間のダウンロードが完了したことを確認するには、このコンテンツを含む、この種類の 2 つの連続するイベントを確認する必要があります。 2 つの 9102 イベントの間に他のイベントがあっても問題ありません。

## <a name="phase-10-run-robocopy-from-your-nas"></a>フェーズ 10: NAS から Robocopy を実行する

ご使用のサーバーで、クラウド共有からの名前空間全体の初期同期が完了したら、この手順を続行できます。 この手順を続行する前に、初期同期が完了している必要があります。 詳細については、前のセクションを参照してください。

この手順では、Robocopy ジョブを実行して、クラウド共有を、共有を Data Box にフォークしてから発生した NAS 上の最新の変更と同期します。
この Robocopy の実行は、NAS 共有で発生したチャーンの量によって、短時間で終了することも、時間がかかることもあります。

> [!WARNING]
> Windows Server 2019 で低下した Robocopy の動作のため、Robocopy `/MIR` スイッチは階層化されたターゲット ディレクトリに対応していません。 移行のこのフェーズでは、Windows Server 2019 または Windows 10 クライアントは使用できません。 中間の Windows Server 2016 インスタンスで、Robocopy を使用します。

基本的な移行方法を次に示します。
 - NAS アプライアンスから Robocopy を実行して、Windows Server インスタンスを同期します。 
 - Azure File Sync を使用して、Windows Server から Azure ファイル共有を同期します。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

1. NAS アプライアンス上で最初の場所を特定します。
1. Windows Server インスタンス上で、既に Azure File Sync が構成されている対応するフォルダーを特定します。
1. Robocopy を使用してコピーを開始します。

次の Robocopy コマンドを実行すると、NAS ストレージから Windows Server のターゲット フォルダーに差分 (更新されたファイルおよびフォルダー) のみがコピーされます。 その後、Windows Server インスタンスによってそれらが Azure ファイル共有に同期されます。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

NAS アプライアンスでファイルが使用する量より少ないストレージを Windows Server にプロビジョニングした場合は、クラウドを使った階層化を構成しています。 ローカル環境の Windows Server ボリュームがいっぱいになると、[クラウドを使った階層化](../file-sync/file-sync-cloud-tiering-overview.md)により、既に正常に同期されているファイルの階層化が開始されます。 クラウドを使った階層化により、NAS アプライアンスからのコピーを続けるのに十分な領域が生成されます。 クラウドを使った階層化では、1 時間に 1 回、同期されたものが確認されて、ボリューム空き領域 99% になるようにディスク領域が解放されます。

Robocopy を使用すると、Windows Server インスタンスでローカルに格納できるよりも多くのファイルを移動しなければならない場合があります。 Robocopy を使用した方が、Azure File Sync でファイルをアップロードしてローカル ボリューム外に階層化するよりも速くファイルを移動できます。 この状況では、Robocopy は失敗します。 このシナリオを回避する順序で共有を処理することをお勧めします。 たとえば、Windows Server インスタンスで使用可能な空き領域に収まる共有のみを移動します。 または、すべての共有に対して Robocopy ジョブを同時に開始しないようにします。 良い点は、`/MIR` スイッチを使用することで、差分だけを確実に移動できることです。 差分を移動した後、再開されたジョブでファイルを再度移動する必要はありません。

### <a name="do-the-cutover"></a>カットオーバーを実行する

Robocopy コマンドを初めて実行するときは、ユーザーとアプリケーションが引き続き NAS 上のファイルにアクセスしていて、それを変更する可能性があります。 Robocopy では 1 つのディレクトリを処理してから、次のディレクトリに移動します。 その後、NAS のユーザーが、現在の Robocopy の実行中に処理されない最初のディレクトリでファイルの追加、変更、または削除を行う可能性があります。 これは正しい動作です。

最初の実行では、チャーンされたデータの大部分が Windows Server インスタンスに移動され、Azure File Sync 経由でクラウドに移動されます。この最初のコピーには、次の条件によっては時間がかかることがあります。

* アップロードの帯域幅。
* ローカル ネットワークの速度と、それに合った最適な Robocopy スレッドの数。
* Robocopy と Azure File Sync によって処理される必要がある項目 (ファイルとフォルダー) の数。

最初の実行が完了した後、再度コマンドを実行します。

共有に対して 2 回目に Robocopy を実行すると、より速く完了します。 これは、前回の実行以降に発生した変更だけを転送する必要があるためです。 同じ共有に対してジョブを繰り返し実行できます。

ダウンタイムを許容できる場合は、お使いの NAS ベースの共有にユーザーがアクセスできないようにする必要があります。 これは、ユーザーがファイルとフォルダー構造およびコンテンツを変更できないようにする任意の方法で行えます。 たとえば、DFS 名前空間を存在しない場所にポイントしたり、共有のルート ACL を変更したりできます。

Robocopy を最後に 1 回実行します。 それにより、見過ごされたすべての変更が取得されます。
この最後の手順にかかる時間は、Robocopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 NAS SMB 共有と同じ共有レベルのアクセス許可を設定してください。 エンタープライズ クラスのドメイン参加済みの NAS がある場合、ユーザーは Active Directory にいて、Robocopy によってファイルとメタデータが完全な忠実性でコピーされるため、ユーザー SID は自動的に一致します。 NAS でローカル ユーザーを使用している場合、次のことを行う必要があります。 
- これらのユーザーを Windows Server ローカル ユーザーとして再作成します。 
- Robocopy によって Windows Server インスタンスに移動された既存の SID を、新しい Windows Server ローカル ユーザーの SID にマップします。

これで、共通のルートまたはボリューム (フェーズ 1 からのマッピングによって異なります) への共有または共有のグループの移行は完了しました。

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

## <a name="deprecated-option-offline-data-transfer"></a>非推奨のオプション: "オフライン データ転送"

Azure File Sync エージェント バージョン 13 がリリースされる前、Data Box 統合は "オフライン データ転送" と呼ばれるプロセスを通して実現されました。 このプロセスは非推奨です。 これは、エージェント バージョン 13 で、この記事で説明されているはるかに簡単かつ高速な手順に置き換えられました。 非推奨の "オフライン データ転送" 機能を使用する必要があるとわかっている場合は、引き続きそうすることができます。 これは、具体的には、[以前の AFS PowerShell モジュール](https://www.powershellgallery.com/packages/Az.StorageSync/1.4.0)を使用することによって引き続き可能です。

```powershell
Install-Module Az.StorageSync -RequiredVersion 1.4.0
Import-module Az.StorageSync -RequiredVersion 1.4.0
# Verify the specific version is loaded:
Get-module Az.StorageSync
```
その後、引き続き、同じ PowerShell モジュールを使用してサーバー エンドポイントを作成し、そのプロセスでステージング共有を指定できます。
オフライン データ転送プロセスで進行中の移行がある場合、その移行は計画どおりに続行されるため、移行が完了したら、引き続きこの設定を無効にする必要があります。
この非推奨のプロセスで新しい移行を開始する機能は、今後予定されているエージェント リリースで削除される予定です。

## <a name="troubleshooting"></a>トラブルシューティング

最も一般的な問題は、Windows Server 側で "ボリュームがいっぱい" になったために Robocopy コマンドが失敗することです。 クラウドを使った階層化は 1 時間ごとに動作し、同期されたローカルの Windows Server ディスクからコンテンツが退避されます。 目標は、ボリュームの空き領域を 99% にすることです。

同期を進行させ、クラウドを使った階層化にディスク領域を解放させます。 これは、Windows Server インスタンスのエクスプローラーで確認できます。

Windows Server インスタンスに空き容量が十分にある場合は、コマンドを再度実行すると問題が解決されます。 この状況で、壊れるものは何もありません。 自信を持って前進できます。 コマンドを再実行する不便さだけが、唯一の影響です。

Azure File Sync の問題のトラブルシューティングを行うには、次のセクションに記載されている記事をご覧ください。

## <a name="next-steps"></a>次のステップ

Azure ファイル共有と Azure File Sync については、さらに知るべきことがあります。以下の記事は、詳細なオプションおよびベスト プラクティスの理解に役立ちます。 また、トラブルシューティングに役立つ情報も提供します。 これらの記事には、必要に応じて [Azure ファイル共有のドキュメント](storage-files-introduction.md)へのリンクが含まれています。

* [移行の概要](storage-files-migration-overview.md)
* [Azure File Sync のデプロイの計画](../file-sync/file-sync-planning.md)
* [ファイル共有を作成する](storage-how-to-create-file-share.md)
* [Azure File Sync のトラブルシューティング](../file-sync/file-sync-troubleshoot.md)