---
title: Application Gateway の一般的な Key Vault エラー
titleSuffix: Azure Application Gateway
description: この記事は、Key Vault に関連する問題を特定し、それらを解決して Application Gateway の円滑な運用を行うために役立ちます。
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748798"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>Application Gateway の一般的な Key Vault エラー

このトラブルシューティング ガイドは、Key Vault エラー コード、その原因、および問題の原因となっている関連する Key Vault リソースの詳細を理解するのに役立ちます。 また、このような構成の誤りを解決するステップ バイ ステップ ガイドも含まれています。

> [!NOTE]
> Application Gateway の Key Vault の診断のログは、4 時間間隔で生成されます。 そのため、構成を修正した後も診断でエラーが引き続き表示される場合は、ログが更新されるのを待つ必要がある場合があります。

> [!TIP]
> バージョンレスのシークレット識別子を使用することをお勧めします。 これにより、Key Vault で新しいバージョンが利用可能な場合は、Application Gateway は証明書を自動的にローテーションします。  バージョンのないシークレット URI の例: `https://myvault.vault.azure.net/secrets/mysecret/`。

### <a name="list-of-error-codes-and-their-details"></a>エラー コードとその詳細の一覧

[comment]: # (エラー コード 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) エラー コード:** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**説明:** 関連付けられたユーザー割り当てマネージド ID に GET アクセス許可が付与されていません。 

**解決方法:** Key Vault のアクセス ポリシーを構成し、関連付けられた ユーザー割り当てマネージド ID にシークレットに対する GET アクセス許可を付与します。 
1. Azure portal で、リンクされた Key Vault に移動します
2. [アクセス ポリシー] ブレードを開きます
3. アクセス許可モデルの [コンテナー アクセス ポリシー] を選択します
4. 該当のユーザー割り当てマネージド ID でシークレットで "Get" アクセス許可を選択します
5. 構成を保存する


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text="ユーザー割り当て ID に Key Vault の Get アクセス許可がありません。":::

Key Vault のアクセス ポリシーに関する完全なガイドについては、この[記事](../key-vault/general/assign-access-policy-portal.md)を参照してください。
</br></br>



[comment]: # (エラー コード 2)
#### <a name="2-error-code-secretdisabled"></a>**2) エラー コード:** SecretDisabled 

**説明:** Key Vault で、関連付けられている証明書が無効になっています。 

**解決方法:** Application Gateway に現在使用されている証明書のバージョンを再び有効にします。
1. Azure portal で、リンクされた Key Vault に移動します
2. [証明書] ブレードを開きます
3. 必要な証明書名をクリックし、無効なバージョンをクリックします
4. 管理ページのトグルを使用して、その証明書のバージョンを有効にします

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="シークレットを再び有効にします。":::
</br></br>


[comment]: # (エラー コード 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) エラー コード:** SecretDeletedFromKeyVault 

**説明:** 関連付けられている証明書が Key Vault から削除されています。 

**解決方法:** Key Vault 内の削除された証明書オブジェクトは、論理的な削除の回復機能を使用して復旧できます。 削除された証明書を復旧するには、 
1. Azure portal で、リンクされた Key Vault に移動します
2. [証明書] ブレードを開きます
3. [Manage Deleted Certificates]\(削除された証明書の管理\) タブを使用して、削除された証明書を復旧します。

一方、証明書オブジェクトが完全に削除された場合は、新しい証明書を作成し、新しい証明書の詳細で Application Gateway を更新する必要があります。 Azure CLI または Azure PowerShell を使用して構成する場合は、バージョンレスのシークレット識別子 URI を使用し、インスタンスが証明書の更新されたバージョン (存在する場合) を取得できるようにすることをお勧めします。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Key Vault で削除された証明書を復旧します。":::
</br></br>


[comment]: # (エラー コード 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) エラー コード:** UserAssignedManagedIdentityNotFound 

**説明:** 関連付けられたユーザー割り当てマネージド ID が削除されています。 

**解決策:** 以下のガイダンスに従ってこの問題を解決します。
1. 以前と同じリソース グループの下で、以前と同じ名前を使用して、マネージド ID を再作成します。 詳細については、リソースのアクティビティ ログを参照してください。 
2. 作成したら、その新しいマネージド ID に、Application Gateway - Access Control (IAM) の下で少なくとも閲覧者ロールを割り当てる必要があります。
3. 最後に、目的の Key Vault リソースに移動し、そのアクセス ポリシーを設定して、この新しいマネージド ID に対する GET シークレット アクセス許可を付与します。 

[詳細情報](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (エラー コード 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) エラー コード:** KeyVaultHasRestrictedAccess

**説明:** Key Vault の制限付きネットワーク設定。 

**解決方法:** アクセスを制限するために、Key Vault ファイアウォールを有効にするとこの問題が発生します。 次の方法で、Key Vault の制限されたネットワークで Application Gateway を引き続き構成できます。
1. Key Vault の [ネットワーク] ブレードで
2. [ファイアウォールと仮想ネットワーク] タブで [Private endpoint and selected networks]\(プライベート エンドポイントと選択したネットワーク\) を選択します
3. 最後に [はい] を選択し、信頼されたサービスが Key Vault のファイアウォールをバイパスできるようにします。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Key Vaultアクセスが制限されています。":::
</br></br>


[comment]: # (エラー コード 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) エラー コード:** KeyVaultSoftDeleted 

**説明:** 関連付けられた Key Vault が論理的な削除の状態にあります。 

**解決策:** 論理的に削除された Key Vault の復旧は容易です。 Azure portal で、Key Vault のサービス ページにアクセスします。

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Key Vault サービスを検索します。":::
</br></br>
[Manage Deleted Vaults]\(削除されたコンテナーの管理\) タブをクリックします。ここから、削除された Key Vault リソースを見つけて復旧できます。
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="論理的な削除を使用して、削除された Key Vault を復旧します。":::
</br></br>


[comment]: # (エラー コード 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) エラー コード:** CustomerKeyVaultSubscriptionDisabled 

**説明:** Key Vault のサブスクリプションが無効になっています。 

**解決方法:** さまざまな理由により、Azure サブスクリプションが無効化されることがあります。 [無効な Azure サブスクリプションを再度有効にする](../cost-management-billing/manage/subscription-disabled.md)ためのガイドを参照し、必要な作業を実行してください。
</br></br>



