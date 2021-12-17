---
title: Azure Active Directory テナント間でのオブジェクト レプリケーションを禁止する (プレビュー)
titleSuffix: Azure Storage
description: テナント間オブジェクト レプリケーションを禁止する
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2fe98c0a15b1ec07ff1608e00aa030fd18360547
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599193"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>Azure Active Directory テナント間でのオブジェクト レプリケーションを禁止する (プレビュー)

オブジェクト レプリケーションを使用すると、ブロック BLOB が 1 つのストレージ アカウントのコンテナーから別のストレージ アカウントのコンテナーに非同期でコピーされます。 オブジェクト レプリケーション ポリシーを構成するときは、ソース アカウントとコンテナー、ならびにコピー先のアカウントとコンテナーを指定します。 ポリシーを構成すると、Azure Storage によってソース オブジェクトに対する作成、更新、および削除操作の結果が、コピー先のオブジェクトに自動的にコピーされます。 Azure Storage でのオブジェクト レプリケーションの詳細については、「[ブロック BLOB のオブジェクト レプリケーション](object-replication-overview.md)」を参照してください。

既定では、許可されているユーザーは、ソース アカウントが 1 つの Azure Active Directory (Azure AD) テナントにあり、コピー先のアカウントが異なるテナントにあるオブジェクト レプリケーション ポリシーを構成することができます。 セキュリティ ポリシーにより、オブジェクト レプリケーションを同じテナント内に存在するストレージ アカウントのみに制限する必要がある場合は、ソースおよびコピー先のアカウントが異なるテナントに存在するポリシーの作成を禁止することができます (プレビュー)。 既定では、明示的に禁止しない限り、ストレージ アカウントのテナント間オブジェクト レプリケーションは有効になります。

この記事では、ストレージ アカウントのテナント間オブジェクト レプリケーションを修復する方法について説明します。 また、新規および既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止するポリシーを作成する方法も説明します。

テナント間ポリシーを含む、オブジェクト レプリケーション ポリシーを構成する方法の詳細については、「[ブロック BLOB のオブジェクト レプリケーションを構成する](object-replication-configure.md)」を参照してください。

## <a name="remediate-cross-tenant-object-replication"></a>テナント間オブジェクト レプリケーションを修復する

Azure AD テナント間でのオブジェクト レプリケーションを禁止するには、ストレージ アカウントの **AllowCrossTenantReplication** プロパティを **false** に設定します。 現在、テナント間オブジェクト レプリケーション ポリシーに含まれていないストレージ アカウントがある場合は、**AllowCrossTenantReplication** プロパティを *false* に設定すると、今後、テナント間オブジェクト レプリケーション ポリシーでこのストレージ アカウントをソースまたはコピー先として使用するよう構成することができなくなります。 ただし、ストレージ アカウントが現在 1 つ以上のテナント間オブジェクト レプリケーション ポリシーに含まれている場合、既存のテナント間ポリシーを削除するまで **AllowCrossTenantReplication** プロパティを *false* に設定することはできません。

ストレージ アカウントでは、テナント間ポリシーが既定で許可されています。 ただし、新規または既存のストレージ アカウントでは **AllowCrossTenantReplication** プロパティは既定で設定されておらず、明示的に設定するまで、値は返されません。 ストレージ アカウントは、プロパティ値が **null** または **true** の場合、テナント間オブジェクト レプリケーション ポリシーに含めることができます。

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>新しいアカウントのテナント間レプリケーションを修復する

新しいストレージ アカウントのテナント間レプリケーションを禁止するには、Azure portal、PowerShell、または Azure CLI を使用します。

#### <a name="portal"></a>[ポータル](#tab/portal)

新しいストレージ アカウントでテナント間オブジェクト レプリケーションを禁止するには、次の手順に従います。

1. Azure portal で、 **[ストレージ アカウント]** ページに移動し、 **[作成]** を選択します。
1. **[基本]** タブに新しいストレージ アカウントの情報を記入します。
1. **[詳細設定]** タブの **[BLOB ストレージ]** セクションで、 **[Allow cross-tenant replication]\(テナント間レプリケーションを許可する\)** 設定を見つけて、チェックボックスをオフにします。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="新しいストレージ アカウントでテナント間オブジェクト レプリケーションを禁止する方法を示すスクリーンショット":::

