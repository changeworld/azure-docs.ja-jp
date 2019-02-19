---
title: Azure Maps による認証 | Microsoft Docs
description: Azure Maps サービスを使用するための認証
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118812"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps による認証

Azure Maps には、要求の認証方法が 2 つあります。 共有キーまたは Azure Active Directory (Azure AD) には、Azure Maps に送信される各要求を承認する方法が別に用意されています。 この記事の目的は、両方の認証方法を説明して、認証の実装を支援することです。

## <a name="shared-key-authentication"></a>共有キー認証

共有キー認証は、Azure Maps への要求ごとに Azure Maps アカウントの生成キーを渡す処理に依存しています。  Azure Maps アカウントの作成時に 2 つのキーが生成されます。  Azure Maps サービスへの要求ごとに、サブスクリプション キーをパラメーターとして URL に追加する必要があります。

> [!Tip]
> キーを定期的に再生成することをお勧めします。 キーは 2 つ提供されるため、1 つのキーを使用して接続を保ったままもう 1 つのキーを再生成できます。 キーを再生成したら、新しいキーを使用するように、このアカウントにアクセスするすべてのアプリケーションを更新する必要があります。

キーを確認する方法については、[認証の詳細](https://aka.ms/amauthdetails)に関するページを参照してください。

## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory による認証 (プレビュー)

Azure Maps は、Azure Maps サービスに対する要求の認証用に [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) の統合を提供するようになりました。  Azure AD は、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) などの ID ベースの認証を提供して、ユーザー レベルまたはアプリケーション レベルのアクセス権を Azure Maps リソースに付与しています。 この記事の目的は、Azure Maps Azure AD の統合の概念とコンポーネントをわかりやすく説明することです。

## <a name="authentication-with-oauth-access-tokens"></a>OAuth アクセス トークンによる認証

Azure Maps は、Azure Maps アカウントを含む Azure サブスクリプションに関連付けられている Azure AD テナントの **OAuth 2.0** アクセス トークンを受け付けます。  Azure Maps は、次のトークンを受け付けます。

* Azure AD ユーザー 
* ユーザーによって委任されたアクセス許可を使用するサード パーティ製アプリケーション
* Azure リソースのマネージド ID

Azure Maps は、Azure Maps アカウントごとに `unique identifier (client ID)` を生成します。  クライアント ID を追加のパラメーターと組み合わせるときに、Azure AD から以下の値を指定してトークンを要求できます。

```
https://login.microsoftonline.com
```
Azure AD を構成し、Azure Maps のトークンを要求する方法の詳細については、[認証の管理方法](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)に関するページを参照してください。

Azure AD からのトークンの要求に関する一般的な情報については、[Azure Active Directory の認証の基本](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)に関するページを参照してください。

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>OAuth トークンを使用して Azure Map リソースを要求する

Azure AD からトークンを取得したら、次の 2 つの必須の要求ヘッダー セットを使用して要求を Azure Maps に送信できます。

| 要求ヘッダー    |    値    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` は、Azure Maps 認証ページに表示される Azure Maps アカウント ベースの GUID です。

以下は、OAuth トークンを使用した Azure Maps ルート要求の例です。

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

クライアント ID を確認する方法については、[認証の詳細](https://aka.ms/amauthdetails)に関するページを参照してください。

## <a name="control-access-with-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) によるアクセス制御

Azure AD の主な機能は、セキュリティで保護されたリソースへのアクセスを RBAC を介して制御することです。 Azure Maps アカウントを作成し、Azure AD テナント内に Azure Maps Azure AD アプリケーションを登録すると、Azure Map アカウント ポータル ページ内でユーザー、アプリケーション、または Azure リソースに対して RBAC を構成できるようになります。 

現在、Azure Maps は、Azure リソースのマネージド ID を使用して個々の Azure AD ユーザー、アプリケーション、または Azure サービスの読み取りアクセス制御をサポートしています。

![概念](./media/azure-maps-authentication/concept.png)

RBAC 設定を確認する方法については、[Azure Maps 用に RBAC を構成する方法](https://aka.ms/amrbac)に関するページを参照してください。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure Resources と Azure Maps のマネージド ID

[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) は、Azure Maps サービスへのアクセスを認可できる自動管理 ID を使用して、Azure サービス (Azure アプリ サービス、Azure Functions、Virtual Machines など) を提供します。  

## <a name="next-steps"></a>次の手順

* Azure AD と Azure Maps を使用してアプリケーションを認証する方法の詳細については、[認証の管理方法](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)に関するページを参照してください。

* Azure Maps、マップ コントロール、および Azure AD の認証の詳細については、[Azure AD と Azure Maps マップ コントロール](https://aka.ms/amaadmc)に関するページを参照してください。