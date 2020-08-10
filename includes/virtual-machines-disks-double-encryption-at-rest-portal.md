---
title: ファイルを含める
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177036"
---
## <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>作業の開始

1. [Azure portal](https://aka.ms/diskencryptionupdates) にサインインします。

    > [!IMPORTANT]
    > [提供されたリンク](https://aka.ms/diskencryptionupdates)を使用して Azure portal にアクセスする必要があります。 現時点では、このリンクを使用しないと、パブリックの Azure portal には保存時の二重暗号化が表示されません。

1. **[ディスク暗号化セット]** を検索して選択します。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="主な Azure portal のスクリーンショット。検索バーでは [ディスク暗号化セット] が強調表示されています。":::

1. **[+ 追加]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="[ディスク暗号化セット] ブレードのスクリーンショット。[+ 追加] が強調表示されています。":::

1. サポートされているリージョンの 1 つを選択します。
1. **[暗号化の種類]** で、 **[Double encryption with platform-managed and customer-managed keys] (プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化)** を選択します。

    > [!NOTE]
    > いったん特定の種類の暗号化を使用してディスク暗号化セットを作成すると、その種類は変更できません。 別の種類の暗号化を使用する場合は、新しいディスク暗号化セットを作成する必要があります。

1. 残りの情報を入力します。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="ディスク暗号化セットの作成ブレードのスクリーンショット。リージョンと、プラットフォーム マネージド キーおよびカスタマー マネージド キーを使用した二重暗号化が強調表示されています。":::

1. Azure Key Vault とキーを選択するか、必要に応じて新しいものを作成します。

    > [!NOTE]
    > Key Vault インスタンスを作成する場合は、論理的な削除と消去保護を有効にする必要があります。 これらの設定は、マネージド ディスクの暗号化のために Key Vault を使用する場合は必須であり、誤った削除によるデータ損失の防止になります。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Key Vault 作成ブレードのスクリーンショット。":::

1. **［作成］** を選択します
1. 作成したディスク暗号化セットに移動し、表示されるエラーを選択します。 これにより、ディスク暗号化セットが機能するように構成されます。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="表示されたディスク暗号化セットのエラーのスクリーンショット。エラー テキストは次のとおりです:このディスク暗号化セットにディスク、イメージ、またはスナップショットを関連付けるには、キー コンテナーにアクセス許可を付与する必要があります。":::

    通知がポップアップ表示され、成功します。 これで、キー コンテナーでディスク暗号化セットを使用できるようになります。
    
    ![キー コンテナーに対するアクセス許可の付与とロールの割り当てが成功したことを示すスクリーンショット。](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. ディスクに移動します。
1. **[暗号化]** を選択します。
1. **[暗号化の種類]** で、 **[Double encryption with platform-managed and customer-managed keys] (プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化)** を選択します。
1. 目的のディスク暗号化セットを選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="マネージド ディスクの [暗号化] ブレードのスクリーンショット。前記の暗号化の種類が強調表示されています。":::

これで、マネージド ディスクで保存時の二重暗号化を有効にしました。
