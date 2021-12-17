---
title: Azure Database for PostgreSQL サーバーを管理する
description: Azure Database for PostgreSQL サーバーの管理について説明します。
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631250"
---
# <a name="manage-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーを管理する

この記事では、Azure Backup サービスでバックアップしている Azure Database for PostgreSQL サーバーの管理方法を説明します。

## <a name="change-policy"></a>ポリシーを変更する

バックアップ インスタンスに関連付けられているポリシーを変更できます。

1. **[バックアップ インスタンス]**  ->  **[ポリシーの変更]** を選択します。


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="ポリシーを変更するオプションのスクリーンショット。":::
   
1. データベースに適用する新しいポリシーを選択します。

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="ポリシーを再割り当てするオプションのスクリーンショット。":::

## <a name="stop-protection-preview"></a>保護を停止する (プレビュー)

Azure Database for PostgreSQL サーバーの保護を停止する方法は 3 つあります。

- **Stop Protection and Retain Data (Retain forever) (保護を停止してデータを保持する (無期限に保持する))** : このオプションでは、Azure Database for PostgreSQL サーバー保護のための将来のバックアップ ジョブをすべて停止します。 ただし、無期限バックアップしている復旧ポイントは Azure Backup サービスによって保持されます。 コンテナーの復旧ポイントを保持するには、料金を支払う必要があります (詳細については、[Azure Backup の料金に関するページ](https://azure.microsoft.com/pricing/details/backup/)を参照してください)。 必要に応じて、これらの復旧ポイントから復元ができます。 保護を再開するには、**Resume backup (バックアップの再開)** オプションを使用します。

- **Stop Protection and Retain Data (Retain as per Policy) (保護を停止してデータを保持する (ポリシーに基づいて保持する))** : このオプションでは、Azure Database for PostgreSQL サーバー保護のための将来のバックアップ ジョブをすべて停止します。 ポリシーに基づいて復旧ポイントを保持し、[Azure Backup 価格](https://azure.microsoft.com/pricing/details/backup/)に従って課金を行います。 ただし、最新の復旧ポイントは無期限に保持します。

- **Stop Protection and Delete Data (保護を停止してデータを削除する)** : このオプションは、Azure Database for PostgreSQL サーバー保護のための将来のバックアップ ジョブをすべて停止し、すべての復旧ポイントを削除します。 データベースの復元、**Resume backup (バックアップの再開)** オプションの使用はできなくなります。

### <a name="stop-protection-and-retain-data"></a>保護を停止してデータを保持する

1. **[Backup center]\(バックアップ センター\)** に移動して **[Azure Database for PostgreSQL server (Preview)]\(Azure Database for PostgreSQL サーバー \(プレビュー\)\)** を選択します。

1. サーバーのバックアップ インスタンスのリストから、保持したいインスタンスを選択します。

1. **[Stop Backup (Preview)]\(バックアップの停止 \(プレビュー\)\)** をクリックします。

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="Azure Database for PostgreSQL サーバーのバックアップ インスタンスから停止するものを選択する操作のスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. 次のデータ保持オプションから 1 つを選びます。

   1. 無期限に保持する
   1. ポリシーに基づいて保持する
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="データ保持方法を選択するオプションのスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   バックアップを停止する理由もドロップダウン リストから選択できます。

1. **[Stop Backup]\(バックアップの停止\)** をクリックします。

1. **[Confirm]\(確認\)** をクリックしてバックアップを停止します。

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="バックアップ停止確認操作のスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

1. **[Backup center]\(バックアップ センター\)** に移動して **[Azure Database for PostgreSQL server (Preview)]\(Azure Database for PostgreSQL サーバー \(プレビュー\)\)** を選択します。

1.  サーバーのバックアップ インスタンスのリストから、削除するインスタンスを選択します。

1. **[Stop Backup (Preview)]\(バックアップの停止 \(プレビュー\)\)** をクリックします。

1. **[Delete Backup Data]\(バックアップ データの削除\)** を選択します。

   バックアップ インスタンスの名前、削除理由、その他のコメントを入力します。

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="バックアップ データを削除するオプションと、必要な情報を追加する操作のスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. **[バックアップの停止]** を選択します。

1. **[Confirm]\(確認\)** をクリックしてバックアップを停止します。

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="バックアップ停止確認操作のスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>保護を再開する

データのバックアップを停止するときに **[Stop Protection and Retain data]\(保護を停止してデータを保持する\)** オプションを選択した場合は、Azure Database for PostgreSQL サーバーの保護を再開できます。

>[!Note]
>データベースの保護を開始すると、保持していたデータにもバックアップ ポリシーを適用します。 ポリシーに従って有効期限が切れた復旧ポイントは削除されます。

次の手順に従います。

1. **[Backup center]\(バックアップ センター\)** に移動して **[Azure Database for PostgreSQL server (Preview)]\(Azure Database for PostgreSQL サーバー \(プレビュー\)\)** を選択します。

1. サーバーのバックアップ インスタンスのリストから、再開するインスタンスを選択します。

1. **[Resume Backup (Preview)]\(バックアップの再開 \(プレビュー\)\)** を選択します。

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="データの保護を再開するオプションのスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. **[Resume backup]\(バックアップの再開\)** をクリックします。

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="データのバックアップを再開するオプションのスクリーンショット。" lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>次のステップ

[バックアップ コンテナーの概要](backup-vault-overview.md)
