---
title: Azure AD の認証と各国のクラウド | Azure
titleSuffix: Microsoft identity platform
description: 各国のクラウドのアプリ登録と認証エンドポイントについて説明します。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/03/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: bc62e3a4ae32cf4b2d0a63dd15bdab24ac490d1e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564539"
---
# <a name="national-clouds"></a>各国のクラウド

各国のクラウドは、物理的に分離された Azure のインスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件が地理的な境界内で確実に守られるように設計されています。

グローバル Azure クラウドを含め、Azure Active Directory (Azure AD) は次の各国のクラウドにデプロイされています。

- Azure Government
- Azure China 21Vianet
- Azure Germany ([2021 年 10 月 29 日に終了](https://www.microsoft.com/cloud-platform/germany-cloud-regions))。 [Azure Germany の移行](#azure-germany-microsoft-cloud-deutschland)について説明します。

個々の各国のクラウドとグローバル Azure クラウドは、クラウド _インスタンス_ です。 各クラウド インスタンスはそれぞれ独立しており、独自の環境と _エンドポイント_ を持っています。 クラウド固有のエンドポイントには、OAuth 2.0 アクセス トークンと OpenID Connect ID トークン要求エンドポイントに加え、Azure portal など、アプリの管理とデプロイのための URL が含まれています。

アプリを開発する場合は、アプリケーションをデプロイするクラウド インスタンスのエンドポイントを使用します。

## <a name="app-registration-endpoints"></a>アプリ登録エンドポイント

各国のクラウドには、それぞれ別個の Azure portal があります。 各国のクラウドでアプリケーションを Microsoft ID プラットフォームに統合するには、その環境に固有の各 Azure Portal で個別にアプリケーションを登録する必要があります。

次の表は、各国のクラウドごとにアプリケーションを登録するために使用される Azure AD エンドポイントのベース URL の一覧を示しています。

| 各国のクラウド                          | Azure portal エンドポイント      |
| --------------------------------------- | -------------------------- |
| 米国政府の Azure portal          | `https://portal.azure.us`  |
| 21Vianet が運営する中国の Azure portal | `https://portal.azure.cn`  |
| Azure portal (グローバル サービス)           | `https://portal.azure.com` |

## <a name="application-endpoints"></a>アプリケーション エンドポイント

アプリケーションの認証エンドポイントは Azure Portal で見つけることができます。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[管理]** で、 **[アプリの登録]** を選択し、上部のメニューで **[エンドポイント]** を選択します。

   Azure AD テナントに登録されたアプリケーションの認証エンドポイントが一覧表示された **[エンドポイント]** ページが表示されます。

   使用している認証プロトコルに対応するエンドポイントを **アプリケーション (クライアント) ID** と共に使用して、アプリケーション固有の認証要求を作成します。

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 認証エンドポイント

各国のクラウドはすべて、各環境で別個にユーザーを認証し、別個の認証エンドポイントを備えています。

次の表は、各国のクラウドごとにトークンを取得するために使用される Azure AD エンドポイントのベース URL の一覧を示しています。

| 各国のクラウド                      | Azure AD 認証エンドポイント           |
| ----------------------------------- | ------------------------------------------ |
| 米国政府向け Azure AD          | `https://login.microsoftonline.us`         |
| 21Vianet が運営する Azure AD China | `https://login.partner.microsoftonline.cn` |
| Azure AD (グローバル サービス)           | `https://login.microsoftonline.com`        |

適切なリージョン固有のベース URL を使用して、Azure AD 承認またはトークン エンドポイントへの要求を構成できます。 たとえば、グローバル Azure の場合:

- 承認共通エンドポイントは `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` です。
- トークン共通エンドポイントは `https://login.microsoftonline.com/common/oauth2/v2.0/token` です。

シングルテナント アプリケーションの場合は、前の URL にある "common" をテナント ID またはテナント名に置き換えます。 たとえば `https://login.microsoftonline.com/contoso.com` です。

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Germany (Microsoft Cloud Deutschland)

アプリケーションを Azure Germany から移行していない場合は、[Azure Germany からの移行に関する Azure Active Directory 情報](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)に従って開始してください。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

各国のクラウドの環境で Microsoft Graph API を呼び出す方法については、[各国のクラウドのデプロイでの Microsoft Graph](/graph/deployments)に関するページを参照してください。

グローバル Azure クラウドのサービスや機能の中には、各国のクラウドのような他のクラウド インスタンスでは利用できないものがあります。

特定のクラウド インスタンスで使用できるサービスと機能については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)」を参照してください。

Microsoft ID プラットフォームを使用してアプリケーションを構築する方法については、[SPA (シングルページ アプリケーション) で認証コード フローする方法に関するチュートリアル](tutorial-v2-angular-auth-code.md)を参照してください。 具体的には、このアプリはユーザーをサインインさせ、Microsoft Graph API を呼び出すためのアクセス トークンを取得します。

## <a name="next-steps"></a>次のステップ

「[国内クラウド環境で Microsoft Authentication Library (MSAL) を使用する](msal-national-cloud.md)」方法について学習する。

国内クラウドのドキュメント:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Germany (2021 年 10 月 29 日に終了)](../../germany/index.yml)
