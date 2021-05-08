---
title: Azure リソースのマネージド ID に関してよく寄せられる質問 - Azure AD
description: マネージド ID に関してよく寄せられる質問
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 0d6527c27420617728428e440c94a60236701212
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376768"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Azure リソースのマネージド ID に関してよく寄せられる質問 - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

## <a name="administration"></a>管理

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>マネージド ID を持つリソースを見つけるにはどうすればいいですか?

次の Azure CLI コマンドを使用して、システム割り当てマネージド ID を持つリソースの一覧を検索できます。 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>リソースのマネージド ID に必要な Azure RBAC アクセス許可は何ですか? 

- システム割り当てマネージド ID:リソースに対する書き込みアクセス許可が必要です。 たとえば、仮想マシンには Microsoft.Compute/virtualMachines/write が必要です。 このアクションは、[Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) などのリソース固有の組み込みロールに含まれています。
- ユーザー割り当てマネージド ID:リソースに対する書き込みアクセス許可が必要です。 たとえば、仮想マシンには Microsoft.Compute/virtualMachines/write が必要です。 さらにマネージド ID に対する [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当て。

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を作成できないようにするにはどうすればよいですか。

[Azure Policy](../../governance/policy/overview.md) を使用して、ユーザー割り当てマネージド ID をユーザーが作成できないようにすることができます

1. [Azure portal](https://portal.azure.com) に移動し、 **[ポリシー]** に移動します。
2. **[定義]** を選択します。
3. **[+ ポリシー定義]** を選択し、必要な情報を入力します。
4. ポリシー規則セクションに、次を貼り付けます。
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

ポリシーを作成したら、使用するリソース グループに割り当てます。

1. リソース グループに移動します。
2. テスト用に使用しているリソース グループを見つけます。
3. 左側のメニューから **[ポリシー]** を選択します。
4. **[ポリシーの割り当て]** を選択します。
5. **[基本]** セクションで、次を指定します。
    1. **[Scope]\(スコープ\)** : テスト用に使用しているリソース グループ
    1. **[ポリシー定義]** :前に作成したポリシー。
6. 他のすべての設定は既定値のままにして、 **[確認と作成]** を選択します

この時点で、リソース グループ内でユーザー割り当てマネージド ID を作成しようとすると失敗します。

  ![ポリシー違反](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>概念

### <a name="do-managed-identities-have-a-backing-app-object"></a>マネージド ID にバッキング アプリ オブジェクトはありますか?

いいえ。 マネージド ID と Azure AD アプリ登録は、ディレクトリ内で同じものではありません。

アプリの登録には、次の 2 つのコンポーネントがあります。アプリケーション オブジェクトとサービス プリンシパル オブジェクト。
Azure リソースのマネージド ID にあるのは、これらのコンポーネントのうち、次の 1 つだけです:サービス プリンシパル オブジェクト。

マネージド ID では、アプリケーション オブジェクトがディレクトリにありません。これは、MS グラフに対するアプリのアクセス許可を付与するために一般的に使用されるものです。 代わりに、マネージド ID に対する MS グラフのアクセス許可をサービス プリンシパルに直接付与する必要があります。

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>マネージド ID に関連付けられる資格情報は何ですか? その有効期間とローテーションの頻度は?

> [!NOTE]
> マネージド ID の認証方法は、内部的な実装の詳細であり、予告なく変更されます。

マネージド ID には、証明書ベースの認証が使用されます。 各マネージド ID の資格情報は有効期限が 90 日で、45 日後にローテーションされます。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>要求内で ID を指定しない場合、IMDS はどの ID を規定値としますか?

- システム割り当てマネージド ID が有効で、要求内に ID が指定されない場合、IMDS ではシステム割り当てマネージド ID が規定で使用されます。
- システム割り当てマネージド ID が有効でなく、ユーザー割り当てマネージド ID が 1 つのみの場合、その単一のユーザー割り当てマネージド ID が既定値になります。
- システム割り当てマネージド ID が有効でなく、複数のユーザー割り当てマネージド ID が存在する場合は、要求内にマネージド ID を指定する必要があります。

## <a name="limitations"></a>制限事項

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>同じマネージド ID を複数のリージョンで使用できますか?

簡単に言えば、はい、ユーザー割り当て済みマネージド ID は複数の Azure リージョンで使用できます。 詳しく回答すると、ユーザー割り当てマネージド ID はリージョン リソースとして作成されますが、Azure AD で作成される、関連付けられた[サービス プリンシパル](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) はグローバルに使用できます。 サービス プリンシパルはどの Azure リージョンからでも使用でき、その可用性は Azure AD の可用性に依存します。 たとえば、中南部リージョンでユーザー割り当て済みマネージド ID を作成し、そのリージョンが使用できなくなった場合、この問題はマネージド ID 自体の[コントロール プレーン アクティビティ](../../azure-resource-manager/management/control-plane-and-data-plane.md)のみに影響します。  マネージド ID を使用するように既に構成されているリソースによって実行されるアクティビティは影響を受けません。

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure リソースのマネージド ID は Azure Cloud Services で動作しますか?

いいえ、Azure Cloud Services で Azure リソースのマネージド ID をサポートする予定はありません。


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID のセキュリティ境界は何ですか?

ID のセキュリティ境界は、ID のアタッチ先リソースです。 たとえば、Azure リソースのマネージド ID が有効な仮想マシンのセキュリティ境界は、仮想マシンです。 その VM 上で実行されているすべてのコードは、Azure リソース エンドポイントと要求トークンのマネージド ID を呼び出すことができます。 これは Azure リソースのマネージド ID をサポートする他のリソースと同様のエクスペリエンスです。

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>サブスクリプションを別のディレクトリに移動する場合、マネージド ID は自動的に再作成されますか?

いいえ。 サブスクリプションを別のディレクトリに移動する場合、お客様が手動でそれらを再作成し、再度 Azure ロールの割り当てを許可する必要があります。
- システム割り当てマネージドID の場合、無効にしてから最有効化します。 
- ユーザー割り当てマネージド ID の場合、削除、再作成の後、必要なリソース (例： 仮想マシン) へ再度アタッチします

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>マネージド ID を使って違うディレクトリやテナント内のリソースへアクセスできますか?

いいえ。 マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>マネージド ID に適用されるレート制限はありますか?

マネージド ID の制限には、Azure サービスの制限、Azure Instance Metadata Service (IMDS) の制限、および Azure Active Directory サービスの制限に対する依存関係があります。

- **Azure サービスの制限** では、テナントとサブスクリプションのレベルで実行できる作成操作の数が定義されます。 ユーザー割り当てマネージド ID には、名前付けの方法に関する[制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)もあります。
- **IMDS**: 一般に、IMDS への要求は、1 秒あたり 5 つの要求に制限されます。 このしきい値を超える要求は、429 の応答で拒否されます。 マネージド ID カテゴリに対する要求は、1 秒あたり 20 個の要求、同時要求数は 5 個に制限されます。 詳細については、「[Azure Instance Metadata Service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)」の記事を参照してください。
- **Azure Active Directory サービス**: 「[Azure AD サービスの制限と制約](../enterprise-users/directory-service-limits-restrictions.md)」で説明されているように、各マネージド ID は、Azure AD テナントにおけるオブジェクトのクォータ制限に加算されます。


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>ユーザー割り当てマネージド ID を異なるリソース グループまたはサブスクリプションに移動できますか?

ユーザー割り当てマネージド ID の異なるリソース グループへの移動はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [マネージド ID と仮想マシンの連携](how-managed-identities-work-vm.md)について説明します。
