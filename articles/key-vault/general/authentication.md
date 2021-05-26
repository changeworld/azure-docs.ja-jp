---
title: Azure Key Vault に対する認証
description: Azure Key Vault に対して認証を行う方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 1fa4f4b4b9b56b5acc3a3d3d9e75177889a4a8af
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479237"
---
# <a name="authentication-in-azure-key-vault"></a>Azure Key Vault の認証

Key Vault による認証は、特定の **セキュリティ プリンシパル** の ID の認証を行う [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) と連携して機能します。

セキュリティ プリンシパルは、Azure リソースへのアクセスを要求するユーザー、グループ、サービス、またはアプリケーションを表すオブジェクトです。 すべてのセキュリティ プリンシパルには、Azure によって一意の **オブジェクト ID** が割り当てられます。

* **ユーザー** セキュリティ プリンシパルでは、Azure Active Directory 内にプロファイルを持つ個人が示されます。

* **グループ** セキュリティ プリンシパルでは、Azure Active Directory 内に作成されたユーザーのセットが示されます。 グループに割り当てられたすべてのロールまたはアクセス許可は、グループ内のすべてのユーザーに付与されます。

* **サービス プリンシパル** は、アプリケーションまたはサービス、つまりユーザーまたはグループではなくコードを示すセキュリティ プリンシパルの一種です。 サービス プリンシパルのオブジェクト ID は **クライアント ID** と呼ばれ、ユーザー名のように機能します。 サービス プリンシパルの **クライアント シークレット** は、パスワードのように機能します。

アプリケーションの場合、サービス プリンシパルを取得するには次の 2 つの方法があります。

* 推奨: アプリケーションに対してシステム割り当ての **マネージド ID** を有効にします。

    Azure では、マネージ ID を使用することにより、アプリケーションのサービス プリンシパルが内部的に管理され、他の Azure サービスでのアプリケーションの認証が自動的に行われます。 マネージド ID は、さまざまなサービスにデプロイされたアプリケーションで使用できます。

    詳細については、[マネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関する記事を参照してください。 また、[マネージド ID がサポートされている Azure サービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)に関する記事も参照してください。それには、特定のサービス (App Service、Azure Functions、Virtual Machines など) でマネージド ID を有効にする方法について説明されている記事へのリンクが含まれます。

* マネージド ID を使用できない場合は、Azure AD テナントにアプリケーションを **登録** します。詳細については、[クイックスタート: Azure ID プラットフォームでのアプリケーションの登録](../../active-directory/develop/quickstart-register-app.md)に関する記事を参照してください。 また、登録によって、すべてのテナントでそのアプリを示す 2 つ目のアプリケーション オブジェクトも作成されます。

## <a name="configure-the-key-vault-firewall"></a>Key Vault ファイアウォールを構成する

Key Vault では、パブリック IP アドレスを使用してリソースにアクセスすることが既定で許可されます。 セキュリティを強化するため、特定の IP 範囲、サービス エンドポイント、仮想ネットワーク、またはプライベート エンドポイントにアクセスを制限することもできます。

詳細については、「[ファイアウォールの向こう側にある Azure Key Vault へのアクセス](./access-behind-firewall.md)」を参照してください。

## <a name="the-key-vault-request-operation-flow-with-authentication"></a>認証を伴う Key Vault 要求操作フロー

Key Vault の認証は、Key Vault に対するあらゆる要求操作の過程で行われます。 トークンは、いったん取得されると、後続の呼び出しで再利用することができます。 認証フローの例:

1. Azure AD に対して認証を行うためのトークンを要求します。その例を次に示します。
    * 仮想マシンや App Service アプリケーションなど、マネージド ID を割り当てられた Azure リソースが、REST エンドポイントに接続してアクセス トークンを取得します。
    * ユーザーが、ユーザー名とパスワードを使用して Azure portal にログインします。

1. Azure AD に対する認証が成功すると、セキュリティ プリンシパルに OAuth トークンが付与されます。

1. Key Vault のエンドポイント (URI) を通して Key Vault REST API を呼び出します。

1. Key Vault ファイアウォールにより、次の条件がチェックされます。 いずれかの条件が満たされた場合、呼び出しは許可されます。 それ以外の場合、呼び出しはブロックされ、不許可の応答が返されます。

    * ファイアウォールは無効になっており、Key Vault のパブリック エンドポイントにパブリック インターネットから到達できます。
    * 呼び出し元は [Key Vault の信頼できるサービス](./overview-vnet-service-endpoints.md#trusted-services)であり、ファイアウォールをバイパスすることができます。
    * 呼び出し元は、IP アドレス、仮想ネットワーク、またはサービス エンドポイントによってファイアウォールのリストに登録されています。
    * 呼び出し元は、構成されたプライベート リンク接続を通して Key Vault に到達できます。    

1. ファイアウォールで呼び出しが許可された場合、Key Vault によって Azure AD が呼び出されて、セキュリティ プリンシパルのアクセス トークンの検証が行われます。

1. Key Vault により、要求された操作に必要なアクセス許可をサービス プリンシパルが持っているかどうかが確認されます。 そうでない場合、Key Vault からは禁止応答が返されます。

1. Key Vault により、要求された操作が実行されて、結果が返されます。

次の図は、アプリケーションが Key Vault の "シークレットの取得" API を呼び出すプロセスを示したものです。

![Azure Key Vault の認証フロー](../media/authentication/authentication-flow.png)

> [!NOTE]
> シークレット、証明書、キーの Key Vault SDK クライアントは、アクセス トークンなしで Key Vault を追加で呼び出すので、テナント情報を取得する際に 401 応答が返されます。 詳細については、「[認証、要求、応答](authentication-requests-and-responses.md)」を参照してください。

## <a name="authentication-to-key-vault-in-application-code"></a>アプリケーション コードでの Key Vault に対する認証

Key Vault SDK には Azure ID クライアント ライブラリが使用されており、Key Vault に対する環境の垣根を越えたシームレスな認証が同じコードで実現されます。

**Azure ID クライアント ライブラリ**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|   

ベスト プラクティスと開発者の例について詳しくは、「[コードでの Key Vault に対する認証](developers-guide.md#authenticate-to-key-vault-in-code)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Key Vault 開発者ガイド](developers-guide.md)
- [Azure portal を使用して Key Vault アクセス ポリシーを割り当てる](assign-access-policy-portal.md)
- [Key Vault に対する Azure RBAC ロールを割り当てる](rbac-guide.md)
- [Key Vault のアクセス ポリシーのトラブルシューティング](troubleshooting-access-issues.md)
- [Key Vault REST API のエラー コード](rest-error-codes.md)

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
