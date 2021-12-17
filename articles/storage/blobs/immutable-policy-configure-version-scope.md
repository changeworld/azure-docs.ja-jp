---
title: BLOB バージョンに不変ポリシーを構成する (プレビュー)
titleSuffix: Azure Storage
description: BLOB バージョンにスコープ設定されている不変ポリシーを構成する方法について説明します (プレビュー)。 不変ポリシーでは、データを消去不可、変更不可の状態で格納することによって、Blob Storage の WORM (Write Once, Read Many) のサポートが提供されます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/10/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7a7ded3df993034963f06b81a0908e68821688cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584296"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>BLOB バージョンに不変ポリシーを構成する (プレビュー)

Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータを WORM (Write Once, Read Many) 状態で保存できます。 WORM の状態では、ユーザーが指定した期間、データを変更および削除できません。 BLOB データに不変ポリシーを構成することにより、上書きや削除からデータを保護することができます。 不変性ポリシーには、時間ベースの保持ポリシーと訴訟ホールドが含まれています。 Blob Storage の不変性ポリシーの詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)」を参照してください。

不変ポリシーは、個々の BLOB バージョン (プレビュー) またはコンテナーのいずれかにスコープ設定できます。 この記事では、バージョン レベルの不変ポリシーを構成する方法について説明します。 コンテナー レベルの不変ポリシーを構成する方法については、「[コンテナーの不変ポリシーを構成する](immutable-policy-configure-container-scope.md)」を参照してください。

バージョン レベルの不変ポリシーの構成は、2 段階のプロセスです。