1. アカウントを作成するプロセスを完了します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

新しいストレージ アカウントでテナント間オブジェクト レプリケーションを禁止するには、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを呼び出し、値を *$false* に設定した `AllowCrossTenantReplication` パラメーターを含めます。

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

新しいストレージ アカウントでテナント間オブジェクト レプリケーションを禁止するには、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを呼び出し、値を *false* に設定した `allow-cross-tenant-replication` パラメーターを含めます。

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>既存のアカウントのテナント間レプリケーションを修復する

既存のストレージ アカウントのテナント間レプリケーションを禁止するには、Azure portal、PowerShell、または Azure CLI を使用します。

#### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

現在テナント間ポリシーに含まれていない既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止するには、次の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[データ管理]** で、 **[オブジェクト レプリケーション]** を選択します。
1. **[詳細設定]** を選択します。
1. **[Allow cross-tenant replication]\(テナント間レプリケーションを許可する\)** のチェックをオフにします。 このボックスは既定ではオンになっています。これは、明示的に禁止しない限り、ストレージ アカウントのテナント間オブジェクト レプリケーションは許可されているためです。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="既存のストレージ アカウントでテナント間オブジェクト レプリケーションを禁止する方法を示すスクリーンショット":::

1. **[OK]** を選択して変更を保存します。

ストレージ アカウントが現在、1 つ以上のテナント間レプリケーション ポリシーに含まれている場合、それらのポリシーを削除するまで、テナント間オブジェクト レプリケーションを禁止することはできません。 このシナリオでは、次の図に示すように、Azure portal ではこの設定を使用することはできません。

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="Screenshot":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

