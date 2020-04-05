---
title: Recovery Services コンテナーの作成
description: この記事では、バックアップと復旧ポイントを格納する Recovery Services コンテナーを作成する方法について説明します。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e722996f516d21445d8e0028df925ca44eb02bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295012"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

2. 左側のメニューから、 **[すべてのサービス]** を選択します。

    ![[すべてのサービス] を選択する](./media/backup-create-rs-vault/click-all-services.png)

3. **[すべてのサービス]** ダイアログ ボックスに、「**Recovery Services**」と入力します。 入力に従って、リソースの一覧がフィルター処理されます。 リソースの一覧から **[Recovery Services コンテナー]** を選択します。

    ![[Recovery Services コンテナー] と入力して選択する](./media/backup-create-rs-vault/all-services.png)

    サブスクリプションに Recovery Services コンテナーの一覧が表示されます。

4. **[Recovery Services コンテナー]** ダッシュボードで **[追加]** を選択します。

    ![[Recovery Services コンテナー] を追加する](./media/backup-create-rs-vault/add-button-create-vault.png)

    **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。 **[名前]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** に値を入力します。

    ![[Recovery Services コンテナー] を構成する](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **[名前]** : コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
   - **[サブスクリプション]** : 使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
   - **[リソース グループ]** : 既存のリソース グループを使用するか、新しいリソース グループを作成します。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リスト ボックスからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。
   - **[場所]** : コンテナーのリージョンを選択します。 仮想マシンを保護するコンテナーを作成する場合、コンテナーは仮想マシンと同じリージョンにある**必要があります**。

      > [!IMPORTANT]
      > VM の場所が不明な場合は、ダイアログ ボックスを閉じます。 ポータルの仮想マシンの一覧に移動します。 いくつかのリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成します。 まず最初の場所にコンテナーを作成してから、別の場所にコンテナーを作成します。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup サービスで自動的に処理されます。
      >
      >

5. Recovery Services コンテナーを作成する準備ができたら、 **[作成]** を選択します。

    ![[Recovery Services コンテナー] を作成する](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services コンテナーの作成に時間がかかることがあります。 **[通知]** 領域 (ポータルの右上隅) で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、 **[最新の情報に更新]** を選択します。

     ![バックアップ コンテナーの一覧を更新する](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>ストレージ冗長性の設定

Azure Backup では、コンテナーのストレージが自動的に処理されます。 ストレージをレプリケートする方法を指定する必要があります。

1. **[Recovery Services コンテナー]** ブレードで、新しいコンテナーをクリックします。 **[設定]** セクションの **[プロパティ]** をクリックします。
2. **[プロパティ]** で、 **[バックアップ構成]** の **[更新]** をクリックします。

3. ストレージのレプリケーションの種類を選択し、 **[保存]** をクリックします。

     ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長**設定を使用することをお勧めします。
   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。
   - [geo](../storage/common/storage-redundancy-grs.md) 冗長と[ローカル](../storage/common/storage-redundancy-lrs.md)冗長の詳細をご確認ください。

> [!NOTE]
> Recovery Services コンテナーの**ストレージのレプリケーションの種類** (ローカル冗長/geo 冗長) の変更は、コンテナーでバックアップを構成する前に行う必要があります。 バックアップをいったん構成すると、変更オプションは無効になり、**ストレージのレプリケーションの種類**は変更できません。

## <a name="set-cross-region-restore"></a>リージョンをまたがる復元の設定

復元オプションの 1 つである、リージョンをまたがる復元 (CRR) を使用すると、Azure VM をセカンダリ リージョン ([Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)) で復元できます。 このオプションを使用すると、次のことができます。

- 監査またはコンプライアンスの必要がある場合にドリルを行う
- プライマリ リージョンで障害が発生した場合に、VM またはそのディスクを復元する。

この機能を選択するには、 **[バックアップ構成]** ブレードから **[Enable Cross Region Restore]\(リージョンをまたがる復元を有効にする\)** を選択します。

このプロセスは、ストレージ レベルでの料金に影響します。

>[!NOTE]
>作業を開始する前に、次のことを行います。
>
>- サポートされているマネージド型とリージョンの一覧については、[サポート マトリックス](backup-support-matrix.md#cross-region-restore)を参照してください。
>- リージョンをまたがる復元 (CRR) 機能は現在、以下のリージョンでのみご利用いただけます。 
>    - 米国中西部
>    - 米国西部 2
>    - 米国中南部
>    - East US
>    - 米国東部 2
>    - 米国中北部
>    - カナダ中部
>    - カナダ東部
>    - オーストラリア東部
>    - オーストラリア南東部
>    - インド中部
>    - インド南部
>    - 東日本
>    - 西日本
>    - 東南アジア
>    - 英国南部
>    - 英国西部
>    - フランス中部
>    - 韓国中部
>    - 韓国南部
>- CRR は、任意の GRS コンテナーのためのコンテナー レベルのオプトイン機能です (既定ではオフになっています)。
>- この機能で使用するサブスクリプションをオンボードするには、次のコマンドを使用してください。<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- 制限付きのパブリック プレビュー中にこの機能に対して利用開始した場合、レビューの承認メールに価格ポリシーの詳細が含まれます。
>- オプトイン後にセカンダリ リージョンでバックアップ項目が利用可能になるまでに、最大 48 時間かかることがあります。
>- 現在 CRR は、バックアップ管理の種類「ARM Azure VM」でのみサポートされています (クラシック Azure VM はサポートされません)。  追加の管理の種類が CRR をサポートすると、それらは自動的に**登録**されます。

### <a name="configure-cross-region-restore"></a>リージョンをまたがる復元の構成

GRS 冗長性を使用して作成されたコンテナーには、リージョンをまたがる復元機能を構成するためのオプションが含まれています。 すべての GRS コンテナーにはバナーがあり、それはドキュメントにリンクされます。 コンテナーの CRR を構成するには、[バックアップ構成] ブレードにアクセスします。ここには、この機能を有効にするオプションが含まれています。

 ![バックアップ構成のバナー](./media/backup-azure-arm-restore-vms/banner.png)

1. ポータルから [Recovery Services コンテナー] > [設定] > [プロパティ] にアクセスします。
2. **[Enable Cross Region Restore in this vault]\(このコンテナーでリージョンをまたがる復元を有効にする\)** をクリックして、機能を有効にします。

   ![[Enable Cross Region Restore in this vault]\(このコンテナーでリージョンをまたがる復元を有効にする\) をクリックする前](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![[Enable Cross Region Restore in this vault]\(このコンテナーでリージョンをまたがる復元を有効にする\) をクリックした後](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

[セカンダリ リージョンのバックアップ項目を表示する](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)方法について学習します。

[セカンダリ リージョンで復元する](backup-azure-arm-restore-vms.md#restore-in-secondary-region)方法について学習します。

[セカンダリ リージョンの復元ジョブを監視する](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)方法について説明します。

## <a name="modifying-default-settings"></a>既定の設定を変更する

コンテナーでバックアップを構成する前に、 **[ストレージ レプリケーションの種類]** と **[セキュリティ設定]** の既定の設定を確認することを強くお勧めします。

- 既定では、 **[ストレージ レプリケーションの種類]** は **[Geo-redundant]\(geo 冗長\)** に設定されます。 バックアップを構成すると、変更オプションは無効になります。 この[手順](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)に従って、設定を確認、変更します。

- 誤った削除や悪意のある削除からバックアップ データを保護するために、新しく作成されたコンテナー上で **[論理的な削除]** は既定で **[Enabled]\(有効\)** になっています。 この[手順](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete)に従って、設定を確認、変更します。

## <a name="next-steps"></a>次のステップ

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。
Recovery Services コンテナーの削除の[詳細情報](backup-azure-delete-vault.md)。
