---
title: 認証方法 | Microsoft Azure Maps
description: この記事では、Azure Active Directory (Azure AD) と共有キー認証について説明します。 どちらも Microsoft Azure Maps サービスに使用されます。 Azure Maps のサブスクリプション キーを取得する方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 2bcc2d4c92e903b723bffa8461a8a1a10534d3e4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025624"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps による認証

Azure Maps には、要求の認証方法が 2 つあります。共有キー認証と Azure Active Directory 認証です。 この記事では、実装の参考になるように、これらの認証方法について説明します。

## <a name="shared-key-authentication"></a>共有キー認証

共有キー認証では、Azure Maps への要求ごとに Azure Maps アカウントによって生成されたキーを渡します。 Azure Maps サービスへの要求ごとに、"*サブスクリプション キー*" をパラメーターとして URL に追加する必要があります。 Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 共有キー認証を使用して Azure Maps を呼び出す場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 2 次キーは、キーのローリング変更などのシナリオで使用できます。  

Azure portal でキーを表示する方法について詳しくは、[認証の管理](https://aka.ms/amauthdetails)に関する記事をご覧ください。

> [!Tip]
> キーを定期的に再生成することをお勧めします。 キーは 2 つ提供されるため、1 つのキーを使用して接続を保持したまま、もう 1 つのキーを再生成することが可能です。 キーを再生成したら、新しいキーを使用するように、アカウントにアクセスするすべてのアプリケーションを更新する必要があります。



## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory による認証 (プレビュー)

Azure Maps では、[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) を使用した Azure Maps サービスの要求認証が提供されるようになりました。 Azure AD には、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) を含む ID ベースの認証が用意されています。 RBAC は、Azure Maps リソースに対するユーザーレベル、グループレベル、またはアプリケーションレベルのアクセスを付与するために使用されます。 次のセクションでは、Azure Maps と Azure AD との統合の概念と構成要素について説明します。
## <a name="authentication-with-oauth-access-tokens"></a>OAuth アクセス トークンによる認証

Azure Maps では、Azure Maps アカウントを含む Azure サブスクリプションに関連付けられている Azure AD テナントの **OAuth 2.0** アクセス トークンを受け付けます。 Azure Maps は、次のトークンを受け付けます。

* Azure AD ユーザー
* ユーザーによって委任されたアクセス許可を使用するパートナー アプリケーション
* Azure リソースのマネージド ID

Azure Maps では、Azure Maps アカウントごとに*一意の識別子 (クライアント ID)* を生成します。 このクライアント ID を追加のパラメーターと組み合わせると、Azure AD からトークンを要求できます。 トークンを要求するには、実際の Azure 環境に基づいて次の表の値を指定する必要があります。

| Azure 環境   | Azure AD トークン エンドポイント |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Azure AD を構成して Azure Maps のトークンを要求する方法について詳しくは、「[Azure Maps での認証の管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)」をご覧ください。

Azure AD からのトークンの要求について詳しくは、「[認証とは](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>OAuth トークンを使用して Azure Map リソースを要求する

Azure AD からトークンを受信すると、次の必須の要求ヘッダー セットを使用して要求が Azure Maps に送信されます。

| 要求ヘッダー    |    Value    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 承認     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` は、Azure Maps 認証ページに表示される Azure Maps アカウント ベースの GUID です。

OAuth トークンを使用する Azure Maps ルート要求の例を次に示します。

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

クライアント ID を表示する方法については、「[認証の詳細を表示する](https://aka.ms/amauthdetails)」をご覧ください。

## <a name="control-access-with-rbac"></a>RBAC を使用してアクセスを制御する

Azure AD で、RBAC を使用して、セキュリティで保護されたリソースへのアクセスを制御します。 Azure Maps アカウントを設定し、Azure Maps Azure AD テナントを登録します。 Azure Maps では、Azure リソースのマネージド ID を使用して個々の Azure AD ユーザー、グループ、アプリケーション、Azure リソース、および Azure サービスの読み取りアクセス制御をサポートしています。 Azure Maps ポータル ページで、目的のロールの RBAC を設定できます。

![Azure Maps データ閲覧者 (プレビュー)](./media/azure-maps-authentication/concept.png)

RBAC 設定を確認する方法については、[Azure Maps RBAC を構成する方法](https://aka.ms/amrbac)に関するページをご覧ください。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure リソースと Azure Maps のマネージド ID

[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) によって、自動的に管理される ID が Azure サービスに提供されます。これを使って、Azure Maps サービスへのアクセスを承認できます。 マネージド ID の例を次に示します。Azure App Service、Azure Functions、Azure Virtual Machines。

## <a name="next-steps"></a>次のステップ

* Azure AD と Azure Maps を使用してアプリケーションを認証する方法について詳しく確認するには、「[Azure Maps での認証の管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)」をご覧ください。

* Azure Maps マップ コントロールおよび Azure AD の認証について詳しく確認するには、「[Azure Maps のマップ コントロールを使用する](https://aka.ms/amaadmc)」をご覧ください。
