---
title: Azure portal 内での Azure ファイル共有のバックアップ
description: Recovery Services コンテナー内のバックアップされた Azure ファイル共有を、Azure portal を使用してバックアップする方法について説明します
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 46068722385e9cf89c5c85d37a72a0528479ab8a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849732"
---
# <a name="back-up-azure-file-shares"></a>Azure ファイル共有のバックアップ

この記事では、Azure portal から [Azure ファイル共有](../storage/files/storage-files-introduction.md)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

* Recovery Services コンテナーを作成する。
* Recovery Services コンテナーからバックアップを構成する
* ファイル共有ペインからバックアップを構成する
* オンデマンド バックアップ ジョブを実行して復元ポイントを作成する

## <a name="prerequisites"></a>前提条件

* Azure ファイル共有スナップショットベースのバックアップ ソリューションについて[学習](azure-file-share-backup-overview.md)する。
* [サポートされているストレージ アカウントの種類](azure-file-share-support-matrix.md)のいずれかにファイル共有が存在することを確認しておいてください。
* ファイル共有をホストするストレージ アカウントと同じリージョンおよびサブスクリプションの [Recovery Services コンテナー](#create-a-recovery-services-vault)を特定または作成する。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Recovery Services コンテナーからバックアップを構成する

[Recovery Services コンテナー] ペインから複数のファイル共有のバックアップを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) で、 **[バックアップ センター]** に移動し、 **[バックアップ]** をクリックします。

   :::image type="content" source="./media/backup-afs/backup-center-configure-inline.png" alt-text="Azure ファイルのバックアップを構成するためのスクリーンショット。" lightbox="./media/backup-afs/backup-center-configure-expanded.png":::

1. データソースの種類として **[Azure Files (Azure Storage)]** を選択し、ファイル共有の保護に使用するコンテナーを選択して、 **[続ける]** をクリックします。

   :::image type="content" source="./media/backup-afs/azure-file-share-select-vault.png" alt-text="Azure Files の選択を示すスクリーンショット。":::

1. **[選択]** をクリックして、バックアップするファイル共有を保存するストレージ アカウントを選択します。

   **[ストレージ アカウントの選択] ウィンドウ** が右側に開き、検出されたサポート対象のストレージ アカウントが一覧表示されます。 これらは、このコンテナーに関連付けられている、またはコンテナーと同じリージョンに存在しているがまだ Recovery Services コンテナーに関連付けられていないストレージ アカウントです。

   :::image type="content" source="./media/backup-afs/azure-file-share-select-storage-account-inline.png" alt-text="ストレージ アカウントの選択を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-select-storage-account-expanded.png":::

1. 検出されたストレージ アカウントの一覧からアカウントを選択し、 **[OK]** を選択します。

   :::image type="content" source="./media/backup-afs/azure-file-share-confirm-storage-account-inline.png" alt-text="検出されたストレージ アカウントのうち 1 つの選択を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-confirm-storage-account-expanded.png":::
   
   >[!NOTE]
   >ストレージ アカウントがコンテナーとは異なるリージョンに存在する場合は、検出されたストレージ アカウントの一覧に表示されません。

1. 次の手順で、バックアップするファイル共有を選択します。 **[FileShares to Backup]\(バックアップするファイル共有)** セクションにある **[追加]** ボタンを選択します。

   :::image type="content" source="./media/backup-afs/azure-select-file-share-inline.png" alt-text="バックアップするファイル共有の選択を示すスクリーンショット。" lightbox="./media/backup-afs/azure-select-file-share-expanded.png":::

1. 右側に **[ファイル共有の選択]** コンテキスト ペインが開きます。 Azure で、バックアップ可能なファイル共有のストレージ アカウントが検索されます。 最近ファイル共有を追加したが、一覧に表示されない場合は、ファイル共有が表示されるまで少し時間を置いてください。

1. **[ファイル共有の選択]** の一覧から、バックアップするファイル共有を選択します。 **[OK]** を選択します。

