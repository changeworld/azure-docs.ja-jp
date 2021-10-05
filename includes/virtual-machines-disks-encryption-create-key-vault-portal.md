---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de89ce807b69cf9d9ef7db180b32dd1b7fe1d50c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091187"
---
ディスクにカスタマー マネージド キーを設定する作業を初めて実行するときは、特定の順序でリソースを作成する必要があります。 最初に、Azure キー コンテナーの作成と設定を行う必要があります。

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault のセットアップ

1. <bpt id="p1">[</bpt>Azure Portal<ept id="p1">](https://aka.ms/diskencryptionupdates)</ept> にサインインします。
1. <bpt id="p1">**</bpt>[キー コンテナー]<ept id="p1">**</ept> を検索して選択します。

    <bpt id="p1">[</bpt><ph id="ph1">![</ph>検索ダイアログ ボックスが展開されている Azure portal のスクリーンショット。<ept id="p1">](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)</ept>

    > [!IMPORTANT]
    > デプロイを成功させるには、Azure キー コンテナー、ディスク暗号化セット、VM、ディスク、スナップショットがすべて同じリージョンとサブスクリプションに存在している必要があります。

1. <bpt id="p1">**</bpt>[+ 作成]<ept id="p1">**</ept> を選択して、新しいキー コンテナーを作成します。
1. 新しいリソース グループを作成します。
1. キー コンテナー名を入力し、リージョンを選択して、価格レベルを選択します。

    > [!NOTE]
    > Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

1. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> を選択し、選択内容を確認してから、 <bpt id="p2">**</bpt>[作成]<ept id="p2">**</ept> を選択します。

    ![Azure キー コンテナーの作成エクスペリエンスのスクリーンショット。 作成する個々の値が表示された状態](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. キー コンテナーのデプロイが完了したら、それを選択します。
1. <bpt id="p1">**</bpt>[設定]<ept id="p1">**</ept> で <bpt id="p2">**</bpt>[キー]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[Generate/Import]\(生成/インポート\)<ept id="p1">**</ept> を選択します。

    ![Key Vault のリソース設定ペインのスクリーンショット。 設定内で [生成/インポート] ボタンが表示されています。](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. <bpt id="p1">**</bpt>[キーの種類]<ept id="p1">**</ept> は <bpt id="p2">**</bpt>[RSA]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[RSA キー サイズ]<ept id="p3">**</ept> は <bpt id="p4">**</bpt>[2048]<ept id="p4">**</ept> に設定されているので、どちらもそのままにしておきます。
1. 必要に応じて残りの選択項目を入力したら、 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択します。

    ![[生成/インポート] ボタンを選択すると表示される [Create a key]\(キーを作成します\) ペインのスクリーンショット](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>Azure RBAC ロールを追加する

Azure キー コンテナーとキーを作成したら、Azure RBAC ロールを追加して、ディスク暗号化セットで Azure キー コンテナーを使用できるようにする必要があります。

1. <bpt id="p1">**</bpt>[アクセス制御 (IAM)]<ept id="p1">**</ept> を選択し、ロールを追加します。
1. <bpt id="p1">**</bpt>Key Vault Administrator\(キー コンテナー管理者\)<ept id="p1">**</ept> 、<bpt id="p2">**</bpt>所有者<ept id="p2">**</ept>、または <bpt id="p3">**</bpt>共同作成者<ept id="p3">**</ept> のいずれかのロールを追加します。

## <a name="set-up-your-disk-encryption-set"></a>ディスク暗号化セットを設定する

1. <bpt id="p1">**</bpt>ディスク暗号化セット<ept id="p1">**</ept>を検索して選択します。
1. <bpt id="p1">**</bpt>[ディスク暗号化セット]<ept id="p1">**</ept> ペインで、 <bpt id="p2">**</bpt>[+ 作成]<ept id="p2">**</ept> を選択します。
1. リソース グループを選択し、暗号化セットに名前を付け、キー コンテナーと同じリージョンを選択します。
1. <bpt id="p1">**</bpt>[SSE Encryption type (SSE 暗号化の種類)]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[Encryption at-rest with a customer-managed key (カスタマー マネージド キーを使用した保存時の暗号化)]<ept id="p2">**</ept> を選択します。

    > [!NOTE]
    > 特定の種類の暗号化を使用してディスク暗号化セットを作成すると、そのセットを変更することはできません。 別の種類の暗号化を使用したい場合は、新しいディスク暗号化セットを作成する必要があります。

1. <bpt id="p1">**</bpt>[クリックしてキーを選択します]<ept id="p1">**</ept> を選択します。
1. 以前に作成したキー コンテナーとキー、およびバージョンを選択します。
1. <bpt id="p1">**</bpt>[選択]<ept id="p1">**</ept> を選択します。
1. <bpt id="p1">[</bpt>カスタマー マネージド キーの自動ローテーション<ept id="p1">](../articles/virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)</ept>を有効にする場合は、 <bpt id="p2">**</bpt>[Auto key rotation (自動キー ローテーション)]<ept id="p2">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> 、 <bpt id="p2">**</bpt>[作成]<ept id="p2">**</ept> の順に選択します。

    <bpt id="p1">:::image type="content" source="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png" alt-text="</bpt>ディスク暗号化作成ペインのスクリーンショット。サブスクリプション、リソース グループ、ディスク暗号化セット名、リージョン、キー コンテナーとキーのセレクターが表示されています。<ept id=&quot;p1&quot;>" lightbox="media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png":::</ept>

1. デプロイされたディスク暗号化セットに移動し、表示されたアラートを選択します。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-alert.png" alt-text="ユーザーが [このディスク暗号化セットにディスク、イメージ、またはスナップショットを関連付けるには、キー コンテナーにアクセス許可を付与する必要があります] アラートを選択するスクリーンショット。" lightbox="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-alert.png":::

1. これにより、キー コンテナーのアクセス許可がディスク暗号化セットに付与されます。

    :::image type="content" source="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-confirmation.png" alt-text="アクセス許可が付与されたことを確認するスクリーンショット。" lightbox="media/virtual-machines-disk-encryption-portal/disk-encryption-set-perm-confirmation.png":::