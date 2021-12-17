---
title: Azure ファイル共有を復元する
description: Azure portal を使用して、Azure Backup によって作成された復元ポイントからファイル共有全体または特定のファイルを復元する方法について説明します。
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 464c9927f901a373310ad6d1c0812a5a5de1eaaa
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846842"
---
# <a name="restore-azure-file-shares"></a>Azure ファイル共有を復元する

この記事では、Azure portal を使用して、[Azure Backup](./backup-overview.md) によって作成された復元ポイントからファイル共有全体または特定のファイルを復元する方法について説明します。

この記事では、次の方法について学習します。

* 完全な Azure ファイル共有を復元する。
* 個々のファイルまたはフォルダーを復元する。
* 復元操作の状態を追跡する。

## <a name="steps-to-perform-a-restore-operation"></a>復元操作を実行する手順

復元操作を実行するには、次の手順を実行します。

### <a name="select-the-file-share-to-restore"></a>復元するファイル共有を選択する

1. [Azure portal](https://portal.azure.com/) で、**バックアップ センター** に移動し、 **[復元]** をクリックします。

   :::image type="content" source="./media/restore-afs/backup-center-restore-inline.png" alt-text="Azure ファイル共有の復元プロセスの開始を示すスクリーンショット。" lightbox="./media/restore-afs/backup-center-restore-expanded.png":::

1. データソースの種類として **[Azure Files (Azure Storage)]** を選択し、復元するファイル共有を選択して、 **[続行]** をクリックします。

   :::image type="content" source="./media/restore-afs/azure-file-share-select-instance.png" alt-text="バックアップ項目の選択を示すスクリーンショット。":::

### <a name="full-share-recovery"></a>完全な共有の回復

この復元オプションを使用すると、元の場所または別の場所に完全なファイル共有を復元できます。

1. 前の手順で **[続行]** を選択した後、 **[復元]** ウィンドウが開きます。 復元操作を実行するために使用する復元ポイントを選択するには、 **[復元ポイント]** テキスト ボックスの下にある **[選択]** リンク テキストを選択し ます。

    ![[選択] を選択して復元ポイントを選択する](./media/restore-afs/select-restore-point.png)

1. 右側に **[復元ポイントの選択]** コンテキスト ペインが開き、選択したファイル共有で使用可能な復元ポイントが一覧表示されます。 復元操作を実行するために使用する復元ポイントを選択し、 **[OK]** を選択します。

   :::image type="content" source="./media/restore-afs/azure-file-share-select-restore-point-inline.png" alt-text="復元ポイントの選択を示すスクリーンショット。" lightbox="./media/restore-afs/azure-file-share-select-restore-point-expanded.png":::

    >[!NOTE]
    >既定では、 **[復元ポイントの選択]** ペインには、過去 30 日間の復元ポイントが一覧表示されます。 特定の期間に作成された復元ポイントを確認する場合は、適切な **[開始時刻]** と **[終了時刻]** を選択して範囲を指定し、 **[最新の情報に更新]** ボタンを選択します。

1. 次の手順では、 **[復元の場所]** を選択します。 **[回復先]** セクションで、データの復元先または復元方法を指定します。 トグル ボタンを使用して、次の 2 つのオプションのいずれかを選択します。

    * **元の場所**:完全なファイル共有を元のソースと同じ場所に復元します。
    * **別の場所**:完全なファイル共有を別の場所に復元し、元のファイル共有をそのままにします。

#### <a name="restore-to-the-original-location-full-share-recovery"></a>元の場所に復元する (完全な共有の復元)

1. **[回復先]** として **[元の場所]** を選択します。また、 **[In case of Conflicts]\(競合の場合\)** ドロップダウン リストから適切なオプションを選ぶことで、競合がある場合にスキップするか上書きするかを選択します。

1. **[復元]** を選択して復元操作を開始します。

   :::image type="content" source="./media/restore-afs/azure-file-share-original-location-recovery.png" alt-text="[復元] を選択して開始するのを示すスクリーンショット。":::

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>別の場所に復元する (完全な共有の復元)

1. **[回復先]** として **[別の場所]** を選択します。
1. **[ストレージ アカウント]** ドロップダウン リストから、バックアップした内容を復元する宛先ストレージ アカウントを選択します。
1. **[ファイル共有の選択]** ドロップダウン リストには、手順 2 で選択したストレージ アカウントに存在するファイル共有が表示されます。 バックアップされたコンテンツを復元するファイル共有を選択します。
1. **[フォルダー名]** ボックスに、復元されたコンテンツを含む、宛先ファイル共有に作成するフォルダー名を指定します。
1. 競合がある場合にスキップするか上書きするかを選択します。
1. すべてのボックスに適切な値を入力したら、 **[復元]** を選択して復元操作を開始します。

   :::image type="content" source="./media/restore-afs/azure-file-share-alternate-location-recovery.png" alt-text="[別の場所] の選択を示すスクリーンショット。":::

### <a name="item-level-recovery"></a>項目レベルの回復

この復元オプションを使用すると、個々のファイルまたはフォルダーを元の場所または別の場所に復元できます。

1. **バックアップ センター** に移動し、データソースの種類として **[Azure Storage (Azure Files)]** を選択して、メニューから **[バックアップ インスタンス]** を選択します。
1. 項目レベルの回復を実行するファイル共有を選択します。

   [バックアップ項目] メニューに **[ファイルの回復]** オプションが表示されます。

    ![[ファイルの回復] を選択する](./media/restore-afs/file-recovery.png)

1. **[ファイルの回復]** を選択すると、 **[復元]** ペインが開きます。 復元操作を実行するために使用する復元ポイントを選択するには、 **[復元ポイント]** テキスト ボックスの下にある **[選択]** リンク テキストを選択し ます。

    ![[選択] リンクを選択して復元ポイントを選択する](./media/restore-afs/select-restore-point.png)

1. 右側に **[復元ポイントの選択]** コンテキスト ペインが開き、選択したファイル共有で使用可能な復元ポイントが一覧表示されます。 復元操作を実行するために使用する復元ポイントを選択し、 **[OK]** を選択します。

    ![復元ポイントの選択](./media/restore-afs/restore-point.png)

1. 次の手順では、 **[復元の場所]** を選択します。 **[回復先]** セクションで、データの復元先または復元方法を指定します。 トグル ボタンを使用して、次の 2 つのオプションのいずれかを選択します。

    * **元の場所**:選択したファイルまたはフォルダーを元のソースと同じファイル共有に復元します。
    * **別の場所**:選択したファイルまたはフォルダーを別の場所に復元し、元のファイル共有のコンテンツをそのままにします。

#### <a name="restore-to-the-original-location-item-level-recovery"></a>元の場所に復元する (項目レベルの復元)

1. **[回復先]** として **[元の場所]** を選択します。また、 **[In case of conflicts]\(競合の場合\)** ドロップダウン リストから適切なオプションを選ぶことで、競合がある場合にスキップするか上書きするかを選択します。

    ![項目レベルの回復の元の場所](./media/restore-afs/original-location-item-level.png)

1. 復元するファイルまたはフォルダーを選択するために、 **[ファイルの追加]** ボタンを選択します。 これにより、右側にコンテキスト ペインが開き、復元用に選択したファイル共有の回復ポイントの内容が表示されます。

    ![[ファイルの追加] を選択する](./media/restore-afs/add-file.png)

1. 復元するファイルまたはフォルダーに対応するチェック ボックスをオンにし、 **[選択]** を選択します。

    ![ファイルまたはフォルダーを選択する](./media/restore-afs/select-file-folder.png)

1. 復元用に複数のファイルまたはフォルダーを選択するには、手順 2 から 4 を繰り返します。
1. 復元するすべての項目を選択したら、 **[復元]** を選択して復元操作を開始します。

    ![[復元] を選択して開始する](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>別の場所に復元する (項目レベルの復元)

1. **[回復先]** として **[別の場所]** を選択します。
1. **[ストレージ アカウント]** ドロップダウン リストから、バックアップした内容を復元する宛先ストレージ アカウントを選択します。
1. **[ファイル共有の選択]** ドロップダウン リストには、手順 2 で選択したストレージ アカウントに存在するファイル共有が表示されます。 バックアップされたコンテンツを復元するファイル共有を選択します。
1. **[フォルダー名]** ボックスに、復元されたコンテンツを含む、宛先ファイル共有に作成するフォルダー名を指定します。
1. 競合がある場合にスキップするか上書きするかを選択します。
1. 復元するファイルまたはフォルダーを選択するために、 **[ファイルの追加]** ボタンを選択します。 これにより、右側にコンテキスト ペインが開き、復元用に選択したファイル共有の回復ポイントの内容が表示されます。

    ![別の場所に復元するアイテムを選択する](./media/restore-afs/restore-to-alternate-location.png)

1. 復元するファイルまたはフォルダーに対応するチェック ボックスをオンにし、 **[選択]** を選択します。

    ![回復先を選択する](./media/restore-afs/recovery-destination.png)

1. 復元用の複数のファイルまたはフォルダーを選択するには、手順 6 から 8 を繰り返します。
1. 復元するすべての項目を選択したら、 **[復元]** を選択して復元操作を開始します。

    ![すべてのファイルを選択したら、[OK] を選択する](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>復元操作を追跡する

復元操作をトリガーすると、追跡用のジョブがバックアップ サービスによって作成されます。 Azure Backup は、ジョブに関する通知をポータルに表示します。 ジョブの操作を表示するには、通知のハイパーリンクを選択します。

![通知のハイパーリンクを選択する](./media/restore-afs/notifications-link.png)

また、Recovery Services コンテナーから復元の進行状況を監視することもできます。

1. **バックアップ センター** に移動し、メニューから **[バックアップ ジョブ]** をクリックします。
1. 必要なデータソースの種類とジョブの状態で、ジョブをフィルター処理します。

   :::image type="content" source="./media/restore-afs/backup-center-jobs-inline.png" alt-text="[バックアップ ジョブ] の選択を示すスクリーンショット。" lightbox="./media/restore-afs/backup-center-jobs-expanded.png":::

1. ファイル共有に対応するワークロード名を選択すると、**転送されたデータ** や **復元されたファイルの数** など、復元操作の詳細が表示されます。

    ![復元の詳細を表示する](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>次のステップ

* [Azure ファイル共有のバックアップを管理する](manage-afs-backup.md)方法について説明します。
