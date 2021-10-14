---
title: Application Gateway の一般的なキー コンテナー エラー
titleSuffix: Azure Application Gateway
description: この記事では、キー コンテナー関連の問題を識別し、Application Gateway のスムーズな操作のためにユーザーがこれらの問題を解決できるように支援します。
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: cfcacf84b5a35862c3ef1423ed58ff4d5d7f3142
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272322"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Azure Application Gateway の一般的なキー コンテナー エラー

この記事では、発生する可能性のあるキー コンテナー エラー コードの詳細 (これらのエラーの原因を含む) をユーザーが理解できるように支援します。 この記事にはまた、このような構成の誤りを解決する手順も含まれています。

> [!TIP]
> バージョンを指定しないシークレット識別子を使用してください。 これにより、Azure Key Vault で新しいバージョンが使用できる場合、Azure Application Gateway は証明書を自動的にローテーションします。 バージョンのないシークレット URI の例には `https://myvault.vault.azure.net/secrets/mysecret/` があります。

## <a name="list-of-error-codes-and-their-details"></a>エラー コードとその詳細の一覧

以降のセクションには、発生する可能性のあるさまざまなエラーが含まれています。 これらの詳細は Azure Advisor で確認できるため、このトラブルシューティングに関する記事はこれらの問題を解決するために使用してください。 詳細については、「[Azure portal を使用して新しい推奨事項に合わせて Azure Advisor アラートを作成する](../advisor/advisor-alerts-portal.md)」を参照してください。

> [!NOTE]
> Azure Application Gateway では、キー コンテナーの診断に関するログを 4 時間ごとに生成します。 構成を修正した後も診断で引き続きエラーが表示される場合は、ログが更新されるまで待つことが必要になる可能性があります。

[comment]: # (エラー コード 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>エラー コード: UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**説明:** 関連付けられているユーザー割り当てマネージド ID に "Get" アクセス許可がありません。 

**解決方法:** ユーザー割り当てマネージド ID にシークレットに対するこのアクセス許可を付与するように Key Vault のアクセス ポリシーを構成します。 
1. Azure portal で、リンクされたキー コンテナーに移動します。
1. **[アクセス ポリシー]** ウィンドウを開きます。
1. **[アクセス許可モデル]** で、 **[コンテナー アクセス ポリシー]** を選択します。
1. **[シークレットの管理操作]** で、 **[取得]** アクセス許可を選択します。
1. **[保存]** を選択します。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="Get アクセス許可のエラーを解決する方法を示すスクリーンショット。":::

詳細については、[Azure portal を使用した Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関するページを参照してください。

[comment]: # (エラー コード 2)
### <a name="error-code-secretdisabled"></a>エラー コード: SecretDisabled 

**説明:** 関連付けられている証明書が Key Vault で無効になっています。 

**解決方法:** Application Gateway に現在使用されている証明書のバージョンを再び有効にします。
1. Azure portal で、リンクされたキー コンテナーに移動します。
1. **[証明書]** ペインを開きます。
1. 必要な証明書名を選択してから、無効になっているバージョンを選択します。
1. 管理ページで、トグルを使用して、その証明書バージョンを有効にします。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="シークレットを再び有効にする方法を示すスクリーンショット。":::

[comment]: # (エラー コード 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>エラー コード: SecretDeletedFromKeyVault 

**説明:** 関連付けられている証明書が Key Vault から削除されています。 

**解決方法:** 削除された証明書を復旧するには、次の手順を実行します。 
1. Azure portal で、リンクされたキー コンテナーに移動します。
1. **[証明書]** ペインを開きます。
1. **[マネージド削除された証明書]** タブを使用して、削除された証明書を復旧します。

これに対して、その証明書オブジェクトが永続的に削除されている場合は、新しい証明書を作成し、その新しい証明書の詳細で Application Gateway を更新する必要があります。 Azure CLI または Azure PowerShell を使用して構成している場合は、バージョンのないシークレット識別子 URI を使用します。 この選択により、インスタンスで、更新されたバージョンの証明書 (存在する場合) を取得できるようになります。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Key Vault の削除された証明書を復旧する方法を示すスクリーンショット。":::

[comment]: # (エラー コード 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>エラー コード: UserAssignedManagedIdentityNotFound 

**説明:** 関連付けられているユーザー割り当てマネージド ID が削除されています。 

**解決方法:** その ID を再び使用するには、次の手順を実行します。
1. 以前使用されていたのと同じ名前を持つマネージド ID を同じリソース グループに再作成します。 リソース アクティビティ ログには、さらに多くの詳細が含まれています。 
1. ID を作成したら、 **[Application Gateway - アクセス制御 (IAM)]** に移動します。 その ID に、少なくとも **閲覧者** ロールを割り当てます。
1. 最後に、目的の Key Vault リソースに移動し、この新しいマネージド ID に **Get** シークレット アクセス許可を付与するようにそのアクセス ポリシーを設定します。 

詳細については、「[統合のしくみ](./key-vault-certs.md#how-integration-works)」を参照してください。

[comment]: # (エラー コード 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>エラー コード: KeyVaultHasRestrictedAccess

**説明:** Key Vault の制限されたネットワーク設定が存在します。 

**解決方法:** このエラーは、制限されたアクセスのために Key Vault ファイアウォールを有効にした場合に発生します。 次の手順に従うことによって、引き続き Key Vault の制限されたネットワークで Application Gateway を構成できます。
1. Key Vault で、 **[ネットワーク]** ペインを開きます。
1. **[ファイアウォールと仮想ネットワーク]** タブを選択し、 **[プライベート エンドポイントと選択されたネットワーク]** を選択します。
1. 次に、[仮想ネットワーク] を使用して、Application Gateway の仮想ネットワークとサブネットを追加します。 このプロセス中に、チェックボックスをオンにして 'Microsoft.KeyVault' サービス エンドポイントも構成します。
1. 最後に、 **[はい]** を選択して、信頼されたサービスが Key Vault のファイアウォールをバイパスできるようにします。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="制限されたネットワークのエラーを回避する方法を示すスクリーンショット。":::

[comment]: # (エラー コード 6)
### <a name="error-code-keyvaultsoftdeleted"></a>エラー コード: KeyVaultSoftDeleted 

**説明:** 関連付けられているキー コンテナーが論理的な削除の状態にあります。 

**解決方法:** Azure portal で、*キー コンテナー* を検索します。 **[サービス]** で、 **[キー コンテナー]** を選択します。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Key Vault サービスを検索する方法を示すスクリーンショット。":::

**[マネージド削除されたコンテナー]** を選択します。 ここから、削除された Key Vault リソースを見つけ、それを復旧することができます。
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="削除されたキー コンテナーを復旧する方法を示すスクリーンショット。":::

[comment]: # (エラー コード 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>エラー コード: CustomerKeyVaultSubscriptionDisabled 

**説明:** Key Vault のサブスクリプションが無効になっています。 

**解決方法:** Azure サブスクリプションは、さまざまな理由で無効になることがあります。 解決するために必要なアクションを実行するには、[無効になっている Azure サブスクリプションの再アクティブ化](../cost-management-billing/manage/subscription-disabled.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Application Gateway を引き続き使用する場合は、次のトラブルシューティングに関する記事が役立つことがあります。

- [Azure Application Gateway の Resource Health の概要](resource-health-overview.md)
- [Azure Application Gateway のセッション アフィニティに関する問題をトラブルシューティングする](how-to-troubleshoot-application-gateway-session-affinity-issues.md)
