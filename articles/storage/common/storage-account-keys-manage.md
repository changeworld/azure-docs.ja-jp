---
title: アカウントのアクセス キーを管理する
titleSuffix: Azure Storage
description: ストレージ アカウントのアクセスキーを表示、管理、およびローテーションする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b7a6e23ae5f1dde2feab830a484cd33107bd7212
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180191"
---
# <a name="manage-storage-account-access-keys"></a>ストレージ アカウント アクセス キーを管理する

ストレージ アカウントを作成すると、Azure により 512 ビットのストレージ アカウント アクセス キーが 2 つ生成されます。 これらのキーは、共有キー認証を使用してストレージ アカウント内のデータへのアクセスする際、認証に使用されます。

アクセスキーの管理には Azure Key Vault を使用し、キーのローテーションと再生成は定期的に行うことをお勧めします。 Azure Key Vault を使用すると、アプリケーションを中断することなく簡単にキーをローテーションできます。 また、キーを手動でローテーションすることもできます。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>アカウントのアクセス キーを表示する

アカウント アクセス キーは、Azure portal、PowerShell、または Azure CLI を使用して表示したり、コピーしたりできます。 Azure portal にはストレージ アカウントの接続文字列も用意されているのでコピーできます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からストレージ アカウントのアクセス キーまたは接続文字列を表示およびコピーするには:

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. **[セキュリティとネットワーク]** で、 **[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。

3. **[キーの表示]** を選択して、アクセス キーと接続文字列を表示し、値をコピーするボタンを有効にします。

4. **[key1]** の下で **[キー]** 値を見つけます。 **[コピー]** ボタンを選択してアカウント キーをコピーします。

5. あるいは、接続文字列全体をコピーできます。 **[key1]** の下で **[接続文字列]** の値を見つけます。 **[コピー]** ボタンを選択して接続文字列をコピーします。

    :::image type="content" source="./media/storage-account-keys-manage/portal-connection-string.png" alt-text="Azure portal でアクセス キーを表示する方法を示したスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell でアカウント アクセス キーを取得するには、[Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) コマンドを呼び出します。

次の例では、最初のキーを取得します。 2 つ目のキーを取得するには、`Value[0]` の代わりに `Value[1]` を使用します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でアカウント アクセス キーを一覧表示するには、次の例のように [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

2 つのキーのいずれかを使用して Azure Storage にアクセスすることもできますが、一般的には、最初のキーを使用し、キーのローテーション時に 2 番目のキーの使用を予約することをお勧めします。

アカウントのアクセス キーを表示または読み込むには、ユーザーがサービス管理者であるか、**Microsoft.Storage/storageAccounts/listkeys/action** を含む Azure ロールが割り当てられている必要があります。 このアクションが含まれる Azure 組み込みロールには、**所有者**、**共同作成者**、および **ストレージ アカウント キー オペレーターのサービス ロール** があります。 サービス管理者ロールの詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関する記事を参照してください。 Azure Storage 用の組み込み RBAC ロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault を使用してアクセスキーを管理する

Microsoft では、Azure Key Vault を使用して、アクセスキーの管理とローテーションをするようお勧めします。 アプリケーションは、Key Vault 内のキーに安全にアクセスできるので、アプリケーションコードと一緒に格納することは避けてください。 キー管理に Key Vault を使用する方法の詳細については、次の記事を参照してください:

- [Azure Key Vault と PowerShell を使用してストレージ アカウント キーを管理する](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure Key Vault と Azure CLI を使用してストレージアカウントキーを管理する](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>アクセス キーを手動でローテーションする

Microsoft では、ストレージアカウントのセキュリティを確保するために、アクセスキーを定期的にローテーションすることをお勧めします。 可能であれば、Azure Key Vault を使用してアクセスキーを管理してください。 Key Vault を使用していない場合は、キーを手動でローテーションする必要があります。

キーをローテーションさせることができるように、2 つのアクセス キーが割り当てられます。 2 つのキーで、アプリケーションはプロセス全体を通じて Azure Storage へのアクセスを維持します。

> [!WARNING]
> アクセス キーの再生成は、ストレージ アカウント キーに依存するあらゆるアプリケーションまたは Azure サービスに影響を与える可能性があります。 メディア サービス、クラウド、モバイル アプリケーション、Azure Storage 向けのグラフィカル ユーザー インターフェイス アプリケーション ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) を含む、アクセス キーを使用してストレージ アカウントにアクセスするクライアントは新しいキーを使用するように更新する必要があります。

アクセス キーのローテーションを手動で行うことを検討している場合は、キーの有効期限ポリシーを設定し、Azure Monitor からクエリを使用して、アクセス キーをローテーションするタイミングを判断することをお勧めします。

### <a name="create-a-key-expiration-policy"></a>キーの有効期限ポリシーを作成する

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、キーの有効期限ポリシーを作成するには:

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. **[セキュリティとネットワーク]** で、 **[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。

3. **[交換リマインダーの設定]** リンクを選択します。

4. **[アクセス キーを交換するためのリマインダーを設定する]** で **[キー交換リマインダーを有効にする]** チェックボックスをオンにして、アラームの頻度を設定します。

5. **[保存]** を選択します。

:::image type="content" source="media/storage-account-keys-manage/portal-key-expiration-policy.png" alt-text="Azure portal でのキーの有効期限ポリシーの構成方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

キーの有効期限ポリシーを作成するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを使用して、アクセス キーがアクティブになってからローテーションが必要になるまでの日数を `-KeyExpirationPeriodInDay` パラメーターで設定します。

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> -Name `
    <storage-account-name>  -KeyExpirationPeriodInDay <period-in-days> 
```

> [!TIP]
> キーの有効期限ポリシーの設定は、ストレージ アカウントの作成時に、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドの `-KeyExpirationPeriodInDay` パラメーターの設定で行うこともできます。

ポリシーが適用されていることを確認するには、前のコマンドの `$account` 変数に返された [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) の `KeyPolicy` プロパティを使用します。

```powershell
$account.KeyPolicy
```

コンソールの出力にキーの有効期限が表示されます。

> [!div class="mx-imgBorder"]
> ![アクセス キーの有効期限](./media/storage-account-keys-manage/key-policy-powershell.png)

有効期限を超えてアクティブになっている既存のキーをローテーションしたい場合があります。 キーの作成日時を調べるには、`KeyCreationTime` プロパティを使用します。

```powershell
$account.KeyCreationTime
```

両方のアクセス キーの作成時刻がコンソールの出力に表示されます。

> [!div class="mx-imgBorder"]
> ![アクセス キーの作成時刻](./media/storage-account-keys-manage/key-creation-time-powershell.png)

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

既存のストレージ アカウントにキーの有効期限ポリシーを作成するには、[az storage account update](/cli/azure/storage/account#az_storage_account_update) コマンドを使用して、アクセス キーがアクティブになってからローテーションが必要になるまでの日数を `--key-exp-days` パラメーターで設定します。

```azurecli-interactive
az storage account update \
  -n <storage-account-name> \
  -g <resource-group> --key-exp-days <period-in-days>
```

> [!TIP]
> キーの有効期限ポリシーの設定は、ストレージ アカウントの作成時に、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドの `-KeyExpirationPeriodInDay` パラメーターの設定で行うこともできます。

ポリシーが適用されていることを確認するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出します。`-query` パラメーターには、`{KeyPolicy:keyPolicy}` という文字列を使用します。

```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "{KeyPolicy:keyPolicy}"
```

コンソールの出力にキーの有効期限が表示されます。

```json
{
  "KeyPolicy": {
    "keyExpirationPeriodInDays": 5
  }
}
```

有効期限を超えてアクティブになっている既存のキーをローテーションしたい場合があります。 キーがいつ作成されたかを調べるには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを使用します。-query パラメーターには、`keyCreationTime` という文字列を使用します。

```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "keyCreationTime"
```

---

### <a name="query-for-policy-violations"></a>ポリシー違反を照会する

[Azure Log Analytics ワークスペースにログを送信](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics)する診断設定を作成すれば、Azure Monitor ログ クエリを使用して、キーの有効期限が切れているかどうかを調べることができます。

キーの有効期限が切れているかどうかを調べるには、 **[ログ検索]** バーに次のクエリを入力します。

```kusto
StorageBlobLogs | where KeyExpiryStatus startsWith "Policy Violated". 
```

有効期限が近づいているかどうかを調べることのできるクエリを作成することもできます。 この情報を取得するクエリは次のとおりです。

```kusto
StorageBlobLogs 
| where type =~ 'microsoft.storage/storageAccounts'
| extend days = datetime_diff('day', now(), todatetime(parse_json(properties).keyCreationTime))
| extend KeyExpiryStatus = iff(days > 180, "Policy Violated", "")
| project name, days, KeyExpiryStatus  
```

### <a name="rotate-access-keys"></a>アクセス キーをローテーションする

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でストレージ アカウントのアクセス キーをローテーションさせるには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

2. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。

3. **[セキュリティとネットワーク]** で、 **[アクセス キー]** を選択します。

4. ストレージ アカウントのプライマリ アクセス キーを再生成するには、プライマリ アクセス キーの隣にある **[再生成]** ボタンを選択します。

5. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

6. 同様に、セカンダリ アクセス キーを再生成します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントのアクセス キーをローテーションするには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

2. 次の例に示すように、[New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) コマンドを呼び出し、プライマリ アクセス キーを再生成します。

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

4. 同様に、セカンダリ アクセス キーを再生成します。 セカンダリ キーを再生成するには、キー名として `key1` の代わりに `key2` を使用します。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントのアクセス キーをローテーションするには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

2. 次の例に示すように、[az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) コマンドを呼び出し、プライマリ アクセス キーを再生成します。

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account> \
      --key primary
    ```

1. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。

2. 同様に、セカンダリ アクセス キーを再生成します。 セカンダリ キーを再生成するには、キー名として `primary` の代わりに `secondary` を使用します。

---

> [!NOTE]
> Microsoft では、すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーを循環させるときに、一部のアプリケーションがアクセス権を失います。

アカウントのアクセス キーをローテーションさせるには、ユーザーがサービス管理者であるか、**Microsoft.Storage/storageAccounts/regeneratekey/action** を含む Azure ロールが割り当てられている必要があります。 このアクションが含まれる Azure 組み込みロールには、**所有者**、**共同作成者**、および **ストレージ アカウント キー オペレーターのサービス ロール** があります。 サービス管理者ロールの詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関する記事を参照してください。 Azure Storage 用の Azure 組み込みロールの詳細については、[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)に関するページの「**ストレージ**」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-account-create.md)
