---
title: Microsoft Azure Backup Server v3 のリリース ノート
description: この記事では、Microsoft Azure Backup Server (MABS) v3 の既知の問題と回避策に関する情報を提供します。
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a5c99bcb95fde39bddc9e9db9ab000881c89081a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185627"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server のリリース ノート

この記事では、Microsoft Azure Backup Server (MABS) V3 の既知の問題と回避策を提供します。

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>クラスター化されたワークロードのバックアップと回復に失敗します

**説明:** 28MABS V2 から MABS V3 にアップグレードした後、HYPER-V クラスターや SQL クラスター (SQL Always On) などのクラスター化されたデータ ソース、または データベース可用性グループ (DAG) の Exchange のバックアップ/復元に失敗します。

**回避策:** これを防ぐには、SQL Server Management Studio (SSMS) を開き、DPM DB に対して次の SQL スクリプトを実行します。

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>ロシア語のロケールで MABS V3 へのアップグレードに失敗します

**説明:** ロシア語ロケールでの MABS V2 から MABS V3 へのアップグレードは、エラー コード **4387** で失敗します。

**回避策:** 次の手順に従い、ロシア語のインストール パッケージを使用して MABS V3 にアップグレードします。

1. [バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) SQL データベースと MABS V2 のアンインストール (アンインストール中に、保護されたデータを保持すると選択)。
2. SQL 2017 (Enterprise) にアップグレードし、アップグレードの一環としてレポートをアンインストールします。
3. SQL Server Reporting Services (SSRS)[をインストール](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server)。
4. SQL Server Management Studio (SSMS) を[インストール](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。
5. [SQL 2017 を使用した SSRS 構成](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)に記載されているパラメーターを使用してレポートを構成します。
6. MABS V3を [インストール](backup-azure-microsoft-azure-backup.md)。
7. [ここ](https://docs.microsoft.com/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync)に記されている通り、SSMS と実行 DPM 同期ツールを使用して SQL を[復元](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017)します。
8. 次のコマンドを使用して dbo.tbl_DLS_GlobalSetting テーブルの 'DataBaseVersion' プロパティを更新します：

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. MSDPM サービスを開始します。

## <a name="next-steps"></a>次のステップ

[MABS V3 の新機能](backup-mabs-whats-new-mabs.md)
