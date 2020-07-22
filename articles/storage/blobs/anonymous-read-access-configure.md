---
title: コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する
titleSuffix: Azure Storage
description: ストレージ アカウントの BLOB データへの匿名アクセスを有効または無効にする方法について説明します。 コンテナーのパブリック アクセス設定を変更して、コンテナーと BLOB に匿名でアクセスできるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209161"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する

Azure Storage では、コンテナーと BLOB の匿名パブリック読み取りアクセスをサポートしています。 既定では、コンテナーとその BLOB に対するすべての要求は、Azure Active Directory (Azure AD)、または共有キーによる承認のどちらかを使用して承認される必要があります。 匿名アクセスを許可するようにコンテナーのパブリック アクセス レベル設定を構成すると、クライアントは要求の承認なしでそのコンテナー内のデータを読み取ることができます。

> [!WARNING]
> コンテナーがパブリック アクセス用に構成されている場合、任意のクライアントがそのコンテナー内のデータを読み取ることができます。 パブリック アクセスは潜在的なセキュリティ リスクをもたらすため、シナリオで必要な場合を除き、ストレージ アカウントに対しては無効にすることをお勧めします。 詳細については、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)」を参照してください。

コンテナーのパブリック アクセスを構成するには、次の 2 つの手順を実行する必要があります。

1. ストレージ アカウントのパブリック アクセスを有効にする
1. コンテナーのパブリック アクセス設定を構成する

この記事では、コンテナーとその BLOB の匿名パブリック読み取りアクセスを構成する方法について説明します。 クライアント アプリケーションから匿名で BLOB データにアクセスする方法については、「[.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)」を参照してください。

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>ストレージ アカウントのパブリック読み取りアクセスの有効化または無効化

既定では、ストレージ アカウントのパブリック アクセスは有効です。 パブリック アクセスを無効にすると、そのアカウントのコンテナーと BLOB に対するすべての匿名アクセスが禁止されます。 セキュリティを向上させるために、ユーザーが匿名で BLOB リソースにアクセスすることが必要なシナリオを除き、ストレージ アカウントのパブリック アクセスは無効にすることをお勧めします。

> [!WARNING]
> ストレージ アカウントでパブリック アクセスを無効にすると、そのストレージ アカウント内のすべてのコンテナーのパブリック アクセス設定がオーバーライドされます。 ストレージ アカウントでパブリック アクセスを無効にすると、以後、そのアカウントに対する匿名要求は失敗します。

ストレージ アカウントのパブリック アクセスを有効または無効にするには、Azure portal または Azure CLI を使用して、アカウントの **blobPublicAccess** プロパティを構成します。 このプロパティは、Azure Resource Manager のデプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 詳細については、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でストレージ アカウントのパブリック アクセスを有効または無効にするには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** から **[構成]** 設定を探します。
1. **[Blob public access]\(BLOB パブリック アクセス\)** を **[無効]** または **[有効]** に設定します。

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="アカウントの BLOB パブリック アクセスを有効または無効にする方法を示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントのパブリック アクセスを有効または無効にするには、まず、[az resource show](/cli/azure/resource#az-resource-show) コマンドを実行してストレージ アカウントのリソース ID を取得します。 次に、[az resource update](/cli/azure/resource#az-resource-update) コマンドを実行して、ストレージ アカウントの **allowBlobPublicAccess** プロパティを設定します。 パブリック アクセスを有効にするには、**allowBlobPublicAccess** プロパティを true に設定します。無効にするには、**false** に設定します。

次の例では、ストレージ アカウントのパブリック BLOB アクセスを無効にします。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

