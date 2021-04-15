---
title: Microsoft Graph アプリケーションを登録する
titleSuffix: Azure AD B2C
description: 必要な Graph API アクセス許可が付与されたアプリケーションを登録することにより、Microsoft Graph を使用した Azure AD B2C リソースの管理について準備します。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382465"
---
# <a name="register-a-microsoft-graph-application"></a>Microsoft Graph アプリケーションを登録する

[Microsoft Graph][ms-graph] を使用すると、顧客のユーザー アカウントやカスタム ポリシーなど、Azure AD B2C テナント内の多くのリソースを管理できます。 [Microsoft Graph API][ms-graph-api] を呼び出すスクリプトまたはアプリケーションを作成することによって、次のようなテナント管理タスクを自動化できます。

* 既存のユーザー ストアを Azure AD B2C テナントに移行する
* Azure DevOps で Azure Pipelines を使用してカスタム ポリシーをデプロイし、カスタム ポリシー キーを管理する
* 自分のページでユーザー登録をホストし、バックグラウンドで Azure AD B2C ディレクトリにユーザー アカウントを作成する
* アプリケーションの登録を自動化する
* 監査ログを取得する

次のセクションでは、Microsoft Graph API を使用して Azure AD B2C ディレクトリ内のリソースの管理を自動化するための準備について説明します。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API の相互作用モード

Microsoft Graph API を使用して Azure AD B2C テナントのリソースを管理する際は、次の 2 つの通信モードを使用できます。

* **対話型** - 一度だけ実行されるタスクに適切です。B2C テナントの管理者アカウントを使用して管理タスクを実行します。 このモードでは、Microsoft Graph API を呼び出す前に、管理者は自分の資格情報を使用してサインインする必要があります。

* **自動** - スケジュールされた、または継続的に実行されるタスクの場合、この方法では、管理タスクを実行するために必要なアクセス許可で構成したサービス アカウントが使用されます。 Azure AD B2C に "サービスアカウント" を作成するには、アプリケーションとスクリプトが "*アプリケーション (クライアント) ID*" と "**OAuth 2.0 のクライアント資格情報**" 付与を使用した認証に使用するアプリケーションを登録します。 この場合、アプリケーションは、前に説明した対話型の方法のように管理者ユーザーとしてではなく、それ自体として Microsoft Graph API を呼び出します。

**自動** の相互作用シナリオを有効にするには、次のセクションで示すアプリケーション登録を作成します。

OAuth 2.0 クライアント資格情報付与フローは現在 Azure AD B2C 認証サービスによって直接サポートされていませんが、Azure AD B2C テナント内のアプリケーション向けに Azure AD と Microsoft ID プラットフォーム/トークンエンド ポイントを使用して、クライアント資格情報フローを設定できます。 Azure AD B2C テナントは、Azure AD のエンタープライズ テナントと同じいくつかの機能を持っています。

## <a name="register-management-application"></a>管理アプリケーションを登録する

スクリプトとアプリケーションが [Microsoft Graph API][ms-graph-api] と対話して Azure AD B2C リソースを管理できるようにするには、必要な API アクセス許可を付与するアプリケーション登録を Azure AD B2C テナントに作成する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*managementapp1* と入力します。
1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオフにします。
1. **[登録]** を選択します。
1. アプリケーションの概要ページに表示されている **[アプリケーション (クライアント) ID]** を記録します。 この値は、後の手順で使用します。

## <a name="grant-api-access"></a>API アクセスの許可

アプリケーションから Microsoft Graph のデータにアクセスするには、登録されているアプリケーションに関連する[アプリケーションのアクセス許可](https://docs.microsoft.com/graph/permissions-reference)を付与します。 アプリケーションの有効なアクセス許可は、そのアクセス許可が暗示する完全なレベルの権限になります。 たとえば、Azure AD B2C テナント内のすべてのユーザーを *作成*、*読み取り*、*更新*、*削除* するには、**User.ReadWrite.All** アクセス許可を追加します。 

> [!NOTE]
> **User.ReadWrite.All** アクセス許可には、ユーザー アカウントのパスワードを更新する機能は含まれていません。 アプリケーションでユーザー アカウントのパスワードを更新する必要がある場合は、[ユーザー管理者ロールを付与](#optional-grant-user-administrator-role)します。 [ユーザー管理者](../active-directory/roles/permissions-reference.md#user-administrator)ロールを付与する場合、**User.ReadWrite.All** は必要ありません。 ユーザー管理者ロールには、ユーザーの管理に必要なすべてが含まれています。

アプリケーションに複数のアプリケーションのアクセス許可を与えることができます。 たとえば、アプリケーションで Azure AD B2C テナント内のグループを管理する必要がある場合は、**Group.ReadWrite.All** アクセス許可も追加します。 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[省略可能] ユーザー管理者ロールを付与する

アプリケーションまたはスクリプトでユーザーのパスワードを更新する必要がある場合は、*ユーザー管理者* ロールをアプリケーションに割り当ててください。 [ユーザー管理者](../active-directory/roles/permissions-reference.md#user-administrator)ロールには、アプリケーションに付与する固定されたアクセス許可のセットがあります。 

*ユーザー管理者* ロールを追加するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[ディレクトリ + サブスクリプション]** フィルターを使用して Azure AD B2C テナントに切り替えます。
1. **Azure AD B2C** を検索して選択します。
1. **[管理]** で **[ロールと管理者]** を選択します。
1. **[ユーザー管理者]** ロールを選択します。 
1. **[割り当ての追加]** を選択します。
1. **[選択]** テキスト ボックスに、前に登録したアプリケーションの名前または ID (たとえば、*managementapp1*) を入力します。 検索結果に表示されたら、アプリケーションを選択します。
1. **[追加]** を選択します。 アクセス許可が完全に反映されるまでに数分かかる場合があります。

## <a name="create-client-secret"></a>クライアント シークレットを作成する

アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを追加するには、次の手順のようにします。

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>次のステップ

管理アプリケーションを登録し、必要なアクセス許可を付与したので、お使いのアプリケーションとサービス (たとえば、Azure Pipelines) はその資格情報とアクセス許可を使用して、Microsoft Graph API と対話することができます。 

* [Azure AD からアクセス トークンを取得する](/graph/auth-v2-service#4-get-an-access-token)
* [アクセス トークンを使用して Microsoft Graph を呼び出す](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph でサポートされている B2C 操作](microsoft-graph-operations.md)
* [Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する](microsoft-graph-operations.md)
* [Azure AD Reporting API を使って監査ログを取得する](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
