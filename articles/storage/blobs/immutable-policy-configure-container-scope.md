---
title: コンテナーの不変性ポリシーを構成する
titleSuffix: Azure Storage
description: コンテナーにスコープ設定されている不変性ポリシーを構成する方法について説明します。 不変ポリシーでは、データを消去不可、変更不可の状態で格納することによって、Blob Storage の WORM (Write Once, Read Many) のサポートが提供されます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a28699af7167bfbc6ffd9a00d64b4fbfd593693
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389382"
---
# <a name="configure-immutability-policies-for-containers"></a>コンテナーの不変性ポリシーを構成する

Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータを WORM (Write Once, Read Many) 状態で保存できます。 WORM の状態では、ユーザーが指定した期間、データを変更および削除できません。 BLOB データに不変ポリシーを構成することにより、上書きや削除からデータを保護することができます。 不変性ポリシーには、時間ベースの保持ポリシーと訴訟ホールドが含まれています。 Blob Storage の不変ポリシーの詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)」を参照してください。

不変ポリシーは、個々の BLOB バージョン (プレビュー) またはコンテナーのいずれかにスコープ設定できます。 この記事では、コンテナーレベルの不変性ポリシーを構成する方法について説明します。 バージョンレベルの不変性ポリシーを構成する方法については、「[BLOB バージョンに対する不変性ポリシーを構成する (プレビュー)](immutable-policy-configure-version-scope.md)」を参照してください。

## <a name="configure-a-retention-policy-on-a-container"></a>コンテナーの保持ポリシーを構成する

コンテナーの時間ベースの保持ポリシーを構成するには、Azure portal、PowerShell、または Azure CLI を使用します。 コンテナー レベルの保持ポリシーは、1 ～ 146,000 日の範囲で構成できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して時間ベースの保持ポリシーをコンテナーに対して構成するには、次の手順に従います。

1. 目的のコンテナーに移動します。
1. 右側の **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[Immutable blob storage]\(不変 BLOB ストレージ\)** セクションで、 **[ポリシーの追加]** を選択します。
1. **[ポリシーの種類]** フィールドで、 **[Time-based retention]\(時間ベースの保持\)** を選択し、保持期間を日単位で指定します。
1. コンテナー スコープでポリシーを作成するには、 **[Enable version-level immutability]\(バージョンレベルの不変性を有効にする\)** チェック ボックスをオフにします。
1. 必要に応じて、 **[その他の保護された追加を許可する]** を選択して、不変ポリシーによって保護されている BLOB を追加する書き込みを有効にします。 詳細については、「[保護された追加 BLOB の書き込みを許可する](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)」を参照してください。

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="コンテナーにスコープ設定されている不変性ポリシーの構成方法を示すスクリーンショット":::

不変性ポリシーを構成したら、それがコンテナーにスコープ設定されていることを確認します。

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="コンテナーにスコープ設定されている既存の不変性ポリシーを示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して時間ベースの保持ポリシーをコンテナーに対して構成するには、[Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出して、保持間隔を日単位で指定します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して時間ベースの保持ポリシーをコンテナーに対して構成するには、[az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) コマンドを呼び出して、保持間隔を日単位で指定します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>ロック解除された保持ポリシーを変更する

ロック解除された時間ベースの保持ポリシーを変更して、保持間隔を短縮または延長し、コンテナー内の追加 BLOB への追加の書き込みを許可することができます。 また、ロック解除されたポリシーを削除することもできます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ロック解除された時間ベースの保持ポリシーを Azure portal で変更するには、次の手順に従います。

1. 目的のコンテナーに移動します。
1. **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、既存のロック解除されたポリシーを見つけます。 **[その他]** ボタンを選択し、メニューから **[編集]** を選択します。
1. ポリシーの新しい保持間隔を指定します。 **[Allow additional protected appends]\(保護された追加をさらに許可する\)** を選択して、保護された追加 BLOB への書き込みを許可することもできます。

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="ロック解除された時間ベースの保持ポリシーの変更方法を示すスクリーンショット":::

ロック解除されたポリシーを削除するには、 **[詳細]** ボタン、 **[削除]** の順に選択します。