1. 最初に、新規または既存のコンテナーでバージョン レベルの不変性のサポートを有効にします。 詳細については、「[コンテナーでバージョン レベルの不変性のサポートを有効にする](#enable-support-for-version-level-immutability-on-a-container)」を参照してください。
1. 次に、そのコンテナー内の 1 つ以上の BLOB バージョンに適用される時間ベースの保持ポリシーまたは訴訟ホールドを構成します。

> [!IMPORTANT]
> バージョン レベルの不変ポリシーは、現在、**プレビュー** の段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

バージョン レベルの時間ベースの保持ポリシーを構成するには、そのストレージ アカウントに対して BLOB のバージョン管理を有効にする必要があります。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。

バージョン レベルの不変ポリシーでサポートされているストレージ アカウント構成の詳細については、「[サポートされているアカウント構成](immutable-storage-overview.md#supported-account-configurations)」を参照してください。

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>コンテナーでバージョン レベルの不変性のサポートを有効にする

時間ベースの保持ポリシーを BLOB バージョンに適用するには、事前にバージョン レベルの不変性のサポートを有効にする必要があります。 バージョン レベルの不変性をサポートするように、新規と既存の両方のコンテナーを構成できます。 ただし、既存のコンテナーでは、サポートを有効にするために、移行プロセスを実行する必要があります。

コンテナーに対してバージョン レベルの不変性のサポートを有効にしても、そのコンテナー内のデータは不変にはならないことに注意してください。 また、コンテナーに対する既定の不変ポリシーまたは特定の BLOB バージョンに対する不変ポリシーのいずれかを構成する必要があります。

### <a name="enable-version-level-immutability-for-a-new-container"></a>新しいコンテナーに対してバージョン レベルの不変性を有効にする

バージョン レベルの不変ポリシーを使用するには、まず、コンテナーに対してバージョン レベルの WORM のサポートを明示的に有効にする必要があります。 バージョン レベルの WORM のサポートは、コンテナーを作成するとき、または既存のコンテナーにバージョン レベルの不変ポリシーを追加するときに有効にできます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でバージョン レベルの不変性をサポートするコンテナーを作成するには、次の手順に従います。

1. Azure portal でご自分のストレージ アカウントの **[コンテナー]** ページに移動し、 **[追加]** を選択します。
1. **[新しいコンテナー]** ダイアログで、コンテナーの名前を指定し、 **[詳細設定]** セクションを展開します。
1. **[Enable version-level immutability support]\(バージョン レベルの不変性のサポートを有効にする\)** を選択して、そのコンテナーにに対してバージョン レベルの不変性を有効にします。

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="バージョン レベルの不変性を有効にしてコンテナーを作成する方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

バージョン レベルの不変性をサポートするコンテナーを PowerShell で作成するには、まず [Az.Storage モジュール](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview) (バージョン 3.10.1-preview) をインストールします。

次に、次の例に示すように、`-EnableImmutableStorageWithVersioning` パラメーターを指定して **New-AzRmStorageContainer** コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
# Create a container with version-level immutability support.
$container = New-AzRmStorageContainer -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -EnableImmutableStorageWithVersioning

# Verify that version-level immutability support is enabled for the container
$container.ImmutableStorageWithVersioning
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

バージョン レベルの不変性をサポートするコンテナーを Azure CLI で作成するには、まず Azure CLI (バージョン 2.27 以降) をインストールします。 Azure CLI のインストールに関する詳細については、「[Azure CLI をインストールする方法](/cli/azure/install-azure-cli)」を参照してください。

次に、`--enable-vlw` パラメータを指定して、[az storage container-rm create](/cli/azure/storage/container-rm#az_storage_container_rm_create) コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
# Create a container with version-level immutability support.
az storage container-rm create \
    --name <container> \
    --storage-account <storage-account> \
    --resource-group <resource-group> \
    --enable-vlw

# Verify that version-level immutability support is enabled for the container
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.enabled]' \
    --output tsv
```

---

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>バージョン レベルの不変性をサポートするために既存のコンテナーを移行する

既存のコンテナーにバージョン レベルの不変ポリシーを構成するには、バージョン レベルの不変ストレージをサポートするためにそのコンテナーを移行する必要があります。 コンテナーの移行には時間がかかる場合があり、元に戻すことはできません。

バージョン レベルの不変性ポリシーをサポートするために既存のコンテナーを移行するには、そのコンテナーにコンテナーレベルの時間ベースの保持ポリシーが構成されている必要があります。 コンテナーに既存のポリシーがない場合、移行は失敗します。 コンテナーレベル ポリシーの保持間隔は、コンテナー上の既定のバージョンレベル ポリシーの保持間隔として維持されます。

コンテナーに既存のコンテナー レベルの訴訟ホールドがある場合は、訴訟ホールドが削除されるまで移行できません。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でバージョン レベルの不変ストレージをサポートするためにコンテナーを移行するには、次の手順に従います。

1. 目的のコンテナーに移動します。
1. 右側の **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. 次に、 **[不変 BLOB ストレージ]** の下で **[ポリシーの追加]** を選択します。
1. **[ポリシーの種類]** フィールドで、 *[時間ベースの保持]* を選択し、保持間隔を指定します。
1. **[Enable version-level immutability]\(バージョン レベルの不変性を有効にする\)** を選択します。
1. **[OK]** を選択すると、指定された保持間隔でコンテナー レベルのポリシーが作成され、バージョン レベルの不変性サポートへの移行が開始されます。

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="バージョン レベルの不変性をサポートするための既存のコンテナーの移行方法を示すスクリーンショット":::

移行操作が進行中の間、コンテナーのポリシーのスコープは "*コンテナー*" と表示されます。

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-in-process.png" alt-text="進行中のコンテナーの移行を示すスクリーンショット":::

移行が完了すると、コンテナー上のポリシーのスコープは "*バージョン*" と表示されます。 表示されているポリシーは、コンテナーの既定のポリシーで、以降コンテナー内で作成されたすべての BLOB のバージョンに自動的に適用されます。 既定のポリシーは、そのバージョンのカスタム ポリシーを指定することで、任意のバージョンでオーバーライドできます。

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-complete.png" alt-text="完了したコンテナーの移行を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

バージョン レベルの不変ストレージをサポートするために PowerShell を使用してコンテナーを移行するには、まず、コンテナーに対してコンテナー レベルの時間ベースの保持ポリシーが存在することを確認します。 これを作成するには、[Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) を呼び出します。

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days>
```

次に、**Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration** コマンドを呼び出して、コンテナーを移行します。 コマンドを非同期的に実行するには、`-AsJob` パラメーターを含めます。 移行の完了には時間がかかる場合があるため、非同期に実行することをお勧めします。

```azurepowershell
$migrationOperation = Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration `
    -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -AsJob
```

実行時間の長い操作の状態を確認するには、操作の **JobStateInfo.State** プロパティを読みます。

```azurepowershell
$migrationOperation.JobStateInfo.State
```

バージョン レベルの不変性に移行しようとしたときに、コンテナーに既存の時間ベースの保持ポリシーが存在していない場合、操作は失敗します。 次の例では、**JobStateInfo.State** プロパティの値をチェックし、コンテナー レベルのポリシーが存在しないことが原因で操作が失敗した場合は、エラー メッセージを表示します。

```azurepowershell
if ($migrationOperation.JobStateInfo.State -eq "Failed") {
Write-Host $migrationOperation.Error
}
The container <container-name> must have an immutability policy set as a default policy
before initiating container migration to support object level immutability with versioning.
```

移行が完了したら、操作の **Output** プロパティを確認して、バージョン レベルの不変性のサポートが有効になっているか確認します。

```azurepowershell
$migrationOperation.Output
```

PowerShell ジョブの詳細については、[PowerShell ジョブで Azure PowerShell コマンドレットを実行する](/powershell/azure/using-psjobs)方法に関するページを参照してください。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

バージョン レベルの不変ストレージをサポートするために Azure CLI を使用してコンテナーを移行するには、まず、コンテナーに対してコンテナー レベルの時間ベースの保持ポリシーが存在することを確認します。 作成するには、[az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) を呼び出します。

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days>
```

次に、[az storage container-rm migrate-vlw](/cli/azure/storage/container-rm#az_storage_container_rm_migrate_vlw) コマンドを呼び出して、コンテナーを移行します。 コマンドを非同期的に実行するには、`--no-wait` パラメーターを含めます。 移行の完了には時間がかかる場合があるため、非同期に実行することをお勧めします。

```azurecli
az storage container-rm migrate-vlw \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --name <container> \
    --no-wait
```

実行時間の長い操作の状態を確認するには、**migrationState** プロパティの値を読みます。

```azurecli
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.migrationState]' \
    --output tsv
```

---

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>コンテナーに時間ベースの保持ポリシーを構成する

コンテナーに対してバージョン レベルの不変性を有効にしたら、そのコンテナーに既定のバージョン レベルの時間ベースの保持ポリシーを指定できます。 コンテナーの既定のポリシーを指定すると、そのポリシーは、コンテナーで作成された新しい BLOB バージョンに既定で適用されます。 コンテナー内の個々の BLOB バージョンの既定のポリシーは、オーバーライドすることができます。

既定のポリシーが、既定のポリシーが構成される前に存在していた BLOB バージョンに自動的に適用されることはありません。

バージョン レベルの不変性をサポートするために既存のコンテナーを移行した場合、移行前に有効だったコンテナー レベルのポリシーは、コンテナーの既定のバージョン レベルのポリシーに移行されます。

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>コンテナーに既定の時間ベースの保持ポリシーを構成する

コンテナーの既定のバージョン レベルの不変ポリシーを構成するには、Azure portal、PowerShell、Azure CLI、または Azure Storage SDK のいずれかを使用します。 [「コンテナーでバージョン レベルの不変性のサポートを有効にする」](#enable-support-for-version-level-immutability-on-a-container)の説明に従って、コンテナーのバージョン レベルの不変性のサポートを有効にするようにしてください。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で既定のバージョン レベルの不変ポリシーをコンテナーで構成するには、次の手順を実行します。

1. Azure portal で、 **[コンテナー]** ページに移動し、ポリシーを適用するコンテナーを見つけます。
1. コンテナー名の右側にある **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[アクセス ポリシー]** ダイアログの **[不変 BLOB ストレージ]** セクションで、 **[ポリシーの追加]** を選択します。
1. **時間ベースの保持ポリシー** を選択し、保持間隔を指定します。
1. 必要に応じて、 **[その他の保護された追加を許可する]** を選択して、不変ポリシーによって保護されている BLOB を追加する書き込みを有効にします。 詳細については、「[保護された追加 BLOB の書き込みを許可する](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)」を参照してください。
1. **[OK]** を選択して、コンテナーに既定のポリシーを適用します。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="コンテナーに既定のバージョン レベルの保持ポリシーを構成する方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してコンテナーの既定のバージョン レベルの不変ポリシーを構成するには、[Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) コマンドを呼び出します。

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days> `
   -AllowProtectedAppendWrite $true
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してコンテナーの既定のバージョン レベルの不変ポリシーを構成するには、[az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) コマンドを呼び出します。

```azurecli
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

---

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>コンテナーに対する保持ポリシーのスコープを決定する

Azure portal で時間ベースの保持ポリシーのスコープを決定するには、次の手順を実行します。

1. 目的のコンテナーに移動します。
1. 右側の **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[不変 BLOB ストレージ]** の下で **[スコープ]** フィールドを見つけます。 コンテナーに既定のバージョン レベルの保持ポリシーが構成されている場合は、次の図に示すように、スコープが "*バージョン*" に設定されます。

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="コンテナーに構成されている既定のバージョン レベルの保持ポリシーを示すスクリーンショット":::

1. コンテナーにコンテナー レベルの保持ポリシーが構成されている場合は、次の図に示すように、スコープが "*コンテナー*" に設定されます。

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="コンテナーに構成されているコンテナー レベルの保持ポリシーを示すスクリーンショット":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>既存のバージョンに時間ベースの保持ポリシーを構成する

時間ベースの保持ポリシーを使用すると、指定された期間 BLOB データが WORM 状態で保持されます。 時間ベースの保持ポリシーの詳細については、「[不変 BLOB データに対する時間ベースの保持ポリシー](immutable-time-based-retention-policy-overview.md)」を参照してください。

BLOB バージョンに時間ベースの保持ポリシーを構成するには、次の 3 つのオプションがあります。

- オプション 1: コンテナーにスコープ設定され、既定でコンテナー内のすべてのオブジェクトに適用される既定のポリシーを構成できます。 コンテナー内のオブジェクトでは、個々の BLOB バージョンでポリシーを構成することによって明示的に既定のポリシーをオーバーライドしない限り、既定のポリシーが継承されます。 詳細については、「[コンテナーに既定の時間ベースの保持ポリシーを構成する](#configure-a-default-time-based-retention-policy-on-a-container)」を参照してください。
- オプション 2: 現在のバージョンの BLOB でポリシーを構成できます。 このポリシーは、コンテナーに構成されている既定のポリシー (存在していてロック解除されている場合) をオーバーライドできます。 既定では、ポリシーの構成後に作成された以前のバージョンでは、BLOB の現在のバージョンのポリシーが継承されます。 詳細については、「[現在のバージョンの BLOB で保持ポリシーを構成する](#configure-a-retention-policy-on-the-current-version-of-a-blob)」を参照してください。
- オプション 3: 以前のバージョンの BLOB でポリシーを構成できます。 このポリシーは、現在のバージョンに構成されている既定のポリシー (存在していてロック解除されている場合) をオーバーライドできます。 詳細については、「[以前のバージョンの BLOB で保持ポリシーを構成する](#configure-a-retention-policy-on-a-previous-version-of-a-blob)」を参照してください。

BLOB のバージョン管理の詳細については、「[BLOB のバージョン管理](versioning-overview.md)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でコンテナーに移動すると、BLOB の一覧が表示されます。 表示される各 BLOB は、BLOB の現在のバージョンを表します。 以前のバージョンのリストにアクセスするには、BLOB の **[詳細]** ボタンを選択し、 **[以前のバージョンを表示する]** を選択します。

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>現在のバージョンの BLOB で保持ポリシーを構成する

現在のバージョンの BLOB に時間ベースの保持ポリシーを構成するには、次の手順を実行します。

1. ターゲット BLOB が含まれているコンテナーに移動します。
1. BLOB 名の右側にある **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。 以前のバージョンに対して時間ベースの保持ポリシーが既に構成されている場合は、それが **[アクセス ポリシー]** ダイアログに表示されます。
1. **[アクセス ポリシー]** ダイアログの **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、 **[ポリシーの追加]** を選択します。
1. **時間ベースの保持ポリシー** を選択し、保持間隔を指定します。
1. **[OK]** を選択して、このポリシーを現在のバージョンの BLOB に適用します。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="現在のバージョンの BLOB に保持ポリシーを構成する方法を示すスクリーンショット":::

BLOB のプロパティを表示して、現在のバージョンでポリシーが有効になっているかどうかを確認できます。 BLOB を選択し、 **[概要]** タブに移動して、 **[Version-level immutability policy]\(バージョン レベルの不変ポリシー\)** プロパティを見つけます。 ポリシーが有効になっている場合は、 **[保持期間]** プロパティにポリシーの有効期限の日時が表示されます。 ポリシーは、現在のバージョンに対して構成されるか、既定のポリシーが有効な場合は BLOB の親コンテナーから継承される場合があることに注意してください。

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Azure portal で BLOB バージョンの不変ポリシープロパティを示すスクリーンショット":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>以前のバージョンの BLOB で保持ポリシーを構成する

以前のバージョンの BLOB で時間ベースの保持ポリシーを構成することもできます。 以前のバージョンは、変更できないという点で常に不変です。 ただし、以前のバージョンは削除できます。 時間ベースの保持ポリシーが有効な間は、削除から保護されます。

以前のバージョンの BLOB に時間ベースの保持ポリシーを構成するには、次の手順を実行します。

1. ターゲット BLOB が含まれているコンテナーに移動します。
1. BLOB を選択し、 **[バージョン]** タブに移動します。
1. ターゲット バージョンを見つけて、 **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。 以前のバージョンに対して時間ベースの保持ポリシーが既に構成されている場合は、それが **[アクセス ポリシー]** ダイアログに表示されます。
1. **[アクセス ポリシー]** ダイアログの **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、 **[ポリシーの追加]** を選択します。
1. **時間ベースの保持ポリシー** を選択し、保持間隔を指定します。
1. **[OK]** を選択して、このポリシーを現在のバージョンの BLOB に適用します。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Azure portal で以前の BLOB バージョンの保持ポリシーを構成する方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して時間ベースの保持ポリシーを BLOB バージョンに対して構成するには、**Set-AzStorageBlobImmutabilityPolicy** コマンドを呼び出します。

次の例は、現在のバージョンの BLOB でロック解除されたポリシーを構成する方法を示しています。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Context

Set-AzStorageBlobImmutabilityPolicy -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -ExpiresOn "2021-09-01T12:00:00Z" `
    -PolicyMode Unlocked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して BLOB バージョンで時間ベースの保持ポリシーを構成するには、まず、*storage-blob-preview* 拡張機能バージョン 0.6.1 以降をインストールする必要があります。

```azurecli
az extension add --name storage-blob-preview
```

Azure CLI 拡張機能のインストールの詳細については、[Azure CLI 拡張機能をインストールおよび管理する方法](/cli/azure/azure-cli-extensions-overview)に関する記事を参照してください。

次に、**az storage blob immutability-policy set** コマンドを呼び出して、時間ベースの保持ポリシーを構成します。 次の例は、現在のバージョンの BLOB でロック解除されたポリシーを構成する方法を示しています。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-09-20T08:00:00Z \
    --policy-mode Unlocked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>BLOB のアップロード時に時間ベースの保持ポリシーを構成する

Azure portal を使用して、バージョン レベルの不変性をサポートするコンテナーに BLOB をアップロードするときに、新しい BLOB に時間ベースの保持ポリシーを構成するためのオプションがいくつかあります。

- オプション 1: コンテナーに既定の保持ポリシーが構成されている場合は、そのコンテナーのポリシーを使用して BLOB をアップロードできます。 このオプションは、コンテナーに保持ポリシーがある場合に既定で選択されます。
- オプション 2: コンテナーに既定の保持ポリシーが構成されている場合は、新しい BLOB にカスタム保持ポリシーを定義するか、ポリシーを使用せずに BLOB をアップロードすることで、既定のポリシーをオーバーライドできます。
- オプション 3: コンテナーに既定のポリシーが構成されていない場合は、カスタム ポリシーを使用するか、ポリシーを使用せずに BLOB をアップロードできます。

BLOB をアップロードするときに時間ベースの保持ポリシーを構成するには、次の手順に従います。

1. 目的のコンテナーに移動し、 **[アップロード]** を選択します。
1. **[BLOB のアップロード]** ダイアログで **[詳細設定]** セクションを展開します。
1. **[保持ポリシー]** フィールドで、新しい BLOB に時間ベースの保持ポリシーを構成します。 コンテナーに既定のポリシーが構成されている場合、そのポリシーが既定で選択されます。 BLOB にカスタム ポリシーを指定することもできます。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Azure portal で BLOB のアップロード時に保持ポリシーを構成するためのオプションを示すスクリーンショット":::

## <a name="modify-or-delete-an-unlocked-retention-policy"></a>ロック解除された保持ポリシーを削除または変更する

ロック解除された時間ベースの保持ポリシーを変更して、保持間隔を短縮または延長できます。 また、ロック解除されたポリシーを削除することもできます。 ある BLOB バージョンに対するロック解除された時間ベースの保持ポリシーを編集または削除しても、他のバージョンに対して有効なポリシーには影響しません。 コンテナーに対して有効な既定の時間ベースの保持ポリシーがある場合、変更または削除されたポリシーを持つ BLOB バージョンはそのコンテナーから継承されなくなります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ロック解除された時間ベースの保持ポリシーを Azure portal で変更するには、次の手順に従います。

1. ターゲット コンテナーまたはバージョンを見つけます。 **[詳細]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. 既存のロック解除された不変ポリシーを見つけます。 **[その他]** ボタンを選択し、メニューから **[編集]** を選択します。

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Azure portal で既存のバージョン レベルの時間ベースの保持ポリシーを編集する方法を示すスクリーンショット":::

1. ポリシーの有効期限の新しい日時を指定します。

ロック解除されたポリシーを削除するには、 **[詳細]** メニューの **[削除]** を選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してロック解除された時間ベースの保持ポリシーを変更するには、ポリシーの有効期限の新しい日付と時刻を指定して、BLOB バージョンで **Set-AzStorageBlobImmutabilityPolicy** コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
$containerName = "<container>"
$blobName = "<blob>"

# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

# Extend the retention interval by five days.
$blobVersion = $blobVersion |
    Set-AzStorageBlobImmutabilityPolicy -ExpiresOn (Get-Date).AddDays(5) `

# View the new policy parameters.
$blobVersion.BlobProperties.ImmutabilityPolicy
```

ロック解除された保持ポリシーを削除するには、**Remove-AzStorageBlobImmutabilityPolicy** コマンドを呼び出します。

```azurepowershell
$blobVersion = $blobVersion | Remove-AzStorageBlobImmutabilityPolicy
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell を使用してロック解除された時間ベースの保持ポリシーを変更するには、ポリシーの有効期限の新しい日付と時刻を指定して、BLOB バージョンで **az storage blob immutability-policy set** コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-10-0T18:00:00Z \
    --policy-mode Unlocked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

ロック解除された保持ポリシーを削除するには、**az storage blob immutability-policy delete** コマンドを呼び出します。

```azurecli
az storage blob immutability-policy delete \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="lock-a-time-based-retention-policy"></a>時間ベースの保持ポリシーをロックする

時間ベースの保持ポリシーのテストが完了したら、そのポリシーをロックできます。 ロックされたポリシーは、SEC 17a-4(f) および他の規制コンプライアンスに準拠しています。 ロックされたポリシーの保持間隔は最大 5 倍まで延長できますが、短縮することはできません。

ポリシーはロックされると、削除できません。 ただし、保持間隔の有効期限が切れた後は BLOB を削除できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でポリシーをロックするには、次の手順を実行します。

1. ターゲット コンテナーまたはバージョンを見つけます。 **[詳細]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、既存のロック解除されたポリシーを見つけます。 **[詳細]** ボタンを選択し、メニューから **[ポリシーのロック]** を選択します。
1. ポリシーをロックすることを確認します。

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Azure portal で時間ベースの保持ポリシーをロックする方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してポリシーをロックするには、**Set-AzStorageBlobImmutabilityPolicy** コマンドを呼び出し、**PolicyMode** パラメーターを *Locked* に設定します。

次の例では、ロックが解除されたポリシーに対して有効になっていた保持期間を指定することによって、ポリシーをロックする方法を示します。 また、ポリシーをロックするときに有効期限を変更することもできます。

```azurepowershell
# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

$blobVersion = $blobVersion |
    Set-AzStorageBlobImmutabilityPolicy `
        -ExpiresOn $blobVersion.BlobProperties.ImmutabilityPolicy.ExpiresOn `
        -PolicyMode Locked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell を使用してポリシーをロックするには、**az storage blob immutability-policy set** コマンドを呼び出し、`--policy-mode` パラメーターを *Locked* に設定します。 また、ポリシーをロックするときに有効期限を変更することもできます。

```azurecli
az storage blob immutability-policy set \
    --expiry-time 2021-10-0T18:00:00Z \
    --policy-mode Locked \
    --container <container> \
    --name <blob-version> \
    --account-name <storage-account> \
    --auth-mode login
```

---

## <a name="configure-or-clear-a-legal-hold"></a>訴訟ホールドを構成またはクリアする

訴訟ホールドは、訴訟ホールドが明示的にクリアされるまで、不変データを格納します。 訴訟ホールド ポリシーの詳細については、「[不変 BLOB データの訴訟ホールド](immutable-legal-hold-overview.md)」を参照してください。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して BLOB バージョンの訴訟ホールドを構成するには、次の手順に従います。

1. ターゲット バージョンを見つけます。これは、現在のバージョンまたは BLOB の以前のバージョンである可能性があります。 **[その他]** ボタンを選択し、 **[アクセス ポリシー]** を選択します。
1. **[Immutable blob versions]\(不変 BLOB バージョン\)** セクションで、 **[ポリシーの追加]** を選択します。
1. ポリシーの種類として **[訴訟ホールド]** を選択して、 **[OK]** を選択します。

次の図は、時間ベースの保持ポリシーと訴訟ホールドの両方が構成された BLOB の現在のバージョンを示しています。

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="BLOB バージョンに構成された訴訟ホールドを示すスクリーンショット":::

訴訟ホールドをクリアするには、 **[アクセス ポリシー]** ダイアログに移動し、 **[詳細]** ボタンを選択して、 **[削除]** を選択します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB バージョンの訴訟ホールドを構成またはクリアするには、**Set-AzStorageBlobLegalHold** コマンドを呼び出します。

```azurepowershell
# Set a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -EnableLegalHold

# Clear a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -DisableLegalHold
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して BLOB バージョンの訴訟ホールドを構成またはクリアするには、**az storage blob set-legal-hold** コマンドを呼び出します。

```azurecli
# Set a legal hold
az storage blob set-legal-hold \
    --legal-hold \
    --container <container> \
    --name <blob-version> \
    --account-name <account-name> \
    --auth-mode login

# Clear a legal hold
az storage blob set-legal-hold \
    --legal-hold false \
    --container <container> \
    --name <blob-version> \
    --account-name <account-name> \
    --auth-mode login
```

---

## <a name="next-steps"></a>次のステップ

- [不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)
- [不変 BLOB データに対する時間ベースの保持ポリシー](immutable-time-based-retention-policy-overview.md)
- [不変 BLOB データに対する法的な保持](immutable-legal-hold-overview.md)
