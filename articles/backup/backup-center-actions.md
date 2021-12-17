---
title: バックアップ センターを使用してアクションを実行する
description: この記事では、バックアップ センターを使用してアクションを実行する方法について説明します
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: e8b357c19caeb20c2a50d9a05f9d8d583bd8f640
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083622"
---
# <a name="perform-actions-using-backup-center"></a>バックアップ センターを使用してアクションを実行する

バックアップ センターを使用すると、個々のコンテナーに移動することなく、中央のインターフェイスからバックアップ関連の主要なアクションを実行できます。 バックアップ センターから実行できるいくつかのアクションを次に示します。

* データソースのバックアップを構成する
* バックアップ インスタンスを復元する
* 新しい資格情報コンテナーの作成
* 新しいバックアップ ポリシーを作成する
* バックアップ インスタンスのオンデマンド バックアップをトリガーする
* バックアップ インスタンスのバックアップを停止する

## <a name="supported-scenarios"></a>サポートされるシナリオ

* バックアップ センターでは現在、Azure VM のバックアップ、Azure VM 内の SQL のバックアップ、Azure VM 内の SAP HANA のバックアップ、Azure Files のバックアップ、Azure Database for PostgreSQL サーバーのバックアップがサポートされています。
* サポートされるシナリオとサポートされないシナリオの詳細な一覧については、<bpt id="p1">[</bpt>サポート マトリックス<ept id="p1">](backup-center-support-matrix.md)</ept>を参照してください。

## <a name="configure-backup"></a>バックアップの構成

Azure AM、Azure VM 内の SQL、Azure VM 内の SAP HANA、または Azure Files をバックアップする場合は、Recovery Services コンテナーを使用する必要があります。 Azure Database for PostgreSQL サーバーをバックアップする場合は、バックアップ コンテナーを使用する必要があります。 

バックアップするデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="configure-backup-to-a-recovery-services-vault"></a>Recovery Services コンテナーへのバックアップを構成する

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[+ バックアップ]<ept id="p1">**</ept> を選択します。

    ![バックアップ センターの概要](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. バックアップするデータソースの種類を選択します。

    ![VM バックアップを構成するためのデータソースを選択する](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Recovery Services コンテナーを選択し、 <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じバックアップの構成エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンのバックアップを構成する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](tutorial-backup-vm-at-scale.md)</ept>を参照してください。

### <a name="configure-backup-to-a-backup-vault"></a>バックアップ コンテナーへのバックアップを構成する

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[+ バックアップ]<ept id="p1">**</ept> を選択します。
2. バックアップするデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーのバックアップを構成するためのデータソースを選択する](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、バックアップ コンテナーから到達できるものと同じバックアップの構成エクスペリエンスに移動します。 バックアップ コンテナーで Azure Database for PostgreSQL サーバーのバックアップを構成する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)</ept>を参照してください。

## <a name="restore-a-backup-instance"></a>バックアップ インスタンスを復元する

復元するデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Recovery Services コンテナーから復元する場合

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[復元]<ept id="p1">**</ept> を選択します。

    ![VM を復元するためのバックアップ センターの概要](./media/backup-center-actions/backup-center-overview-restore.png)

2. 復元するデータソースの種類を選択します。

    ![VM の復元のためのデータソースを選択する](./media/backup-center-actions/restore-select-datasource-vm.png)

3. バックアップ インスタンスを選択し、 <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じ設定の復元エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンを復元する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](backup-azure-arm-restore-vms.md#before-you-start)</ept>を参照してください。

### <a name="if-youre-restoring-from-a-backup-vault"></a>バックアップ コンテナーから復元する場合

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[復元]<ept id="p1">**</ept> を選択します。
2. 復元するデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーの復元のためのデータソースを選択する](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. バックアップ インスタンスを選択し、 <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じ設定の復元エクスペリエンスに移動します。 バックアップ コンテナーで Azure Database for PostgreSQL サーバーを復元する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](restore-azure-database-postgresql.md)</ept>を参照してください。

## <a name="create-a-new-vault"></a>新しい資格情報コンテナーの作成

バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[+ コンテナー]<ept id="p1">**</ept> を選択することによって新しいコンテナーを作成できます。

![[コンテナーの作成]](./media/backup-center-actions/backup-center-create-vault.png)

* <bpt id="p1">[</bpt>Recovery Services コンテナーの作成の詳細について学習する<ept id="p1">](backup-create-rs-vault.md)</ept>
* <bpt id="p1">[</bpt>バックアップ コンテナーの作成の詳細について学習する<ept id="p1">](backup-vault-overview.md)</ept>

## <a name="create-a-new-backup-policy"></a>新しいバックアップ ポリシーを作成する

バックアップするデータソースの種類に応じて、以下で説明されている適切な手順に従ってください。

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Recovery Services コンテナーにバックアップする場合

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[+ ポリシー]<ept id="p1">**</ept> を選択します。

    ![バックアップ ポリシーのためのバックアップ センターの概要](./media/backup-center-actions/backup-center-overview-policy.png)