パブリック アクセスが有効かどうかを Azure CLI で 確認するには、[az resource show](/cli/azure/resource#az-resource-show) コマンドを実行して、**allowBlobPublicAccess** プロパティをクエリします。

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> ストレージ アカウントのパブリック アクセスを無効にしても、そのストレージ アカウントでホストされている静的な Web サイトには影響しません。 **$web** コンテナーは常にパブリック アクセスが可能です。

## <a name="check-the-public-access-setting-for-a-storage-account"></a>ストレージ アカウントのパブリック アクセス設定の確認

ストレージ アカウントのパブリック アクセス設定を確認するには、**allowBlobPublicAccess** プロパティの値を取得します。 多数のストレージ アカウントを対象にこのプロパティをまとめて確認するには、Azure Resource Graph エクスプローラーを使用します。

**allowBlobPublicAccess** プロパティは既定では未設定で、明示的に設定されるまで値を返しません。 プロパティ値が null の場合、ストレージ アカウントは既定でパブリック アクセスを許可します。

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>1 つのストレージ アカウントのパブリック アクセス設定の確認

1 つのストレージ アカウントのパブリック アクセス設定を Azure CLI を使用して確認するには、[az resource show](/cli/azure/resource#az-resource-show) コマンドを実行して、**allowBlobPublicAccess** プロパティをクエリします。

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>複数のストレージ アカウントのパブリック アクセス設定の確認

Azure portal の Azure Resource Graph エクスプローラーを使用すると、複数のストレージ アカウントにまたがって最も高速にパブリック アクセス設定を確認できます。 Resource Graph エクスプローラーの使用方法については、「[クイックスタート: Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](/azure/governance/resource-graph/first-query-portal)」を参照してください。

Resource Graph エクスプローラーで次のクエリを実行すると、ストレージ アカウントの一覧が返され、各アカウントの **allowBlobPublicAccess** プロパティの値が表示されます。

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>コンテナーのパブリック アクセス レベルの設定

匿名ユーザーにコンテナーとその BLOB に対する読み取りアクセスを許可するには、まずストレージ アカウントのパブリック アクセスを有効にしてから、コンテナーのパブリック アクセス レベルを設定します。 ストレージ アカウントのパブリック アクセスが無効になっている場合、コンテナーのパブリック アクセスを構成できません。

ストレージ アカウントのパブリック アクセスが有効になっている場合、次のアクセス許可を使用してコンテナーを構成できます。

- **パブリック読み取りアクセスなし:** コンテナーとその BLOB には、承認された要求を使用しなければアクセスできません。 このオプションは、すべての新しいコンテナーの既定値です。
- **BLOB に限定したパブリック読み取りアクセス:** コンテナー内の BLOB は匿名要求で読み取り可能ですが、コンテナー データは匿名では使用できません。 匿名クライアントはコンテナー内の BLOB を列挙することはできません。
- **コンテナーとその BLOB に対するパブリック読み取りアクセス:** コンテナーと BLOB のデータは、コンテナーのアクセス許可設定とコンテナーのメタデータを除いて、匿名要求によって読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

個々の BLOB のパブリック アクセス レベルを変更することはできません。 パブリック アクセス レベルは、コンテナー レベルでのみ設定されます。

コンテナーのパブリック アクセス レベルを設定するには、Azure portal または Azure CLI を使用します。 コンテナーの作成時にコンテナーのパブリック アクセス レベルを設定するか、既存のコンテナーでこの設定を更新することができます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal で 1 つ以上の既存のコンテナーのパブリック アクセス レベルを更新するには、次の手順を実行します。

1. Azure portal でご利用のストレージ アカウントの概要に移動します。
1. メニュー ブレードの **[BLOB サービス]** で、 **[コンテナー]** を選択します。
1. パブリック アクセス レベルを設定するコンテナーを選択します。
1. **[アクセス レベルの変更]** ボタンを使用して、パブリック アクセスの設定を表示します。
1. **[パブリック アクセス レベル]** ドロップダウンから目的のパブリック アクセス レベルを選択し、[OK] ボタンをクリックして選択したコンテナーに変更を適用します。

    ![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/anonymous-read-access-configure/configure-public-access-container.png)

ストレージ アカウントのパブリック アクセスが無効になっている場合、コンテナーのパブリック アクセス レベルは設定できません。 コンテナーのパブリック アクセス レベルを設定しようとしても、アカウントでパブリック アクセスが禁止されているため、設定が無効になっています。

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="パブリック アクセスが無効なときはコンテナーのパブリック アクセス レベルの設定がブロックされることを示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して 1 つ以上のコンテナーのパブリック アクセス レベルを更新するには、[az storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) コマンドを実行します。 アカウント キー、接続文字列、または Shared Access Signature (SAS) を渡すことによって、この操作を承認します。 コンテナーのパブリック アクセス レベルを設定する[コンテナー ACL の設定](/rest/api/storageservices/set-container-acl)操作では、Azure AD による承認はサポートされていません。 詳細については、「[Blob および queue データ操作を呼び出す権限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」を参照してください。

次の例では、コンテナーのパブリック アクセス設定を変更して、コンテナーとその BLOB への匿名アクセスを有効にします。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

ストレージ アカウントのパブリック アクセスが無効になっている場合、コンテナーのパブリック アクセス レベルは設定できません。 コンテナーのパブリック アクセス レベルを設定しようとすると、ストレージ アカウントでパブリック アクセスが許可されていないことを知らせるエラーが発生します。

---

## <a name="check-the-container-public-access-setting"></a>コンテナーのパブリック アクセス設定の確認

1 つ以上のコンテナーのパブリック アクセス設定を確認するために、Azure portal、PowerShell、Azure CLI、いずれかの Azure Storage クライアント ライブラリ、または Azure Storage リソース プロバイダーを使用できます。 以下のセクションで、いくつかの例を示します。  

### <a name="check-the-public-access-setting-for-a-single-container"></a>1 つのコンテナーのパブリック アクセス設定の確認

Azure CLI を使用して 1 つ以上のコンテナーのパブリック アクセス レベルを取得するには、[az storage container show permission](/cli/azure/storage/container#az-storage-container-show-permission) コマンドを実行します。 アカウント キー、接続文字列、または Shared Access Signature (SAS) を渡すことによって、この操作を承認します。 コンテナーのパブリック アクセス レベルを返す[コンテナー ACL の取得](/rest/api/storageservices/get-container-acl)操作では、Azure AD による承認はサポートされていません。 詳細については、「[Blob および queue データ操作を呼び出す権限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」を参照してください。

次の例では、コンテナーのパブリック アクセス設定を読み取ります。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>複数のコンテナーのパブリック アクセス設定の確認

コンテナーを一覧表示してパブリック アクセス設定を確認することによって、1 つ以上のストレージ アカウントのどのコンテナーがパブリック アクセス用に構成されているかを確認できます。 この方法は、ストレージ アカウントに多数のコンテナーが含まれていない場合や、少数のストレージ アカウントにまたがって設定を確認する場合に実用的な選択肢です。 ただし、多数のコンテナーを列挙しようとすると、パフォーマンスが低下する可能性があります。

次の例では、PowerShell を使用して、ストレージ アカウントのすべてのコンテナーのパブリック アクセス設定を取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>次のステップ

- [コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)
- [.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)
- [Azure Storage へのアクセスを承認する](../common/storage-auth.md)