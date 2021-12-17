---
title: パートナー センター申請 API のオンボードの概要
description: パートナー センター申請 API のオンボードの概要。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: e75b85ea7557e4eb6688e8a549e92820d580359b
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614747"
---
# <a name="partner-center-submission-api-onboarding"></a>パートナー センター申請 API のオンボード

パートナー センター申請 API を使用して、プログラムにより商業マーケットプレースのオファーをクエリし、申請を作成し、公開します。 この API は、お使いのアカウントで多くのプランを管理していて、これらのプランの申請プロセスを自動化および最適化したい場合に役立ちます。

## <a name="api-prerequisites"></a>API 前提条件

パートナー センターの申請 API には、以下のようないくつかのプログラム資産が必要です。

- Azure Active Directory アプリケーション
- Azure Active Directory (Azure AD) アクセス トークン

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>手順 1:パートナー センター申請 API を使用するための前提条件を満たす

パートナー センター申請 API を呼び出すコードを書き始める前に、次の前提条件が満たされていることを確認してください。

- 自分 (または自分の組織) に Azure AD ディレクトリがあり、自分がそのディレクトリに対する[グローバル管理者](../active-directory/roles/permissions-reference.md)のアクセス許可を持っている必要があります。 Microsoft 365 または Microsoft の他のビジネス サービスをすでに使用している場合、Azure AD ディレクトリをすでに所有しています。 そうでなければ、パートナー センターで[新しい Azure AD を無料で作成](manage-tenants.md#create-a-new-tenant)できます。
- [Azure AD アプリケーションをパートナー センター アカウントと関連付け](manage-aad-apps.md)、テナント ID、クライアント ID、キーを取得する必要があります。 これらは、Microsoft Store 申請 API への呼び出しに使用する Azure AD アクセス トークンを取得するために必要です。

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD アプリケーションをパートナー センター アカウントと関連付ける

パートナー センター申請 API を使用するには、Azure AD アプリケーションをパートナー センター アカウントと関連付け、アプリケーションのテナント ID とクライアント ID を取得し、キーを生成する必要があります。 Azure AD アプリケーションは、パートナー センター申請 API の呼び出し元であるアプリまたはサービスを表します。 API に渡す Azure AD アクセス トークンを取得するには、テナント ID、クライアント ID、キーが必要です。

> [!NOTE]
> このタスクは 1 度だけ実行する必要があります。 テナント ID、クライアント ID、キーがあれば、新しい Azure AD アクセス トークンを作成する必要がある度にそれらを再利用できます。

1. パートナー センターで、[組織のパートナー センター アカウントを組織の Azure AD ディレクトリに関連付けます](manage-tenants.md)。

1. パートナー センターの **[アカウント設定]** セクションの **[ユーザー]** ページから、パートナー センター アカウントの申請にアクセスするために使用するアプリまたはサービスを表す [Azure AD アプリケーションを追加](manage-aad-apps.md)します。 このアプリケーションに **マネージャー** ロールを確実に割り当てます。 アプリケーションがまだ Azure AD ディレクトリに存在していない場合は、パートナー センターで[新しい Azure AD アプリケーションを作成](manage-aad-apps.md#add-new-azure-ad-applications)します。

1. **[ユーザー]** ページに戻り、Azure AD アプリケーションの名前を選択してアプリケーション設定に移動し、**テナント ID** と **クライアント ID** の値をコピーしておきます。

1. **[新しいキーを追加]** を選択します。 次の画面で、**キー** の値をコピーしておきます。 このページを離れると、この情報にアクセスすることはできなくなります。 詳細については、「[Azure AD アプリケーションのキーを管理する](manage-aad-apps.md#manage-keys-for-an-azure-ad-application)」を参照してください。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>手順 2:Azure AD アクセス トークンを取得する

パートナー センター申請 API のメソッドを呼び出す前に、まずは API の各メソッドの **Authorization** ヘッダーに渡す Azure AD アクセス トークンを取得する必要があります。 アクセス トークンは、発行後 60 分が経過すると有効期限が切れます。 その後、更新することで、将来の API 呼び出しで使用できるようになります。

アクセス トークンを取得するには、「[クライアント資格情報を使用したサービス間の呼び出し](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)」の指示に従って、`HTTP POST` を `https://login.microsoftonline.com/<tenant_id>/oauth2/token` エンドポイントに送信します。 要求のサンプルを次に示します。

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

POST URI の tenant_id の値と client_id および client_secret のパラメーターには、前のセクションでパートナー センターから取得したアプリケーションのテナント ID、クライアント ID、キーを指定します。 resource パラメーターには、`https://api.partner.microsoft.com` を指定する必要があります。

### <a name="step-3-use-the-partner-center-submission-api"></a>ステップ 3: パートナー センター申請 API を使用する

Azure AD アクセス トークンを取得したら、パートナー センター申請 API でメソッドを呼び出します。 申請を作成または更新するには、通常はパートナー センター申請 API 内で複数のメソッドを特定の順序で呼び出します。 それぞれのシナリオと各メソッドの構文については、[Ingestion API](https://apidocs.microsoft.com/services/partneringestion/) Swagger を参照してください。

## <a name="next-steps"></a>次のステップ

- 上記の説明に従って、パートナー センター申請 API の使用を開始します
