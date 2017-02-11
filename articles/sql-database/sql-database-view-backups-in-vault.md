---
title: "Azure Recovery Services コンテナーでバックアップを表示する | Microsoft Docs"
description: "Azure Recovery Services コンテナーとそのバックアップで使用される領域で、バックアップを表示する方法のクイック リファレンス"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>長期のバックアップ リテンション期間のデータベース バックアップに関する情報を確認する

この操作方法トピックでは、長期のバックアップ リテンション期間のデータベース バックアップに関する情報を確認する方法について説明します。

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Azure Portal を使用して長期のバックアップ リテンション期間の情報を確認する 

1. Azure Recovery Services コンテナーのブレードを開き (**[すべてのリソース]** に移動し、サブスクリプションのリソースの一覧から選びます)、コンテナー内のデータベース バックアップで使用されているストレージの容量を確認します。

   ![バックアップを含む Recovery Services コンテナーの表示](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. データベースの **[SQL database]** ブレードを開きます。

    ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

3. ツール バーの **[復元]** をクリックします。

    ![ツール バーの [復元]](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. [復元] ブレードの **[長期]** をクリックします。

5. [Azure vault backups (Azure コンテナーのバックアップ)] の **[バックアップの選択]** をクリックして、長期的なバックアップ保有期間内の使用可能なデータベース バックアップを表示します。

    ![コンテナー内のバックアップ](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」をご覧ください。
>

## <a name="next-steps"></a>次のステップ

- Azure Recovery Services コンテナーの自動バックアップの長期のリテンション期間を構成するには、「[](sql-database-configure-long-term-retention.md)」をご覧ください。
- 長期のリテンション期間のバックアップからデータベースを復元するには、「[長期のバックアップ リテンション期間からの復元](sql-database-restore-from-long-term-retention.md)」をご覧ください。
- サービスによって生成された自動バックアップについては、「[自動バックアップ](sql-database-automated-backups.md)」をご覧ください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。


<!--HONumber=Dec16_HO2-->