現在テナント間ポリシーに含まれていない既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止するには、まず [Az.Storage PowerShell モジュール](https://www.powershellgallery.com/packages/Az.Storage)\, バージョン 3.7.0 以降をインストールします。 次に、ストレージ アカウントの **AllowCrossTenantReplication** プロパティを構成します。

次の例は、PowerShell を使用して既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

ストレージ アカウントが現在、1 つ以上のテナント間レプリケーション ポリシーに含まれている場合、それらのポリシーを削除するまで、テナント間オブジェクト レプリケーションを禁止することはできません。 PowerShell には、既存のテナント間レプリケーション ポリシーが原因で操作が失敗したことを示すエラーが表示されます。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

現在テナント間ポリシーに含まれていない既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止するには、まず Azure CLI バージョン 2.24.0 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 次に、新規または既存のストレージ アカウントの **allowCrossTenantReplication** プロパティを構成します。

次の例は、Azure CLI を使用して既存のストレージ アカウントに対してテナント間オブジェクト レプリケーションを禁止する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

ストレージ アカウントが現在、1 つ以上のテナント間レプリケーション ポリシーに含まれている場合、それらのポリシーを削除するまで、テナント間オブジェクト レプリケーションを禁止することはできません。 Azure CLI には、既存のテナント間レプリケーション ポリシーが原因で操作が失敗したことを示すエラーが表示されます。

---

テナント間レプリケーションを禁止にした後に、ストレージ アカウントをソースまたはコピー先に設定したテナント間ポリシーを構成しようとすると失敗します。 Azure Storage から、そのストレージ アカウントではテナント間オブジェクト レプリケーションが許可されていないことを示すエラーが返されます。

ストレージ アカウントに対してテナント間オブジェクト レプリケーションが禁止されている場合、そのアカウントで作成する新しいオブジェクト レプリケーション ポリシーには、ソースとコピー先アカウントの完全な Azure Resource Manager ID を含める必要があります。 Azure Storage は、移行元と移行先のアカウントが同じテナント内に存在するかどうかを確認するために、完全なリソース ID を必要とします。 詳細については、[ソースとコピー先アカウントの完全なリソース ID を指定する](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts)方法に関するセクションを参照してください。

**AllowCrossTenantReplication** プロパティは、Azure Resource Manager デプロイ モデルを使用するストレージ アカウントのみでサポートされています。 Azure Resource Manager デプロイ モデルを使用しているストレージ アカウントの詳細については、「[ストレージ アカウントの種類](../common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>テナント間レプリケーションを許可または禁止するためのアクセス許可

ストレージ アカウントの **AllowCrossTenantReplication** プロパティを設定するには、ストレージ アカウントを作成および管理するためのアクセス許可が必要です。 これらのアクセス許可を提供する Azure ロールベースのアクセス制御 (Azure RBAC) ロールには、**Microsoft.Storage/storageAccounts/write** または **Microsoft.Storage/storageAccounts/\*** アクションが含まれます。 このアクションの組み込みロールには、次のようなロールがあります。

- Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール
- [Storage Account の共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)ロール

これらのロールでは、Azure Active Directory (Azure AD) を使用してストレージ アカウントのデータにアクセスすることはできません。 ただし、アカウント アクセス キーへのアクセスを許可する **Microsoft.Storage/storageAccounts/listkeys/action** が含まれています。 このアクセス許可では、ユーザーがアカウント アクセス キーを使用して、ストレージ アカウント内のすべてのデータにアクセスできます。

ユーザーがストレージ アカウントに対するテナント間オブジェクト レプリケーションを許可または禁止できるようにするには、ロール割り当てのスコープをストレージ アカウント以上のレベルにする必要があります。 ロール スコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」を参照してください。

これらのロールを割り当てる際には、ストレージ アカウントを作成したり、そのプロパティを更新したりする機能を必要とするユーザーにのみ割り当てるように、注意してください。 最小限の特権の原則を使用して、ユーザーに、それぞれのタスクを実行するのに必要な最小限のアクセス許可を割り当てるようにします。 Azure RBAC でアクセスを管理する方法の詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者** ロールにはすべてのアクションが含まれているため、これらの管理者ロールのいずれかを持つユーザーも、ストレージ アカウントを作成および管理できます。 詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)に関する記事を参照してください。

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy を使用してコンプライアンスを監査する

多数のストレージ アカウントがある場合、監査を実行して、それらのアカウントがテナント間オブジェクト レプリケーションを防止するように構成されていることを確認したい場合があります。 一連のストレージ アカウントのコンプライアンスを監査するには、Azure Policy を使用します。 Azure Policy は、Azure リソースにルールを適用するポリシーの作成、割り当て、管理に使用できるサービスです。 Azure Policy を使用すると、それらのリソースが会社の標準やサービス レベル アグリーメントに準拠した状態を維持するのに役立ちます。 詳細については、[Azure Policy の概要](../../governance/policy/overview.md)に関するページを参照してください。

### <a name="create-a-policy-with-an-audit-effect"></a>Audit 効果を持つポリシーを作成する

Azure Policy では、ポリシー規則がリソースに対して評価されたときに実行される動作を決定する効果がサポートされています。 Audit 効果を使用すると、リソースが準拠していない場合に警告が生成されますが、要求は停止されません。 効果の詳細については、「[Azure Policy の効果について](../../governance/policy/concepts/effects.md)」を参照してください。

Azure portal でストレージ アカウントのテナント間オブジェクト レプリケーション設定のために Audit 効果を持つポリシーを作成するには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[作成]** セクションで **[定義]** を選択します。
1. **[ポリシー定義の追加]** を選択して、新しいポリシー定義を作成します。
1. **[定義の場所]** フィールドで、 **[More]\(詳細\)** ボタンを選択して、監査ポリシーのリソースがある場所を指定します。
1. ポリシーの名前を指定します。 必要に応じて説明およびカテゴリを指定することもできます。
1. **[ポリシー規則]** で、次のポリシー定義を **policyRule** セクションに追加します。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. ポリシーを保存します。

### <a name="assign-the-policy"></a>ポリシーを割り当てる

次に、ポリシーをリソースに割り当てます。 ポリシーのスコープは、そのリソースとその下にあるすべてのリソースに対応します。 ポリシー割り当ての詳細については、「[Azure Policy の割り当ての構造](../../governance/policy/concepts/assignment-structure.md)」を参照してください。

Azure portal でポリシーを割り当てるには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[作成]** セクションで **[割り当て]** を選択します。
1. 新しいポリシー割り当てを作成するために、 **[ポリシーの割り当て]** を選択します。
1. **[スコープ]** フィールドで、ポリシー割り当てのスコープを選択します。
1. **[ポリシー定義]** フィールドで、 **[More]\(詳細\)** ボタンを選択して、前のセクションで定義したポリシーを一覧から選択します。
1. ポリシー割り当て用の名前 を入力します。 説明は省略できます。
1. **[ポリシーの適用]** を "*有効*" のままに設定しておきます。 この設定は、監査ポリシーには影響しません。
1. **[確認および作成]** を選択して割り当てを作成します。

### <a name="view-compliance-report"></a>コンプライアンス レポートを表示する

ポリシーを割り当てたら、コンプライアンス レポートを表示できます。 監査ポリシーのコンプライアンス レポートには、テナント間オブジェクト レプリケーション ポリシーがまだ許可されているストレージ アカウントに関する情報が表示されます。 詳細については、[ポリシーのコンプライアンス データを取得する](../../governance/policy/how-to/get-compliance-data.md)ことに関する記事を参照してください。

ポリシー割り当てが作成された後、コンプライアンス レポートが使用可能になるまで数分かかる場合があります。

Azure portal でコンプライアンス レポートを表示するには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[コンプライアンス]** を選択します。
1. 前の手順で作成したポリシー割り当ての名前の結果をフィルター処理します。 このレポートには、ポリシーに準拠していないリソースが表示されます。
1. レポートをドリルダウンして、準拠していないストレージ アカウントの一覧などの詳細を表示できます。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="BLOB のテナント間オブジェクト レプリケーションについての監査ポリシーのコンプライアンス レポートを示すスクリーンショット":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>Azure Policy を使用して同じテナント内のレプリケーション ポリシーを適用する

Azure Policy では、Azure リソースが要件と標準に準拠していることを確認することで、クラウド ガバナンスがサポートされます。 組織内のストレージ アカウントでテナント間レプリケーションが禁止されるようにするために、テナント間オブジェクト レプリケーション ポリシーを許可する新しいストレージ アカウントを作成できないようにするポリシーを作成します。 強制ポリシーでは、テナント間オブジェクト レプリケーションを許可するためにストレージ アカウントを作成または変更する要求を防ぐための Deny 効果が使用されます。 この Deny ポリシーでは、既存のアカウントに対するテナント間オブジェクト レプリケーションの設定がポリシーに準拠していない場合に、そのアカウントに対するすべての構成変更が禁止されます。 Deny 効果の詳細については、「[Azure Policy の効果について](../../governance/policy/concepts/effects.md)」を参照してください。

テナント間オブジェクト レプリケーションに対して Deny 効果を持つポリシーを作成するには、「[Azure Policy を使用してコンプライアンスを監査する](#use-azure-policy-to-audit-for-compliance)」で説明されている手順に従います。この際、ポリシー定義の **policyRule** セクションでは次の JSON を指定します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Deny 効果を持つポリシーを作成し、これをスコープに割り当てると、ユーザーはテナント間オブジェクト レプリケーションを許可するストレージ アカウントを作成できなくなります。 また、ユーザーは、現在テナント間オブジェクト レプリケーションを許可している既存のストレージ アカウントに対して構成変更を行うこともできません。 この操作を行おうとすると、エラーが発生します。 ポリシーに準拠してアカウントの作成や構成の更新を行うには、ストレージ アカウントの **AllowCrossTenantReplication** プロパティを **false** に設定する必要があります。

次の図は、Deny 効果を持つポリシーでテナント間オブジェクト レプリケーションが禁止されている場合に、テナント間オブジェクト レプリケーションを許可するストレージ アカウント (新しいアカウントの既定) を作成しようとした場合に発生するエラーを示しています。

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="ポリシーに違反するストレージ アカウントを作成したときに発生したエラーを示すスクリーンショット":::

## <a name="see-also"></a>関連項目

- [ブロック BLOB のオブジェクト レプリケーション](object-replication-overview.md)
- [ブロック BLOB のオブジェクト レプリケーションを構成する](object-replication-configure.md)
