---
title: Azure File Sync へのオフラインでの取り込みには、Data Box などの方法を使用してください。
description: 同期に対応した一括移行サポートを有効にするためのプロセスとベスト プラクティス。
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212668"
---
# <a name="migrate-to-azure-file-sync"></a>Azure File Sync への移行
Azure File Sync に移行する方法はいくつかあります。

### <a name="uploading-files-via-azure-file-sync"></a>Azure File Sync を介したファイルのアップロード
最も簡単な方法は、ファイルをローカルで Windows Server 2012 R2 以降に移動し、Azure File Sync エージェントをインストールすることです。 同期が構成されると、サーバーからファイルがアップロードされます。 現在、すべてのお客様の平均アップロード速度は、約 2 日ごとに 1 TB です。
ご使用のサーバーがデータ センターで他のサーバーの妨げとならないように、[帯域幅調整スケジュール](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)を検討してください。

### <a name="offline-bulk-transfer"></a>オフラインの一括転送
確かに、アップロードは最も簡単な方法ですが、利用可能な帯域幅で合理的な時間内にファイルを Azure に同期できない場合は、有用ではないことがあります。 ここで克服しなければならない課題は、ファイル セット全体の初期同期です。 その後、Azure File Sync は名前空間で行われた変更のみを移動します。通常、この移動には、はるかに少ない帯域幅が使用されます。
この初期アップロードの課題を克服するために、Azure [Data Box ファミリ](https://azure.microsoft.com/services/storage/databox)などのオフライン一括移行ツールを使用できます。 次の記事では、Azure File Sync と互換性のある方法でオフライン移行を活用するために実行する必要があるプロセスを中心に説明します。 同期を有効にした場合に、競合ファイルを回避し、ファイルとフォルダーの ACL とタイムスタンプを保持するのに役立つベストプラクティスについて説明します。

### <a name="online-migration-tools"></a>オンライン移行ツール
以下に概説されているプロセスは、Data Box にのみ対応しているわけではありません。 すべてのオフライン移行ツール (Data Box など) やオンライン ツール (AzCopy、Robocopy など)、またはサード パーティ製のツールやサービスに対応しています。 そのため、最初のアップロードの課題を克服する方法に関係なく、同期に対応した方法でこれらのツールを利用するには、以下に概説されている手順を実行することが引き続き重要になります。


## <a name="offline-data-transfer-benefits"></a>オフライン データ転送の利点
Data Box を使用する場合のオフライン移行の主な利点は次のとおりです。

- Data Box などのオフライン一括転送プロセスを使用してファイルを Azure に移行する場合、ネットワーク経由でサーバーからすべてのファイルをアップロードする必要はありません。 大規模な名前空間の場合、これはネットワークの帯域幅と時間を大幅に節約できることを意味します。
- Azure File Sync を使用すると、使用している転送モード (Data Box、Azure Import など) に関係なく、ライブ サーバーは、ユーザーが Azure にデータを送信した後に変更があったファイルのみをアップロードします。
- Azure File Sync を使用すると、オフラインの一括移行製品によって ACL が転送されない場合でも、ファイルとフォルダーの ACL も確実に同期されます。
- Azure Data Box と Azure File Sync を使用すると、ダウンタイムはゼロになります。 Data Box を使用して Azure にデータを転送すると、ファイルの忠実性を維持しながらネットワーク帯域幅を効率的に使用できます。 また、Data Box が送信されてから変更されたファイルのみをアップロードすることによって、名前空間を最新の状態に保ちます。

## <a name="plan-your-offline-data-transfer"></a>オフライン データ転送の計画
開始する前に、次の情報を確認してください。

- Azure File Sync との同期を有効にする前に、1 つまたは複数の Azure ファイル共有への一括移行を完了してください。
- 一括移行に Data Box を使用する予定の場合は、[Data Box のデプロイの前提条件](../../databox/data-box-deploy-ordered.md#prerequisites)に関する説明を確認してください。
- 最終的な Azure File Sync トポロジの計画:[Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
- 同期するファイル共有を保持する Azure ストレージ アカウントを選択します。 一括移行が、同じストレージ アカウント内の一時的なステージング共有に対して行われるようにしてください。 一括移行は、同じストレージ アカウントに存在する最終およびステージングの共有を使用してのみ有効にできます。
- 一括移行は、サーバーの場所との新しい同期関係を作成するときにのみ使用できます。 既存の同期関係で一括移行を有効にすることはできません。

## <a name="offline-data-transfer-process"></a>オフライン データ転送プロセス
このセクションでは、Azure Data Box などの一括移行ツールと互換性のある方法で Azure File Sync を設定するプロセスについて説明します。

![次の段落でも詳細に説明されているプロセスの手順の視覚化](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 手順 | 詳細 |
|---|---------------------------------------------------------------------------------------|
| ![プロセスの手順 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [お客様の Data Box を注文します](../../databox/data-box-deploy-ordered.md)。 お客様のニーズに対応するため、[Data Box ファミリにはいくつかのオファリングが用意されています](https://azure.microsoft.com/services/storage/databox/data)。 Data Box を受け取り、Data Box の[ドキュメントに従ってデータをコピー](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)します。 データが Data Box のこの UNC パス `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` にコピーされていることを確認します。ここで `ShareName` は、ステージング共有の名前です。 Data Box を Azure に送り返します。 |
| ![プロセスの手順 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 一時的なステージング共有として指定した Azure ファイル共有にファイルが表示されるまで待ちます。 **それらの共有への同期を有効にしないでください。** |
| ![プロセスの手順 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Data Box によって作成されたファイル共有ごとに、空の新しい共有を作成します。 この新しい共有が Data Box 共有と同じストレージ アカウントにあることを確認してください。 [新しい Azure ファイル共有を作成する方法](storage-how-to-create-file-share.md)。 |
| ![プロセスの手順 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | ストレージ同期サービスで[同期グループを作成](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)し、空の共有をクラウド エンドポイントとして参照します。 Data Box のファイル共有ごとにこの手順を繰り返します。 「[Azure File Sync のデプロイ](storage-sync-files-deployment-guide.md)」ガイドを確認して、Azure File Sync の設定に必要な手順に従ってください。 |
| ![プロセスの手順 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [ライブ サーバー ディレクトリをサーバー エンドポイントとして追加](storage-sync-files-deployment-guide.md#create-a-server-endpoint)します。 Azure へのファイルの移動が既に完了していることをこのプロセスで指定し、ステージング共有を参照します。 必要に応じて、クラウドの階層化を有効または無効にできます。 ライブ サーバー上でサーバー エンドポイントを作成している間は、ステージング共有を参照する必要があります。 新しいサーバー エンドポイント ブレードで [オフライン データ転送] (下の画像) を有効にし、クラウド エンドポイントと同じストレージ アカウントに存在する必要があるステージング共有を参照します。 利用可能な共有の一覧は、まだ同期されていないストレージ アカウントと共有によってフィルター処理されます。 |

![新しいサーバー エンドポイントを作成するときにオフライン データ転送を有効にするための Azure portal ユーザー インターフェイスを視覚化しています。](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>共有の同期
サーバー エンドポイントを作成すると、同期が開始されます。 同期では、サーバー上に存在する各ファイルについて、対象のファイルが、Data Box がファイルを配置したステージング共有にも存在するかどうかが判断されます。存在する場合、サーバーからファイルがアップロードされるのではなく、ステージング共有からファイルがコピーされます。 ファイルがステージング共有に存在しない場合、またはローカル サーバーで新しいバージョンが利用可能な場合は、同期によってローカル サーバーからファイルがアップロードされます。

> [!IMPORTANT]
> 一括移行モードは、サーバー エンドポイントの作成中にのみ有効にできます。 サーバー エンドポイントが確立されると、現時点では、既に同期されているサーバーから名前空間に一括移行済みデータを統合する方法はありません。

## <a name="acls-and-timestamps-on-files-and-folders"></a>ファイルとフォルダーの ACL とタイムスタンプ
Azure File Sync は、使用された一括移行ツールが最初に ACL を転送しなかった場合でも、ファイルおよびフォルダーの ACL がライブサーバーから確実に同期されるようにします。 つまり、ステージング共有にファイルやフォルダーの ACL が含まれていなくても問題ありません。 新しいサーバー エンドポイントを作成するときにオフライン データ移行機能を有効にすると、その時点でサーバー上のすべてのファイルに対して ACL が同期されます。 作成および変更されたタイムスタンプについても同様です。

## <a name="shape-of-the-namespace"></a>名前空間の形状
名前空間の形状は、同期が有効になっているときにサーバー上に何が存在するかによって決まります。 Data Box の "スナップショット" と移行の後にファイルがローカル サーバー上で削除された場合、それらのファイルがライブの同期名前空間に送信されることはありません。 それらのファイルはまだステージング共有にありますが、コピーされることはありません。 同期でライブ サーバーに応じて名前空間が維持されるため、これは望ましい動作です。 Data Box の "スナップショット" は、効率的なファイル コピーのための単なるステージング グラウンドであり、ライブ名前空間の形状に対する権限ではありません。

## <a name="finishing-bulk-migration-and-clean-up"></a>一括移行の終了とクリーンアップ
以下のスクリーン ショットは、Azure portal のサーバー エンドポイント プロパティのブレードを示しています。 オフラインの [データ転送] セクションで、プロセスの状態を確認できます。 [進行中] または [完了] と表示されます。

サーバーが名前空間全体の初期同期を完了すると、サーバーでのステージング ファイル共有と Data Box の一括移行ファイルの使用が完了します。 オフライン データ転送のサーバー エンドポイントのプロパティで、状態が [完了] に変更されていることを確認します。 この時点で、次のようにしてステージング共有をクリーンアップしてコストを節約できます。

1. 状態が [完了] のときに、サーバー エンドポイントのプロパティで [Disable offline data transfer]\(オフライン データ転送を無効にする\) をクリックします。
2. コストを節約するためにステージング共有を削除することを検討します。 ステージング共有にファイルとフォルダーの ACL が含まれていることはほとんどないため、ステージング共有の用途は限られています。 代わりに、バックアップの「特定の時点」の目的で、[同期する Azure ファイル共有の実際のスナップショット](storage-snapshots-files.md)を作成します。 スケジュールに基づいた [Azure Backup によるスナップショット作成を有効にする]( ../../backup/backup-azure-files.md)ことができます。

![オフライン データ転送の状態および無効化コントロールが配置されているサーバー エンドポイント プロパティの Azure portal ユーザー インターフェイスを視覚化しています。](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

このモードを無効にするのは、状態が [完了] の場合、または構成の誤りが原因で本当に中止したい場合だけにしてください。 正規のデプロイの途中でこのモードを無効にすると、ステージング共有がまだ利用可能であっても、ファイルはサーバーからアップロードされ始めます。

> [!IMPORTANT]
> オフライン データ転送を無効にした後は、一括移行からのステージング共有がまだ利用可能であっても、オフライン データ転送を再度有効にする方法はありません。

## <a name="next-steps"></a>次の手順
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
