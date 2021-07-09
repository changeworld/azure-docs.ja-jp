---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/01/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 7d354ce3e69f075d2f523a75ba96f9a6efb7d797
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783579"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、時間の経過と共に作成された復旧ポイントを格納する管理エンティティであり、バックアップ関連の操作を実行するためのインターフェイスが用意されています。 たとえば、オンデマンドのバックアップの作成、復元の実行、バックアップ ポリシーの作成などの操作です。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

1. Azure portal で **バックアップ センター** を検索し、 **[バックアップ センター]** ダッシュボードにナビゲートします。

    ![バックアップ センターを選択する](../includes/media/backup-create-rs-vault/backup-center-search-backup-center.png)

1. **[概要]** タブから **[+Vault]\(+コンテナー\)** を選択します。

    ![コンテナーの作成](./media/backup-create-rs-vault/backup-center-create-vault.png) 

1. **[Recovery Services コンテナー]** を選択し、 **[続行]** をクリックします。

    ![Recovery Services コンテナーの選択](./media/backup-create-rs-vault/backup-center-select-recovery-services-vault.png) 

1. **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。 **[名前]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** に値を入力します。

    ![[Recovery Services コンテナー] を構成する](./media/backup-create-rs-vault/backup-center-add-vault-details.png)

   - **Name**:コンテナーを識別するフレンドリ名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
   - **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
   - **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リストからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/management/overview.md)」を参照してください。
   - **[場所]** :コンテナーの地理的リージョンを選択します。 任意のデータ ソースを保護するためのコンテナーを作成するには、コンテナーがデータ ソースと同じリージョンにある "*必要があります*"。

      > [!IMPORTANT]
      > データ ソースの場所がわからない場合は、ダイアログ ボックスを閉じます。 ポータルの自分のリソースの一覧に移動します。 複数のリージョンにデータ ソースがある場合は、リージョンごとに Recovery Services コンテナーを作成します。 まず最初の場所にコンテナーを作成してから、別の場所にコンテナーを作成します。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup で自動的に処理されます。
      >
      >

1. 値を指定したら、 **[確認と作成]** を選択します。

    ![Recovery Services コンテナーを作成するプロセスにおける [確認と作成] ボタンのスクリーンショット。](./media/backup-create-rs-vault/review-and-create.png)

1. Recovery Services コンテナーを作成する準備ができたら、 **[作成]** を選択します。

    ![[Recovery Services コンテナー] を作成する](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services コンテナーの作成に時間がかかることがあります。 **[通知]** 領域 (ポータルの右上隅) で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、 **[最新の情報に更新]** を選択します。

     ![バックアップ コンテナーの一覧を更新する](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> コンテナーでバックアップを構成する前に、 **[ストレージ レプリケーションの種類]** と **[セキュリティ設定]** の既定の設定を確認することを強くお勧めします。 詳細については、「[ストレージ冗長性の設定](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy)」セクションを参照してください。