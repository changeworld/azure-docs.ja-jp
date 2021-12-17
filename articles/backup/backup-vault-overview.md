---
title: バックアップ コンテナーの概要
description: バックアップ コンテナーの概要。
ms.topic: conceptual
ms.date: 10/31/2021
ms.custom: references_regions
ms.openlocfilehash: 1a3909e525adadc2e5983e2792e407301a5b321d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473644"
---
# <a name="backup-vaults-overview"></a>バックアップ コンテナーの概要

この記事では、バックアップ コンテナーの機能について説明します。 バックアップ コンテナーは、Azure Backup によってサポートされる特定の新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、Azure Database for PostgreSQL サーバーなど、さまざまな Azure サービスのバックアップ データと Azure Backup によってサポートされる新しいワークロードを保持できます。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、次のような機能を提供します。

- **強化されたバックアップ データの保護機能**:バックアップ コンテナーでは、Azure Backup によりクラウド バックアップを保護するセキュリティ機能が提供されます。 このセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **Azure ロールベースのアクセス制御 (Azure RBAC)** : Azure RBAC を使用すると、Azure のアクセス制御を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 バックアップ コンテナーは Azure RBAC と互換性があります。これにより、定義された一連のユーザー ロールへのバックアップと復元のアクセスが制限されます。 [詳細情報](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>バックアップ コンテナーのストレージ設定

バックアップ コンテナーは、時間の経過と共に作成されたバックアップと復旧ポイントを格納するエンティティです。 バックアップ コンテナーには、保護対象の仮想マシンに関連付けられたバックアップ ポリシーも格納されます。

- Azure Backup では、コンテナーのストレージが自動的に処理されます。 バックアップ コンテナーを作成するときに、ビジネス ニーズに合ったストレージの冗長性を選択します。

- ストレージの冗長性の詳細については、[geo](../storage/common/storage-redundancy.md#geo-redundant-storage) と[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)の冗長性に関する記事を参照してください。

## <a name="encryption-settings-in-the-backup-vault"></a>バックアップ コンテナーの暗号化設定

このセクションでは、バックアップ コンテナーに格納されるバックアップ データを暗号化するために使用できるオプションについて説明します。 Azure Backup サービスは、**バックアップ管理サービス** アプリを使用して Azure Key Vault にアクセスしますが、バックアップ コンテナーのマネージド ID は使用しません。


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化を有効にするためにユーザーが明示的なアクションを実行する必要はありません。 これは、バックアップ コンテナーにバックアップされるすべてのワークロードに適用されます。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、時間の経過と共に作成された復旧ポイントを格納する管理エンティティであり、バックアップ関連の操作を実行するためのインターフェイスを提供します。 たとえば、オンデマンドのバックアップの作成、復元の実行、バックアップ ポリシーの作成などの操作です。

バックアップ コンテナーを作成するには、次の手順に従います。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( <https://portal.azure.com> ) にサインインします。

### <a name="create-backup-vault"></a>バックアップ コンテナーの作成

1. 検索ボックスに「**バックアップ コンテナー**」と入力します。
2. **[サービス]** で **[バックアップ コンテナー]** を選択します。
3. **[バックアップ コンテナー]** ページで、 **[追加]** を選択します。
4. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、 **[新しいリソース グループの作成]** を選択します。 名前として「*myResourceGroup*」と入力します。

    ![新しいリソース グループの作成](./media/backup-vault-overview/new-resource-group.png)

5. **[インスタンスの詳細]** で、 **[バックアップ コンテナー名]** に「*myVault*」と入力し、 **[リージョン]** で任意のリージョン (この例では *[米国東部]* ) を選択します。
6. 次に、 **[ストレージ冗長]** を選択します。 コンテナーに項目を保護した後で、ストレージ冗長を変更することはできません。
7. プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長** 設定を使用することをお勧めします。
8. プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。 [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) 冗長と[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)冗長の詳細をご確認ください。

    ![ストレージ冗長を選択する](./media/backup-vault-overview/storage-redundancy.png)

9. ページの下部にある [確認と作成] ボタンを選択します。

    ![[確認と作成] を選択する](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>バックアップ コンテナーの削除

このセクションでは、バックアップ コンテナーを削除する方法について説明します。 依存関係を削除してからコンテナーの削除を行うための手順が含まれています。

### <a name="before-you-start"></a>開始する前に

次のいずれかの依存関係があるバックアップ コンテナーは削除できません。

- 保護されたデータ ソース (PostgreSQL サーバーの Azure データベースなど) が格納されているコンテナーは削除できません。
- バックアップ データが含まれるコンテナーを削除することはできません。

依存関係を削除せずにコンテナーを削除しようとすると、次のエラー メッセージが表示されます。

>Cannot delete the Backup vault as there are existing backup instances or backup policies in the vault. (既存のバックアップ インスタンスまたはバックアップ ポリシーがバックアップ コンテナー内に存在するため、このコンテナーを削除できません。) Delete all backup instances and backup policies that are present in the vault and then try deleting the vault. (コンテナー内に存在するすべてのバックアップ インスタンスとバックアップ ポリシーを削除してから、コンテナーの削除をもう一度お試しください。)

バックアップ コンテナーを削除する前に、**バックアップ センター** の **データソースの種類** のフィルター オプションで、削除する必要がある既存のバックアップ インスタンスまたはポリシーを忘れないようにしてください。

![データ ソースの種類](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>コンテナーを削除する正しい方法

>[!WARNING]
下記の操作は破壊的であり、元に戻すことはできません。 保護されたサーバーに関連付けられているすべてのバックアップ データとバックアップ項目が、完全に削除されます。 慎重に進めてください。

コンテナーを正しく削除するには、次の順序で手順に従う必要があります。

- 保護された項目があるかどうかを確認します。
  - 左側のナビゲーション バーの **[Backup Instances]\(バックアップ インスタンス\)** に移動します。 ここに一覧表示されているすべての項目を最初に削除する必要があります。

これらの手順を完了したら、引き続きコンテナーを削除できます。

### <a name="delete-the-backup-vault"></a>バックアップ コンテナーの削除

コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** を選択します。 コンテナーを削除するかどうかを確認するメッセージが表示されます。

![コンテナーを削除する](./media/backup-vault-overview/delete-vault.png)

1. コンテナーを削除することを確認するために、 **[はい]** を選択します。 コンテナーが削除されます。 ポータルが **[新規作成]** サービス メニューに戻ります。

## <a name="monitor-and-manage-the-backup-vault"></a>バックアップ コンテナーの監視および管理

このセクションでは、バックアップ コンテナーの **[概要]** ダッシュボードを使用して、バックアップ コンテナーの監視と管理を行う方法について説明します。 [概要] ペインには、 **[ジョブ]** と **[インスタンス]** の 2 つのタイルがあります。

![概要ダッシュボード](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>バックアップ インスタンスの管理

**[ジョブ]** タイルには、バックアップ コンテナーのすべてのバックアップおよび復元関連のジョブの概要が表示されます。 このタイルで数字を選択すると、ジョブの詳細情報を表示でき、特定のデータ ソースの種類、操作の種類、状態を確認できます。

![バックアップ インスタンス](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>バックアップ ジョブの管理

**[Backup Instances]\(バックアップ インスタンス\)** タイルには、バックアップ コンテナーのすべてのバックアップ インスタンスの概要が表示されます。 このタイルで数字を選択すると、バックアップ インスタンスの詳細情報を表示でき、特定のデータ ソースの種類と保護の状態を確認できます。

![バックアップ ジョブ](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>Azure サブスクリプション/リソース グループ間でバックアップ コンテナーを移動する (パブリック プレビュー)

このセクションでは、Azure サブスクリプションとリソース グループ間で、Azure portal を使用してバックアップ コンテナー (Azure Backup 用に構成) を移動する方法について説明します。

>[!Note]
>[PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) および [CLI](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move) を使用して、別のリソース グループやサブスクリプションにバックアップ コンテナーを移動することもできます。

### <a name="supported-regions"></a>サポートされているリージョン

サブスクリプションとリソース グループの間のコンテナーの移動は、すべてのパブリック リージョンでサポートされます。

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>Azure portal を使用してバックアップ コンテナーを別のリソース グループに移動する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. バックアップ コンテナーの一覧を開き、移動するコンテナーを選択します。

   コンテナー ダッシュボードには、コンテナーの詳細が表示されます。

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="別のリソース グループに移動するコンテナーのダッシュボードを示すスクリーンショット。" lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. コンテナーの **[概要]** メニューで **[移動]** をクリックし、 **[別のリソース グループに移動する]** を選択します。

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="バックアップ コンテナーを別のリソース グループに移動するオプションを示すスクリーンショット。" lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >管理者のサブスクリプションのみが、コンテナーを移動するのに必要なアクセス許可を持ちます。

1. **[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、 **[新規作成]** を選択して、新しいリソース グループを作成します。

   サブスクリプションは同じままで、自動的に設定されます。

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="既存のリソース グループの選択または新しいリソース グループの作成を示すスクリーンショット。" lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. **[移動するリソース]** タブで、移動する必要のあるバックアップ コンテナーが検証されます。 このプロセスは数分かかることがあります。 検証が完了するまで待ってください。

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="バックアップ コンテナーの検証状態を示すスクリーンショット。" lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. _[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]_ チェックボックスを選択して確定し、 **[移動]** を選択します。
 
   >[!Note]
   >リソース グループまたはサブスクリプション間でコンテナーを移動すると、リソース パスが変更されます。 移動操作が完了した後で、ツールとスクリプトを新しいリソース パスで更新してください。

移動操作が完了するまで待って、コンテナーに対して他の操作を実行します。 移動中にバックアップ コンテナーに対して実行された操作は失敗します。 プロセスが完了すると、バックアップ コンテナーがターゲット リソース グループに表示されます。

>[!Important]
>コンテナーの移動中にエラーが発生した場合は、「[エラー コードとトラブルシューティング](#error-codes-and-troubleshooting)」セクションを参照してください。  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>Azure portal を使用してバックアップ コンテナーを別のサブスクリプションに移動する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. バックアップ コンテナーの一覧を開き、移動するコンテナーを選択します。
   
   コンテナー ダッシュボードには、コンテナーの詳細が表示されます。

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="別の Azure サブスクリプションに移動するコンテナーのダッシュボードを示すスクリーンショット。" lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. コンテナーの **[概要]** メニューで **[移動]** をクリックし、 **[別のサブスクリプションに移動する]** を選択します。

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="バックアップ コンテナーを別の Azure サブスクリプションに移動するオプションを示すスクリーンショット。" lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >管理者のサブスクリプションのみが、コンテナーを移動するのに必要なアクセス許可を持ちます。

1. **[サブスクリプション]** ドロップダウン リストから既存のサブスクリプションを選択します。

   サブスクリプション間でコンテナーを移動する場合、ターゲット サブスクリプションがソース サブスクリプションと同じテナントに存在する必要があります。 コンテナーを別のテナントに移動するには、「[サブスクリプションを別のディレクトリに転送する](../role-based-access-control/transfer-subscription.md)」を参照してください。

1. **[リソース グループ]** ドロップダウン リストで、既存のリソース グループを選択するか、 **[新規作成]** を選択して、新しいリソース グループを作成します。

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="別の Azure サブスクリプションでの既存のリソース グループの選択または新しいリソース グループの作成を示すスクリーンショット。" lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. **[移動するリソース]** タブで、移動する必要のあるバックアップ コンテナーが検証されます。 このプロセスは数分かかることがあります。 検証が完了するまで待ってください。

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="別の Azure サブスクリプションに移動するバックアップ コンテナーの検証状態を示すスクリーンショット。" lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. _[移動されたリソースに関連付けられているツールとスクリプトは、新しいリソース ID を使用するように更新するまで動作しないことを理解しました。]_ チェックボックスを選択して確定し、 **[移動]** を選択します。
 
   >[!Note]
   >リソース グループまたはサブスクリプション間でコンテナーを移動すると、リソース パスが変更されます。 移動操作が完了した後で、ツールとスクリプトを新しいリソース パスで更新してください。

移動操作が完了するまで待って、コンテナーに対して他の操作を実行します。 移動中にバックアップ コンテナーに対して実行された操作は失敗します。 プロセスが完了すると、バックアップ コンテナーがターゲットのサブスクリプションとリソース グループに表示されます。

>[!Important]
>コンテナーの移動中にエラーが発生した場合は、「[エラー コードとトラブルシューティング](#error-codes-and-troubleshooting)」セクションを参照してください。

### <a name="error-codes-and-troubleshooting"></a>エラー コードとトラブルシューティング

バックアップ コンテナーの移動中に発生する可能性がある次の一般的な問題のトラブルシューティングを行います。

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

**原因**: バックアップ コンテナーの移動が一部しか成功しなければ、このエラーが発生する可能性があります。

**推奨事項**: 問題は 36 時間以内に自動的に解決されます。 問題が解決しない場合は、Microsoft サポートに連絡してください。

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

**原因**: バックアップ コンテナーの移動が非常に失敗すると、このエラーが発生する可能性があります。 

**推奨事項**: 問題は 36 時間以内に自動的に解決されます。 問題が解決しない場合は、Microsoft サポートに連絡してください。 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

**原因**: 移動中にバックアップ コンテナーに対して操作を実行しようとすると、このエラーが発生する可能性があります。 

**推奨事項**: 移動操作が完了するまで待ち、再試行します。 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

**原因**: 1 回の試行で複数のバックアップ コンテナーを移動しようとすると、このエラーが発生する可能性があります。 

**推奨事項**: 移動操作ごとに 1 つのバックアップ コンテナーのみが選択されるようにします。 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

**原因**: コンテナーがまだプロビジョニングされていない場合、このエラーが発生する可能性があります。 

**推奨事項**: しばらく待ってから、操作を再試行してください。

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

**原因**: バックアップ コンテナーのリソース移動は、現在、選択した Azure リージョンではサポートされていません。

**推奨事項**: バックアップ コンテナーを移動するために、サポートされているリージョンのいずれかを選択してください。 「 [サポートされているリージョン](#supported-regions)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure PostgreSQL データベースでバックアップを構成する](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