1. ファイル共有のバックアップ ポリシーを選択するには、次の 3 つのオプションがあります。

   * 既定のポリシーを選択します。<br>
   このオプションを使用すると、毎日のバックアップを 30 日間保有できます。 コンテナーに既存のバックアップ ポリシーがない場合は、既定のポリシー設定で [バックアップ] ウィンドウが開きます。 既定の設定を選択する場合は、 **[バックアップの有効化]** を直接選択します。

   * 新しいポリシーの作成 <br>

      1. ファイル共有の新しいバックアップ ポリシーを作成するには、 **[バックアップ ポリシー]** セクションのドロップダウン リストの下にあるリンク テキストを選択します。<br>

         :::image type="content" source="./media/backup-afs/azure-file-share-edit-policy-inline.png" alt-text="新しいポリシーの作成を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-edit-policy-expanded.png":::

      1. 「[新しいポリシーの作成](manage-afs-backup.md#create-a-new-policy)」セクションの手順 3 から 7 を実行します。

      1. ポリシーのすべての属性を定義したら、 **[OK]** をクリックします。

         :::image type="content" source="./media/backup-afs/azure-file-share-policy-parameters-inline.png" alt-text="ポリシー名と保有期間の値の指定を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-policy-parameters-expanded.png":::

   * 既存のバックアップ ポリシーを選択する <br>

      保護を構成するために既存のバックアップ ポリシーの 1 つを選択するには、 **[バックアップ ポリシー]** ドロップダウン リストから任意のポリシーを選択します。<br>

      ![既存のポリシーを選択する](./media/backup-afs/choose-existing-policy.png)

1. **[バックアップの有効化]** を選択してファイル共有の保護を開始します。

   ![バックアップの有効化を選択する](./media/backup-afs/enable-backup.png)

バックアップ ポリシーを設定すると、スケジュールされた時刻にファイル共有のスナップショットが作成されます。 復旧ポイントも、選択した期間にわたって保持されます。


## <a name="configure-backup-from-the-file-share-pane"></a>ファイル共有ペインからバックアップを構成する

次の手順では、個々のファイル共有のバックアップをそれぞれのファイル共有ペインから構成する方法について説明します。

1. [Azure portal](https://portal.azure.com/) で、バックアップするファイル共有をホストしているストレージ アカウントを開きます。

1. そのストレージ アカウント内で、 **[ファイル共有]** というラベルの付いたタイルを選択します。 ストレージ アカウントの目次を使用して **[ファイル共有]** に移動することもできます。

   ![ストレージ アカウント](./media/backup-afs/storage-account.png)

1. ファイル共有の一覧には、ストレージ アカウント内に存在するすべてのファイル共有が表示されます。 バックアップするファイル共有を選択します。

   ![ファイル共有の一覧](./media/backup-afs/file-shares-list.png)

1. ファイル共有ペインの **[操作]** セクションで **[バックアップ]** を選択します。 **バックアップの構成** ペインが右側に読み込まれます。

   ![バックアップの構成ペイン](./media/backup-afs/configure-backup.png)

1. Recovery Services コンテナーの選択で、次のいずれかの操作を行います。

    * 既にコンテナーがある場合は、 **[既存のものを選択]** ラジオ ボタンを選択して Recovery Service コンテナーを選び、 **[コンテナー名]** ドロップダウン メニューから既存のコンテナーのいずれかを選択します。

       ![既存のコンテナーの選択](./media/backup-afs/select-existing-vault.png)

    * コンテナーがない場合は、 **[新規作成]** ラジオ ボタンを選択して Recovery Service コンテナーを作成します。 コンテナーの名前を指定します。 それはファイル共有と同じリージョンに作成されます。 既定では、コンテナーはファイル共有と同じリソース グループ内に作成されます。 別のリソース グループを選択する場合は、 **[リソースの種類]** ドロップダウンの下にある **[新規作成]** リンクを選択し、リソース グループの名前を指定します。 **[OK]** を選択して続行します。

       ![新しいコンテナーの作成](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >ストレージ アカウントがコンテナーに登録されている場合や、保護しようとしているファイル共有をホストしているストレージ アカウント内に、保護された共有が少ない場合は、Recovery Services コンテナーの名前が事前に設定され、それを編集することはできません。[詳細については、こちらを参照してください](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)。

1. **バックアップ ポリシー** を選択するには、次のいずれかの操作を行います。

    * 既定のポリシーをそのままにします。 これにより、毎日のバックアップが 30 日間保持されます。

    * 既存のバックアップ ポリシーがある場合は、 **[バックアップ ポリシー]** ドロップダウン メニューから選択します。

       ![バックアップ ポリシーを選択する](./media/backup-afs/choose-backup-policy.png)

    * 必要に応じて、日単位、週単位、月単位、年単位の保有期間を指定して新しいポリシーを作成します。  

         1. **[新しいポリシーの作成]** リンク テキストを選択します。

         2. 「[新しいポリシーの作成](manage-afs-backup.md#create-a-new-policy)」セクションの手順 3 から 7 を実行します。

         3. ポリシーのすべての属性を定義したら、 **[OK]** をクリックします。

            ![新しいバックアップ ポリシーの作成](./media/backup-afs/create-new-backup-policy.png)

1. **[バックアップの有効化]** を選択してファイル共有の保護を開始します。

   ![[バックアップの有効化] の選択](./media/backup-afs/select-enable-backup.png)

1. ポータルの通知で構成の進行状況を追跡するか、ファイル共有の保護に使用しているコンテナーでのバックアップ ジョブを監視することができます。

   ![Azure portal の通知](./media/backup-afs/portal-notifications.png)

1. バックアップの構成操作が完了したら、ファイル共有ペインの **[操作]** セクションで **[バックアップ]** を選択します。 **Vault Essentials** を示すコンテキスト ペインが右側に読み込まれます。 そこから、オンデマンドのバックアップおよび復元操作をトリガーできます。

   ![Vault Essentials](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>オンデマンド バックアップ ジョブを実行する

場合によっては、バックアップ ポリシーでスケジュールされた以外の時間に、バックアップ スナップショットまたは復旧ポイントを生成することができます。 一般的に、オンデマンド バックアップを生成するのは、バックアップ ポリシーを構成した直後です。 バックアップ ポリシーのスケジュールに基づいて、スナップショットが作成されるまで数時間または数日かかることがあります。 バックアップ ポリシーが適用されるまでデータを保護するために、オンデマンド バックアップを開始します。 多くの場合は、ファイル共有への計画的な変更を行う前にオンデマンド バックアップの作成が必要です。

### <a name="from-backup-center"></a>バックアップ センターから

1. **バックアップ センター** に移動し、メニューから **[バックアップ インスタンス]** をクリックします。

   データソースの種類として **[Azure Files (Azure Storage)]** でフィルター処理します。

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-instances-inline.png" alt-text="バックアップ インスタンスの選択を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-backup-instances-expanded.png":::

1. オンデマンド バックアップ ジョブを実行する項目を選択します。

1. **[バックアップ項目]** メニューの **[今すぐバックアップ]** を選択します。 このバックアップ ジョブはオンデマンドであるため、復旧ポイントに関連付けられた保持ポリシーはありません。

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-now-inline.png" alt-text="[今すぐバックアップ] の選択を示すスクリーンショット。" lightbox="./media/backup-afs/azure-file-share-backup-now-expanded.png":::

1. **[今すぐバックアップ]** ペインが開きます。 復旧ポイントの保持期限を指定します。 オンデマンド バックアップでは、最大保有期間を 10 年に指定できます。

   :::image type="content" source="./media/backup-afs/azure-file-share-on-demand-backup-retention.png" alt-text="保有期間の日付の選択を示すスクリーンショット。":::

1. **[OK]** を選択して、実行するオンデマンド バックアップ ジョブを確定します。

1. ポータルの通知を監視して、バックアップ ジョブの実行完了を把握します。

   **[バックアップ センター]** ダッシュボードでジョブの進行状況を監視するには、 **[バックアップ センター]**  ->  **[バックアップ ジョブ]**  ->  **[実行中]** の順に選択します。

### <a name="from-the-file-share-pane"></a>ファイル共有ペインから

1. オンデマンド バックアップを作成するファイル共有の **概要** ペインを開きます。

1. **[操作]** セクションで **[バックアップ]** を選択します。 **Vault Essentials** を示すコンテキスト ペインが右側に読み込まれます。 **[今すぐバックアップ]** を選択してオンデマンド バックアップを作成します。

   ![[今すぐバックアップ] の選択](./media/backup-afs/select-backup-now.png)

1. **[今すぐバックアップ]** ペインが開きます。 回復ポイントの保持について指定します。 オンデマンド バックアップでは、最大保有期間を 10 年に指定できます。

   ![バックアップ保持の日付](./media/backup-afs/retain-backup-date.png)

1. **[OK]** を選択して確定します。

>[!NOTE]
>対応するアカウント内の任意のファイル共有に対して保護を構成する際は、Azure Backup によってストレージ アカウントがロックされます。 これにより、バックアップされたファイル共有を含むストレージ アカウントが誤って削除されるのを防ぐことができます。

## <a name="best-practices"></a>ベスト プラクティス

* Azure Backup によって作成されたスナップショットを削除しないでください。 スナップショットを削除すると、復旧ポイントが失われたり、復元が失敗したりする場合があります。

* ストレージ アカウントで Azure Backup によって行われたロックは、削除しないでください。 ロックを削除すると、ストレージ アカウントが誤って削除される可能性があります。削除されると、スナップショットまたはバックアップが失われます。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。

* [Azure ファイル共有を復元する](restore-afs.md)
* [Azure ファイル共有のバックアップを管理する](manage-afs-backup.md)
