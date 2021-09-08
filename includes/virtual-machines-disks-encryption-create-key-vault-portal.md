---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a8a503f5357ec69a1bab007f4ae2849582fed3b
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835595"
---
ディスクにカスタマー マネージド キーを設定する作業を初めて実行するときは、特定の順序でリソースを作成する必要があります。 最初に、Azure キー コンテナーの作成と設定を行う必要があります。

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault のセットアップ

1. [Azure Portal](https://aka.ms/diskencryptionupdates) にサインインします。
1. **[キー コンテナー]** を検索して選択します。

    [![検索ダイアログ ボックスが展開されている Azure portal のスクリーンショット。](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > デプロイを成功させるには、Azure キー コンテナー、ディスク暗号化セット、VM、ディスク、スナップショットがすべて同じリージョンとサブスクリプションに存在している必要があります。

1. **[+ 作成]** を選択して、新しいキー コンテナーを作成します。
1. 新しいリソース グループを作成します。
1. キー コンテナー名を入力し、リージョンを選択して、価格レベルを選択します。

    > [!NOTE]
    > Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

1. **[確認および作成]** を選択し、選択内容を確認してから、 **[作成]** を選択します。

    ![Azure キー コンテナーの作成エクスペリエンスのスクリーンショット。 作成する個々の値が表示された状態](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. キー コンテナーのデプロイが完了したら、それを選択します。
1. **[設定]** で **[キー]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。

    ![Key Vault のリソース設定ペインのスクリーンショット。 設定内で [生成/インポート] ボタンが表示されています。](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. **[キーの種類]** は **[RSA]** 、 **[RSA キー サイズ]** は **[2048]** に設定されているので、どちらもそのままにしておきます。
1. 必要に応じて残りの選択項目を入力したら、 **[作成]** を選択します。

    ![[生成/インポート] ボタンを選択すると表示される [Create a key]\(キーを作成します\) ペインのスクリーンショット](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>Azure RBAC ロールを追加する

Azure キー コンテナーとキーを作成したら、Azure RBAC ロールを追加して、ディスク暗号化セットで Azure キー コンテナーを使用できるようにする必要があります。

1. **[アクセス制御 (IAM)]** を選択し、ロールを追加します。
1. **Key Vault Administrator\(キー コンテナー管理者\)** 、**所有者**、または **共同作成者** のいずれかのロールを追加します。

## <a name="set-up-your-disk-encryption-set"></a>ディスク暗号化セットを設定する

1. **ディスク暗号化セット** を検索して選択します。
1. **[ディスク暗号化セット]** ペインで、 **[+ 作成]** を選択します。
1. リソース グループを選択し、暗号化セットに名前を付け、キー コンテナーと同じリージョンを選択します。
1. **[SSE Encryption type (SSE 暗号化の種類)]** で、 **[Encryption at-rest with a customer-managed key (カスタマー マネージド キーを使用した保存時の暗号化)]** を選択します。

    > [!NOTE]
    > 特定の種類の暗号化を使用してディスク暗号化セットを作成すると、そのセットを変更することはできません。 別の種類の暗号化を使用したい場合は、新しいディスク暗号化セットを作成する必要があります。

1. **[クリックしてキーを選択します]** を選択します。
1. 以前に作成したキー コンテナーとキー、およびバージョンを選択します。
1. **[選択]** を選択します。
1. [カスタマー マネージド キーの自動ローテーション](../articles/virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)を有効にする場合は、 **[Auto key rotation (自動キー ローテーション)]** を選択します。
1. **[確認および作成]** 、 **[作成]** の順に選択します。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png" alt-text="ディスク暗号化作成ペインのスクリーンショット。サブスクリプション、リソース グループ、ディスク暗号化セット名、リージョン、キー コンテナーとキーのセレクターが表示されています。" lightbox="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png":::
