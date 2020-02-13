---
title: Azure portal 内での Azure ファイル共有のバックアップ
description: Recovery Services コンテナー内のバックアップされた Azure ファイル共有を、Azure portal を使用してバックアップする方法について説明します
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938149"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Recovery Services コンテナーに Azure ファイル共有をバックアップする

この記事では、Azure portal を使用して [Azure ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

* Recovery Services コンテナーを作成する。
* ファイル共有を検出してバックアップを構成します。
* オンデマンド バックアップ ジョブを実行して復元ポイントを作成します。

## <a name="prerequisites"></a>前提条件

* ファイル共有をホストするストレージ アカウントと同じリージョンの [Recovery Services コンテナー](#create-a-recovery-services-vault)を特定または作成する。
* [サポートされているストレージ アカウントの種類](#limitations-for-azure-file-share-backup-during-preview)のいずれかにファイル共有が存在することを確認しておいてください。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>プレビュー期間における Azure ファイル共有のバックアップの制限

Azure ファイル共有のバックアップはプレビュー段階です。 汎用 v1 ストレージ アカウントと汎用 v2 ストレージ アカウント、どちらの Azure ファイル共有もサポートされています。 Azure ファイル共有のバックアップには、次の制限があります。

* 現在、[ゾーン冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) レプリケーションを使用したストレージ アカウントでの Azure ファイル共有のバックアップのサポートは、[これらのリージョン](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)に限定されています。
* Azure Backup では、現在、Azure ファイル共有の 1 日 1 回のスケジュール済みバックアップの構成をサポートしています。
* スケジュール バックアップの数は、1 日につき 1 個が上限となります。
* オンデマンド バックアップの数は、1 日につき 4 個が上限となります。
* ストレージ アカウントに対する[リソース ロック](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)を使用して、Recovery Services コンテナー内のバックアップを誤って削除しないようにします。
* Azure Backup によって作成されたスナップショットを削除しないでください。 スナップショットを削除すると、復旧ポイントが失われたり、復元が失敗したりする場合があります。
* Azure Backup によって保護されているファイル共有は削除しないでください。 現在のソリューションでは、ファイル共有が削除されると Azure Backup によって取得されたスナップショットがすべて削除されるため、すべての復元ポイントが失われます。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>ストレージ レプリケーションを変更する

既定では、コンテナーには [geo 冗長ストレージ (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) が使用されます。

* コンテナーをプライマリ バックアップ メカニズムとする場合は、GRS を使用することをお勧めします。
* 低コストのオプションとして[ローカル冗長ストレージ (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) を使用できます。

ストレージ レプリケーションの種類を変更にするには:

1. 新しいコンテナーで、 **[設定]** セクションから **[プロパティ]** を選択します。

1. **[プロパティ]** ページで、 **[バックアップ構成]** から **[更新]** をクリックします。

1. ストレージのレプリケーションの種類を選択し、 **[保存]** を選択します。

    ![バックアップ構成の更新](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> コンテナーを設定してバックアップ項目を格納した後で、ストレージ レプリケーションの種類を変更することはできません。 これを行う場合は、コンテナーを再作成する必要があります。
>

## <a name="discover-file-shares-and-configure-backup"></a>ファイル共有を検出してバックアップを構成する

1. [Azure portal](https://portal.azure.com/) で、ファイル共有のバックアップに使用する Recovery Services コンテナーを開きます。

1. **[Recovery Services コンテナー]** ダッシュボードで **[+ バックアップ]** を選択します。

   ![Recovery Services コンテナー](./media/backup-afs/recovery-services-vault.png)

    a. **[バックアップの目標]** で、 **[ワークロードはどこで実行されていますか?]** を **[Azure]** に設定します。

    ![バックアップの目標として Azure ファイル共有を選択する](./media/backup-afs/backup-goal.png)

    b.  **[何をバックアップしますか]** を選択し、ドロップダウン リストから **[Azure ファイル共有]** を選択します。

    c.  **[バックアップ]** を選択して、Azure ファイル共有拡張機能をコンテナーに登録します。

    ![[バックアップ] を選択して Azure ファイル共有をコンテナーに関連付ける](./media/backup-afs/register-extension.png)

1. **[バックアップ]** を選択すると、 **[バックアップ]** ペインが開き、検出されたサポートされるストレージ アカウントの一覧からストレージ アカウントを選択するよう求められます。 これらは、このコンテナーに関連付けられている、またはコンテナーと同じリージョンに存在しているがまだ Recovery Services コンテナーに関連付けられていないストレージ アカウントです。

   ![ストレージ アカウントを選択する](./media/backup-afs/select-storage-account.png)

1. 検出されたストレージ アカウントの一覧からアカウントを選択し、 **[OK]** を選択します。 Azure で、バックアップ可能なファイル共有のストレージ アカウントが検索されます。 最近ファイル共有を追加したが、一覧に表示されない場合は、ファイル共有が表示されるまで少し時間を置いてください。

    ![ファイル共有の検出](./media/backup-afs/discovering-file-shares.png)

1. **[ファイル共有]** の一覧から、バックアップするファイル共有を 1 つ以上選択します。 **[OK]** を選択します。

1. ファイル共有を選択すると、 **[バックアップ]** メニューが **[バックアップ ポリシー]** に切り替わります。 このメニューから、既存のバックアップ ポリシーを選択するか新しいバックアップ ポリシーを作成します。 次に、 **[バックアップの有効化]** を選択します。

    ![[バックアップ ポリシー] を選択する](./media/backup-afs/select-backup-policy.png)

バックアップ ポリシーを設定すると、スケジュールされた時刻にファイル共有のスナップショットが作成されます。 復旧ポイントも、選択した期間にわたって保持されます。

## <a name="create-an-on-demand-backup"></a>オンデマンド バックアップの作成

場合によっては、バックアップ ポリシーでスケジュールされた以外の時間に、バックアップ スナップショットまたは復旧ポイントを生成することができます。 一般的に、オンデマンド バックアップを生成するのは、バックアップ ポリシーを構成した直後です。 バックアップ ポリシーのスケジュールに基づいて、スナップショットが作成されるまで数時間または数日かかることがあります。 バックアップ ポリシーが適用されるまでデータを保護するために、オンデマンド バックアップを開始します。 多くの場合は、ファイル共有への計画的な変更を行う前にオンデマンド バックアップの作成が必要です。

### <a name="create-a-backup-job-on-demand"></a>バックアップ ジョブをオンデマンドで作成する

1. ファイル共有をバックアップするために使用した Recovery Services コンテナーを開きます。 **[概要]** ペインで、 **[保護された項目]** セクションから **[バックアップ項目]** を選択します。

   ![バックアップ項目を選択する](./media/backup-afs/backup-items.png)

1. **[バックアップ項目]** を選択すると、すべての**バックアップ管理の種類**を一覧表示する新しいペインが **[概要]** ペインの隣に開きます。

   ![バックアップ管理の種類一覧](./media/backup-afs/backup-management-types.png)

1. **[バックアップの管理の種類]** の一覧から、 **[Azure Storage (Azure Files)]** を選択します。 このコンテナーを使用してバックアップされたすべてのファイル共有とそれに対応するストレージ アカウントの一覧が表示されます。

   ![Azure Storage (Azure Files) のバックアップ項目](./media/backup-afs/azure-files-backup-items.png)

1. Azure ファイル共有の一覧から、目的のファイル共有を選択します。 **バックアップ項目**の詳細が表示されます。 **[バックアップ項目]** メニューの **[今すぐバックアップ]** を選択します。 このバックアップ ジョブはオンデマンドであるため、復旧ポイントに関連付けられた保持ポリシーはありません。

   ![[今すぐバックアップ] を選択する](./media/backup-afs/backup-now.png)

1. **[今すぐバックアップ]** ペインが開きます。 復旧ポイントの保持期限を指定します。 オンデマンド バックアップでは、最大保有期間を 10 年に指定できます。

   ![保持期間を選択します](./media/backup-afs/retention-date.png)

1. **[OK]** を選択して、実行するオンデマンド バックアップ ジョブを確定します。

1. ポータルの通知を監視して、バックアップ ジョブの実行完了を把握します。 コンテナー ダッシュボードでジョブの進行状況を監視できます。 **[バックアップ ジョブ]**  >  **[進行中]** を選択します。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [Azure ファイル共有を復元する](restore-afs.md)
* [Azure ファイル共有のバックアップを管理する](manage-afs-backup.md)
