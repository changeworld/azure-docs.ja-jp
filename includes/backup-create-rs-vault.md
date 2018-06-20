---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664957"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。
2. 左側のメニューで、**[すべてのサービス]** を選択します。

    ![メイン メニューで、[すべてのサービス] オプションを選択する](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. [すべてのサービス] ダイアログ ボックスに「*Recovery Services*」と入力します。 入力を開始すると、リソースのリストが絞り込まれます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

    ![[すべてのサービス] ダイアログに「Recovery Services」と入力する](./media/backup-create-rs-vault/all-services.png) <br/>

    サブスクリプションに Recovery Services コンテナーの一覧が表示されます。
4. **[Recovery Services コンテナー]** メニューの **[追加]** を選択します。

    ![Create Recovery Services Vault step 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    **[Recovery Services コンテナー]** メニューが開きます。 **[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** の情報を入力するように求められます。

    ![[Recovery Services コンテナー] ウィンドウ](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
6. **[サブスクリプション]** で、使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
7. **[リソース グループ]** で、既存のリソース グループを使用するか、新しいリソース グループを作成することができます。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、**[既存のものを使用]** を選択し、ドロップダウン メニューをクリックします。 新しいリソース グループを作成するには、**[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md)」を参照してください。
8. **[場所]** で、コンテナーのリージョンを選択します。 仮想マシンを保護するためのコンテナーを作成する場合、コンテナーは仮想マシンと同じリージョンにある*必要があります*。

   > [!IMPORTANT]
   > VM がどの場所に存在するかが不明な場合は、コンテナーを作成するダイアログ ボックスを閉じて、ポータルで仮想マシンの一覧に移動します。 複数のリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成します。 最初の場所でコンテナーを作成してから、次の場所に移動してください。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup サービスで自動的に処理されます。
   >
   >

9. Recovery Services コンテナーを作成する準備ができたら、**[作成]** をクリックします。

    ![バックアップ コンテナーの一覧](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services コンテナーの作成に時間がかかることがあります。 [通知] セクション (ポータルの右上の領域) で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、**[最新の情報に更新]** をクリックします。

     ![バックアップ コンテナーの一覧](./media/backup-create-rs-vault/refresh-button.png)