> [!NOTE]
> [Enable version-level immutability] (バージョンレベルの不変性を有効にする) チェック ボックスをオンにすると、バージョンレベルの不変性ポリシー (プレビュー) を有効化できます。 バージョンレベルの不変性ポリシーの有効化について、詳しくは「[BLOB バージョンに対する不変性ポリシーを構成する (プレビュー)](immutable-policy-configure-version-scope.md)」を参照してください。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ロック解除されたポリシーを変更するには、まず、[Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出してポリシーを取得します。 次に、[Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出してポリシーを更新します。 日単位の新しい保持間隔と `-ExtendPolicy` パラメーターを含めます。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

ロック解除されたポリシーを削除するには、[Remove-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出します。

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ロック解除された時間ベースの保持ポリシーを変更するには、[az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) コマンドを呼び出して、新しい保持間隔を日単位で指定します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy extend \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true
```

ロック解除されたポリシーを削除するには、[az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete) コマンドを呼び出します。

---

## <a name="lock-a-time-based-retention-policy"></a>時間ベースの保持ポリシーをロックする

時間ベースの保持ポリシーのテストが完了したら、そのポリシーをロックできます。 ロックされたポリシーは、SEC 17a-4(f) および他の規制コンプライアンスに準拠しています。 ロックされたポリシーの保持間隔は最大 5 倍まで延長できますが、短縮することはできません。

ポリシーはロックされると、削除できません。 ただし、保持間隔の有効期限が切れた後は BLOB を削除できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でポリシーをロックするには、次の手順を実行します。

1. ロック解除されたポリシーがあるコンテナーにアクセスします。
1. **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、既存のロック解除されたポリシーを見つけます。 **[詳細]** ボタンを選択し、メニューから **[ポリシーのロック]** を選択します。
1. ポリシーをロックすることを確認します。

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="Azure portal で時間ベースの保持ポリシーをロックする方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してポリシーをロックするには、まず、[Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出してポリシーの ETag を取得します。 次に、[Lock-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出して ETag の値を渡すことで、ポリシーをロックします。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してポリシーをロックするには、まず、[az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) コマンドを呼び出してポリシーの ETag を取得します。 次に、[az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) コマンドを呼び出して ETag の値を渡すことで、ポリシーをロックします。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy lock \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>訴訟ホールドを構成またはクリアする

訴訟ホールドは、訴訟ホールドが明示的にクリアされるまで、不変データを格納します。 訴訟ホールド ポリシーの詳細については、「[不変 BLOB データの訴訟ホールド](immutable-legal-hold-overview.md)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してコンテナーの訴訟ホールドを構成するには、次の手順に従います。

1. 目的のコンテナーに移動します。
1. **[詳細]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、 **[ポリシーの追加]** を選択します。
1. ポリシーの種類として **[訴訟ホールド]** を選択して、 **[OK]** を選択します。

次の図は、時間ベースの保持ポリシーと訴訟ホールドの両方が構成されたコンテナーを示しています。

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="時間ベースの保持ポリシーと訴訟ホールドの両方が構成されたコンテナーを示すスクリーンショット":::

訴訟ホールドをクリアするには、 **[アクセス ポリシー]** ダイアログに移動し、 **[詳細]** ボタンを選択して、 **[削除]** を選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してコンテナーの訴訟ホールドを構成するには、[Add-AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold) コマンドを呼び出します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

訴訟ホールドを解除するには、[Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold) コマンドを呼び出します。

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell を使用してコンテナーの訴訟ホールドを構成するには、[az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set) コマンドを呼び出します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli
az storage container legal-hold set \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

訴訟ホールドをクリアするには、[az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear) コマンドを呼び出します。

```azurecli
az storage container legal-hold clear \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>次のステップ

- [不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)
- [不変 BLOB データに対する時間ベースの保持ポリシー](immutable-time-based-retention-policy-overview.md)
- [不変 BLOB データに対する法的な保持](immutable-legal-hold-overview.md)
- [BLOB バージョンに対する不変性ポリシーを構成する (プレビュー)](immutable-policy-configure-version-scope.md)
