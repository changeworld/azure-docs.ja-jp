---
title: Event Grid のカスタム トピック、ドメイン、およびパートナー名前空間にイベントを発行するクライアントを認証する
description: この記事では、Event Grid のカスタム トピック、ドメイン、およびパートナー名前空間にイベントを発行するクライアントを認証するさまざまな方法について説明します。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779589"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>Event Grid にイベントを発行する際のクライアント認証
Event Grid にイベントを発行するクライアントの認証は、次の方法でサポートされています。

- Azure Active Directory (Azure AD)
- アクセス キーまたは Shared Access Signature (SAS)

## <a name="authenticate-using-azure-active-directory-preview"></a>Azure Active Directory トークンを使用して認証する (プレビュー)
Event Grid リソースの Azure AD 統合により、リソースへのクライアントのアクセスをきめ細かく制御するための、Azure ロールベースのアクセス制御 (RBAC) が提供されます。 Azure RBAC を使用して、"セキュリティ プリンシパル" (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 このトークンを使用して、Event Grid リソース (トピック、ドメイン、またはパートナー名前空間) へのアクセス要求を承認できます。 詳細については、[Microsoft ID プラットフォームでの認証と承認](authenticate-with-active-directory.md)に関する記事を参照してください。


> [!IMPORTANT]
> Azure AD の ID を使用したユーザーやアプリケーションの認証および承認は、キー ベースの認証や Shared Access Signatures (SAS) 認証よりも優れたセキュリティと使いやすさを提供します。 Azure AD を使用すれば、コードに認証用のシークレットを格納する必要がないため、潜在的なセキュリティの脆弱性をリスクに晒すことはなくなります。 Azure Event Grid イベント発行アプリケーションで Azure AD を使用することを強くお勧めします。

> [!NOTE]
> Azure Event Grid による Azure AD の認証サポートがプレビューとしてリリースされました。 Kubernetes の Azure Event Grid では、Azure AD 認証はまだサポートされていません。 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>アクセス キーと Shared Access Signature を使用した認証
**アクセス キー** または **Shared Access Signature (SAS)** トークンを使用して、Azure Event Grid のトピック、ドメイン、またはパートナー名前空間にイベントを発行するクライアントを認証することができます。 詳細については、[アクセス キーまたは Shared Access Signature (SAS) の使用](authenticate-with-access-keys-shared-access-signatures.md)に関するページを参照してください。 
   

## <a name="next-steps"></a>次のステップ
この記事では、イベントを Event Grid に **発行する** (イベント イングレス) 際の認証について説明します。 イベントを **配信する** 際 (イベント エグレス) の Event Grid の認証については、[イベンド ハンドラーへのイベント配信の認証](security-authentication.md)に関する記事を参照してください。 

