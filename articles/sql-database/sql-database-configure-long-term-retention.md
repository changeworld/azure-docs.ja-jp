---
title: "Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する | Microsoft Docs"
description: "Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する方法に関するクイック リファレンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する 

この操作方法トピックでは、Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する方法について説明します。

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Azure Portal を使用して長期的な保有期間を構成する

1. お使いのサーバーの **[SQL Server]** ブレードを開きます。

    ![[SQL Server] ブレード](./media/sql-database-get-started/sql-server-blade.png)

2. **[Long-term backup retention (長期的なバックアップ保有期間)]** をクリックします。

   ![長期的なバックアップ保有期間のリンク](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. **[Long-term backup retention (長期的なバックアップ保有期間)]** ブレードで、プレビューの使用条件を確認して同意します (既に同意している場合またはこの機能がプレビュー段階ではなくなった場合を除く)。

   ![プレビューの使用条件の承認](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. データベースの長期的なバックアップ保有期間を構成するには、グリッドでそのデータベースを選択し、ツール バーの **[構成]** をクリックします。

   ![長期的なバックアップ保有期間の対象となるデータベースの選択](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. **[構成]** ブレードの **[Recovery service vault (Recovery Services コンテナー)]** で **[必要な設定の構成]** をクリックします。

   ![コンテナーの構成リンク](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. **[Recovery Services コンテナー]** ブレードで、既存のコンテナーを選択します (ある場合)。 また、サブスクリプションに Recovery Services コンテナーが見つからなかった場合は、フローをクリックして終了し、Recovery Services コンテナーを作成します。

   ![新しいコンテナーの作成リンク](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. **[Recovery Services コンテナー]** ブレードの **[追加]** をクリックします。

   ![新しいコンテナーの追加リンク](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. **[Recovery Services コンテナー]** ブレードで、新しい Recovery Services コンテナーに有効な名前を指定します。

   ![新しいコンテナーの名前](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. サブスクリプションとリソース グループを選択し、コンテナーの場所を選択します。 完了したら、**[作成]** をクリックします。

   ![新しいコンテナーの作成](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > コンテナーは、Azure SQL 論理サーバーと同じリージョンに配置する必要があります。また、論理サーバーと同じリソース グループを使用する必要があります。
   >

10. 新しいコンテナーが作成されたら、必要な手順を実行して **[Recovery Services コンテナー]** ブレードに戻ります。

11. **[Recovery Services コンテナー]** ブレードで、コンテナーをクリックし、**[選択]** をクリックします。

   ![既存のコンテナーの選択](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. **[構成]** ブレードで、新しい保持ポリシーに有効な名前を指定し、必要に応じて既定の保持ポリシーを変更して、**[OK]** をクリックします。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. **[Long-term backup retention (長期的なバックアップ保有期間)]** ブレードで、**[保存]** をクリックし、**[OK]** をクリックして、選択したすべてのデータベースに長期的なバックアップ保持ポリシーを適用します。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. **[保存]** をクリックして、構成した Azure Recovery Services コンテナーに対して、この新しいポリシーを使用して長期的なバックアップ保有期間を有効にします。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

14. 長期的なバックアップ保有期間が有効になったら、**[Recovery Services コンテナー]** ブレードを開きます (**[すべてのリソース]** に移動し、サブスクリプションのリソースの一覧から選択します)。

   ![Recovery Services コンテナーの表示](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」を参照してください。
>

## <a name="next-steps"></a>次のステップ

- 長期的な保有期間のバックアップからデータベースを復元するには、[長期的な保有期間のバックアップからの復元](sql-database-restore-from-long-term-retention.md)に関する記事を参照してください。
- Azure Recovery Services コンテナーでバックアップを表示するには、[長期的な保有期間のバックアップの表示](sql-database-view-backups-in-vault.md)に関する記事を参照してください。
- サービスによって生成された自動バックアップについては、[自動バックアップ](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。


<!--HONumber=Dec16_HO2-->


