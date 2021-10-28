---
title: Azure Managed Disks を管理する
description: Azure portal での Azure Managed Disks の管理について説明します。
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 38f131d9656f604ee4d53b5d1de0f16ffa56b9ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215993"
---
# <a name="manage-azure-managed-disks"></a>Azure Managed Disks を管理する

この記事では、Azure portal から Azure Managed Disks を管理する方法について説明します。

## <a name="stop-protection-preview"></a>保護を停止する (プレビュー)


Azure ディスクの保護を停止するには、次の 3 つの方法があります。

- **保護を停止し、データを保持する (無期限に保持)** : このオプションを使用すると、ディスクを保護する将来のすべてのバックアップ ジョブが停止されます。 ただし、バックアップされている回復ポイントは、Azure Backup サービスによって永久に保持されます。 コンテナーの復旧ポイントを保持するには、料金を支払う必要があります (詳細については、[Azure Backup の料金に関するページ](https://azure.microsoft.com/pricing/details/backup/)を参照してください)。 必要な場合は、ディスクを復元することができます。 ディスクの保護を再開するには、 **[バックアップの再開]** オプションを使用します。

- **保護を停止し、データを保持する (ポリシーに従って保持)** : このオプションを使用すると、ディスクを保護する将来のすべてのバックアップ ジョブが停止されます。 回復ポイントはポリシーに従って保持され、[Azure Backup の価格](https://azure.microsoft.com/pricing/details/backup/)に従って課金されます。 ただし、最新の回復ポイントはいつまでも保持されます。

- **保護を停止し、データを削除する**: このオプションを使用すると、ディスクを保護する将来のすべてのバックアップ ジョブが停止され、すべての回復ポイントが削除されます。 ディスクを復元することも、 **[バックアップの再開]** オプションを使用することもできなくなります。

### <a name="stop-protection-and-retain-data"></a>保護を停止してデータを保持する

1. **バックアップ センター** に移動し、 **[Azure ディスク]** を選択します。

1. ディスク バックアップ インスタンスの一覧から、保持するインスタンスを選択します。

1. **[バックアップの停止 (プレビュー)]** を選択します。

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="停止する Azure ディスク バックアップ インスタンスの選択を示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. 次のいずれかのデータ保持オプションを選択します。

   1. 無期限に保持する
   1. ポリシーに従って保持する
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="ディスク バックアップ インスタンスの保護を停止するオプションを示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   ドロップダウン リストからバックアップを停止する理由を選択することもできます。

1. **[バックアップの停止]** をクリックします。

1. **[確認]** を選択してデータ保護を停止します。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="ディスク バックアップ インスタンス保持オプションの選択を示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

1. **バックアップ センター** に移動し、 **[Azure ディスク]** を選択します。

1. ディスク バックアップ インスタンスの一覧から、削除するインスタンスを選択します。

1. **[バックアップの停止 (プレビュー)]** をクリックします。

1. **[Delete Backup Data]\(バックアップ データの削除\)** を選択します。

   バックアップ インスタンスの名前、削除の理由、その他のコメントを指定します。

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="ディスク バックアップ停止の確認のスクリーンショット。" lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. **[バックアップの停止]** を選択します。

1. **[確認]** を選択してデータ保護を停止します。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="ディスク バックアップ インスタンス保持オプションの選択を示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>保護を再開する

**保護を停止してデータを保持する** オプションを選択した場合は、ディスクの保護を再開できます。

>[!Note]
>ディスクの保護を開始すると、保持されているデータにもバックアップ ポリシーが適用されます。 ポリシーに従って有効期限が切れた回復ポイントはクリーンアップされます。

次の手順に従います。

1. **バックアップ センター** に移動し、 **[Azure ディスク]** を選択します。

1. ディスク バックアップ インスタンスの一覧から、再開するインスタンスを選択します。

1. **[バックアップの再開 (プレビュー)]** を選択します。

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="ディスクの保護を再開するオプションを示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. **[バックアップの再開]** を選択します。

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="ディスクのバックアップを再開するオプションを示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>バックアップ インスタンスを削除する

スケジュールされたバックアップ ジョブをすべて停止し、既存のすべてのバックアップを削除する場合は、 **[バックアップ インスタンスの削除]** を使用します。

>[!Note]
>スナップショット リソース グループを手動で削除した場合、またはバックアップ コンテナーのマネージド ID に対するアクセス許可が取り消された場合、バックアップ インスタンスの削除は失敗します。 そのようなエラーが発生した場合は、スナップショット リソース グループを (同じ名前で) 一時的に作成し、バックアップ コンテナーのマネージド ID に[こちら](./backup-managed-disks-ps.md#assign-permissions)に記載されている必要なロールのアクセス許可を指定します。 スナップショット リソース グループの名前は、 **[バックアップ インスタンス]** 画面の **[基本]** タブで見つけることができます。 

ディスク バックアップ インスタンスを削除するには、次の手順のようにします。

1. バックアップ インスタンス画面で **[削除]** をクリックします。

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="バックアップ インスタンスを削除するプロセスを示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. バックアップ インスタンスの名前、削除の理由、追加のコメントなど、確認の詳細を入力します。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="バックアップ インスタンスの削除の確認を示すスクリーンショット。" lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. **[削除]** をクリックし、バックアップ インスタンスの削除を確定して続行します。

## <a name="next-steps"></a>次のステップ

[バックアップ コンテナーの概要](backup-vault-overview.md)