2. バックアップするデータソースの種類を選択します。

    ![VM バックアップのポリシーのためのデータソースを選択する](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Recovery Services コンテナーを選択し、 <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、Recovery Services コンテナーから到達できるものと同じポリシーの作成エクスペリエンスに移動します。 Recovery Services コンテナーで Azure 仮想マシンの新しいバックアップ ポリシーを作成する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](backup-azure-arm-vms-prepare.md#create-a-custom-policy)</ept>を参照してください。

### <a name="if-youre-backing-up-to-a-backup-vault"></a>バックアップ コンテナーにバックアップする場合

1. バックアップ センターに移動し、 <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> タブの上部にある <bpt id="p1">**</bpt>[+ ポリシー]<ept id="p1">**</ept> を選択します。
2. バックアップするデータソースの種類 (この場合は Azure Database for PostgreSQL サーバー) を選択します。

    ![Azure Database for PostgreSQL サーバーのバックアップのポリシーのためのデータソースを選択する](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> を選択します。 これにより、バックアップ コンテナーから到達できるものと同じポリシーの作成エクスペリエンスに移動します。 バックアップ コンテナーで新しいバックアップ ポリシーを作成する方法の詳細については、<bpt id="p1">[</bpt>ここ<ept id="p1">](backup-azure-database-postgresql.md#create-backup-policy)</ept>を参照してください。

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>バックアップ インスタンスのオンデマンド バックアップを実行する

バックアップ センターを使用すると、バックアップ資産全体にわたってバックアップ インスタンスを検索したり、オンデマンドでバックアップ操作を実行したりできます。

保護を停止するには、バックアップ センターに移動し、 **[インスタンスのバックアップ]** メニュー項目を選択します。 これを選択すると、アクセスできるすべてのバックアップ インスタンスの詳細を表示できます。 バックアップするバックアップ インスタンスを検索できます。 グリッド内の項目を右クリックすると、使用可能なアクションの一覧が表示されます。 オンデマンド バックアップを実行するには、 <bpt id="p1">**</bpt>[今すぐバックアップ]<ept id="p1">**</ept> オプションを選択します。

![オンデマンド バックアップ](./media/backup-center-actions/backup-center-on-demand-backup.png)

<bpt id="p1">[</bpt>Azure Virtual Machines のオンデマンド バックアップの実行の詳細について学習します<ept id="p1">](backup-azure-manage-vms.md#run-an-on-demand-backup)</ept>。

<bpt id="p1">[</bpt>Azure Database for PostgreSQL サーバーのオンデマンド バックアップの実行の詳細について学習します<ept id="p1">](backup-azure-database-postgresql.md)</ept>。

## <a name="stop-backup-for-a-backup-instance"></a>バックアップ インスタンスのバックアップを停止する

バックアップの対象である基になるリソースが既に存在しなくなった場合など、バックアップ インスタンスのバックアップを停止することが必要になるシナリオが存在します。

保護を停止するには、バックアップ センターに移動し、 **[インスタンスのバックアップ]** メニュー項目を選択します。 これを選択すると、アクセスできるすべてのバックアップ インスタンスの詳細を表示できます。 バックアップするバックアップ インスタンスを検索できます。 グリッド内の項目を右クリックすると、使用可能なアクションの一覧が表示されます。 バックアップ インスタンスのバックアップを停止するには、 <bpt id="p1">**</bpt>[バックアップの停止]<ept id="p1">**</ept> オプションを選択します。

![保護の停止](./media/backup-center-actions/backup-center-stop-protection.png)

- Azure Virtual Machines のバックアップの停止に関する[詳細情報を参照してください](backup-azure-manage-vms.md#stop-protecting-a-vm)。
- ディスクのバックアップの停止に関する[詳細情報を参照してください](manage-azure-managed-disks.md#stop-protection-preview)。
- Azure Database for PostgreSQL サーバーのバックアップの停止に関する[詳細情報を参照してください](manage-azure-database-postgresql.md#stop-protection-preview)。

## <a name="resume-backup"></a>バックアップの再開

**保護の停止** 中にデータを保持していた場合、ワークロードのバックアップを再開することができます。

- ディスクの保護の再開に関する[詳細情報を参照してください](manage-azure-database-postgresql.md#resume-protection)。
- [Azure Database for PostgreSQL サーバーの保護の再開](manage-azure-managed-disks.md#resume-protection)に関する詳細情報を参照してください。

## <a name="next-steps"></a>次のステップ

* <bpt id="p1">[</bpt>バックアップを監視および操作する<ept id="p1">](backup-center-monitor-operate.md)</ept>
* <bpt id="p1">[</bpt>バックアップ資産を管理する<ept id="p1">](backup-center-govern-environment.md)</ept>
* <bpt id="p1">[</bpt>バックアップに関する分析情報を取得する<ept id="p1">](backup-center-obtain-insights.md)</ept>
