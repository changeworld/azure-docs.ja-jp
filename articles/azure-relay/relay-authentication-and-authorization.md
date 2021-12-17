---
title: Azure Relay の認証と承認 | Microsoft Docs
description: この記事では、Azure Relay サービスでの Shared Access Signature (SAS) 認証の概要について説明します。
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741225"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay の認証と承認
Azure Relay リソースへのアクセスを認証して承認する方法は 2 つあります。Azure Activity Directory (Azure AD) と Shared Access Signature (SAS) です。 この記事では、この 2 種類のセキュリティ メカニズムの使用について詳しく説明します。

## <a name="azure-active-directory-preview"></a>Azure Active Directory (プレビュー)
Azure AD と Azure Relay リソースとの統合により、リソースへのクライアントのアクセスをきめ細かに制御するための、Azure ロールベースのアクセス制御 (Azure RBAC) が提供されています。 Azure RBAC を使用して、"セキュリティ プリンシパル" (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 トークンは、Azure Relay リソースにアクセスする要求を承認するために使用できます。

Azure AD を使用した認証の詳細については、次の記事を参照してください。
- [マネージド ID による認証](authenticate-managed-identity.md)
- [Azure Active Directory アプリケーションから認証する](authenticate-application.md)

> [!IMPORTANT]
> Azure AD によって返された OAuth 2.0 トークンを使用するユーザーまたはアプリケーションの承認では、Shared Access Signatures (SAS) よりも優れたセキュリティが提供され、使いやすくなります。 Azure AD を使用すれば、コードにトークンを格納する必要がなく、潜在的なセキュリティ脆弱性のリスクはありません。 可能な場合は、Azure Relay アプリケーションで Azure AD を使用することをお勧めします。

### <a name="built-in-roles"></a>組み込みのロール
Azure Relay の場合、名前空間およびそれに関連するすべてのリソースの Azure portal および Azure リソース管理 API による管理は、Azure RBAC モデルを使って既に保護されています。 Azure では、Relay 名前空間へのアクセスを承認するための次の Azure 組み込みロールが提供されています。

| Role | 説明 | 
| ---- | ----------- | 
| [Azure Relay 所有者](../role-based-access-control/built-in-roles.md#azure-relay-owner) | このロールを使用して、Azure Relay リソースへの **フル** アクセスを許可します。 |
| [Azure Relay リスナー](../role-based-access-control/built-in-roles.md#azure-relay-listener) | このロールを使用して、Azure Relay リソースへの **リッスンおよびエンティティ読み取り** アクセスを許可します。 |
| [Azure Relay 送信者](../role-based-access-control/built-in-roles.md#azure-relay-sender) | このロールを使用して、Azure Relay リソースへの **送信およびエンティティ読み取り** アクセスを許可します。 | 


## <a name="shared-access-signature"></a>Shared Access Signature
アプリケーションは、Shared Access Signature (SAS) 認証を使用して Azure Relay に対して認証できます。 アプリケーションは SAS 認証により、Relay 名前空間で構成されたアクセス キーを使用して Azure Relay サービスへの認証を行うことができます。 次に、このキーを使用して、クライアントがリレー サービスに対する認証に使用できる Shared Access Signature トークンを生成できます。

[SAS 認証](../service-bus-messaging/service-bus-sas.md)により、特定の権限で Azure Relay リソースにアクセスできるようになります。 SAS 認証を使用するには、リソースに対する関連した権限を使用して暗号化キーを構成する必要があります。 これにより、クライアントは SAS トークンを提示してリソースへのアクセス権を取得できます。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限から成ります。

SAS のキーは Relay 名前空間で構成できます。 Service Bus メッセージングとは異なり、[Relay ハイブリッド接続](relay-hybrid-connections-protocol.md)では、許可されていない送信者や匿名の送信者がサポートされます。 ポータルの次のスクリーンショットに示すように、エンティティの作成時に、そのエンティティの匿名アクセスを有効にすることができます。

![[ハイブリッド接続の作成] というタイトルのダイアログ ボックスには、[名前] テキスト ボックスと、[クライアント認証が必要] というラベルの付いたチェック ボックス (オンになっている) があります。][0]

SAS を使用するには、Relay 名前空間で、次のプロパティで構成される [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトを構成します。

* *KeyName* 。
* *PrimaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *SecondaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *Rights* 。

名前空間レベルで構成された承認規則では、対応するキーを使用して署名されたトークンによって、クライアントの名前空間内のすべてのリレー接続へのアクセス権を付与できます。 Relay 名前空間では、このような承認規則を最大 12 個構成できます。 既定では、すべての権限を保有する [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) は、最初のプロビジョニング時にすべての名前空間に対して構成されます。

エンティティにアクセスするには、クライアントには、特定の [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)を使用して生成された SAS トークンが必要です。 SAS トークンの生成には、承認規則に関連付けられた暗号化キーによって、アクセスが要求されるリソース URI と、有効期限で構成されるリソース文字列の HMAC-SHA256 を使用します。

Azure Relay の SAS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 SAS には、 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、SAS 接続文字列がサポートされています。

## <a name="next-steps"></a>次のステップ

- SAS の詳細については、「[Shared Access Signature による Service Bus の認証](../service-bus-messaging/service-bus-sas.md)」を引き続きお読みください。
- ハイブリッド接続機能の詳細については、[Azure Relay ハイブリッド接続プロトコル ガイド](relay-hybrid-connections-protocol.md)を参照してください。
- Service Bus メッセージングの認証と承認についての対応する情報については、「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。 

[0]: ./media/relay-authentication-and-authorization/hcanon.png