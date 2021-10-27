---
title: Azure Service Bus の認証と承認 | Microsoft Docs
description: Shared Access Signature (SAS) 認証を使用して、アプリケーションを Service Bus に対して認証します。
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: 1b1cc20df861f657ed9fff4704862e9ee36965a2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002076"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus の認証と承認
Azure Service Bus リソースへのアクセスを認証して承認する方法は 2 つあります。Azure Activity Directory (Azure AD) と Shared Access Signature (SAS) です。 この記事では、この 2 種類のセキュリティ メカニズムの使用について詳しく説明します。 

## <a name="azure-active-directory"></a>Azure Active Directory
Service Bus リソースに関する Azure AD 統合により、リソースへのクライアントのアクセスをきめ細かく制御するための、Azure ロールベースのアクセス制御 (RBAC) が提供されます。 Azure RBAC を使用して、"セキュリティ プリンシパル" (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 このトークンは、Service Bus リソース (キュー、トピックなど) へのアクセス要求を承認するために使用できます。

Azure AD を使用した認証の詳細については、次の記事を参照してください。

- [マネージド ID による認証](service-bus-managed-service-identity.md)
- [アプリケーションからの認証](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) では、Azure AD を使用する OAuth 認証がサポートされています。

> [!IMPORTANT]
> Azure AD によって返された OAuth 2.0 トークンを使用するユーザーまたはアプリケーションの承認では、Shared Access Signatures (SAS) よりも優れたセキュリティが提供され、使いやすくなります。 Azure AD を使用すれば、コードにトークンを格納する必要がなく、潜在的なセキュリティ脆弱性のリスクはありません。 可能な場合は、Azure Service Bus アプリケーションで Azure AD を使用することをお勧めします。 

## <a name="shared-access-signature"></a>共有アクセス署名
[SAS 認証](service-bus-sas.md)により、特定の権限で Service Bus リソースにアクセスできるようになります。 Service Bus の SAS 認証には、Service Bus リソースに対する関連した権限を使用した暗号化キーの構成が伴います。 これにより、クライアントは SAS トークンを提示してリソースへのアクセス権を取得できます。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限から成ります。

SAS のキーは Service Bus 名前空間で構成できます。 このキーは、その名前空間内のすべてのメッセージング エンティティに適用されます。 Service Bus のキューとトピックでキーを構成することもできます。 SAS は [Azure リレー](../azure-relay/relay-authentication-and-authorization.md)でもサポートされます。

SAS を使用するために、名前空間、キュー、またはトピックに共有アクセス承認規則を構成できます。 この規則は次の要素で構成されます。

* *KeyName*: ルールを識別します。
* *PrimaryKey*: SAS トークンの署名または検証に使用される暗号化キー。
* *SecondaryKey*: SAS トークンの署名または検証に使用される暗号化キー。
* *Rights*: 付与されている **リッスン**、**送信、** 、または **管理** 権限を表します。

名前空間レベルで構成された承認規則では、対応するキーを使用して署名されたトークンによって、クライアントの名前空間内のすべてのエンティティへのアクセス権を付与できます。 Service Bus の名前空間、キュー、またはトピックでは、このような承認規則を最大 12 個構成できます。 既定では、すべての権限を持つ共有アクセス承認規則は、最初のプロビジョニング時にすべての名前空間に対して構成されます。

エンティティにアクセスするには、クライアントに、特定の共有アクセス承認規則を使用して生成された SAS トークンが必要です。 SAS トークンの生成には、承認規則に関連付けられた暗号化キーによって、アクセスが要求されるリソース URI と、有効期限で構成されるリソース文字列の HMAC-SHA256 を使用します。

Service Bus の SAS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 SAS には、共有アクセス承認規則のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、SAS 接続文字列がサポートされています。


## <a name="next-steps"></a>次のステップ
Azure AD を使用した認証の詳細については、次の記事を参照してください。

- [マネージド ID による認証](service-bus-managed-service-identity.md)
- [アプリケーションからの認証](authenticate-application.md)

SAS を使用した認証の詳細については、次の記事を参照してください。

- [SAS による認証](service-bus-sas.md)
