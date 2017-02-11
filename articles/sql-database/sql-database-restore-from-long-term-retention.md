---
title: "Azure Recovery Service コンテナーのバックアップからデータベースを復元する | Microsoft Docs"
description: "Azure Recovery Services コンテナーとそのバックアップで使用される領域で、バックアップのためにデータベースを復元する方法のクイック リファレンス"
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>長期的なバックアップ保有期間内のバックアップからデータベースを復元する

この操作方法トピックでは、長期のバックアップ リテンション期間のバックアップからデータベースを復元する方法について説明します。

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Azure Portal を使用して長期のバックアップ リテンション期間から復元する

1. **[Azure vault backups (Azure コンテナーのバックアップ)]** ブレードで、復元するバックアップをクリックし、**[選択]** をクリックします。

    ![コンテナー内のバックアップの選択](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. **[データベース名]** ボックスに、復元されるデータベースの名前を指定します。

    ![新しいデータベース名](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. **[OK]** をクリックして、コンテナー内にあるバックアップから新しいデータベースにデータベースを復元します。

4. ツール バーの通知アイコンをクリックして、復元ジョブの状態を確認します。

    ![コンテナーからの復元ジョブの進行状況](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 復元ジョブが完了したら、**[SQL データベース]** ブレードを開き、新しく復元されたデータベースを確認します。

    ![コンテナーから復元されたデータベース](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」をご覧ください。
>

## <a name="next-steps"></a>次のステップ

- Azure Recovery Services コンテナーの自動バックアップの長期のリテンション期間を構成するには、「[長期のバックアップ リテンション期間の構成](sql-database-configure-long-term-retention.md)」をご覧ください。
- Azure Recovery Services コンテナーでバックアップを表示するには、「[長期的な保有期間のバックアップの表示](sql-database-view-backups-in-vault.md)」をご覧ください。
- サービスによって生成された自動バックアップについては、[自動バックアップ](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。


<!--HONumber=Dec16_HO2-->


