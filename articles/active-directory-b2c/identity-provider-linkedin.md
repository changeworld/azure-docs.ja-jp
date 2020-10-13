---
title: LinkedIn アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで LinkedIn アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259460"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して LinkedIn アカウントでのサインアップおよびサインインを設定する

## <a name="create-a-linkedin-application"></a>LinkedIn アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](authorization-code-flow.md)として LinkedIn アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ LinkedIn アカウントを持っていない場合は、[https://www.linkedin.com/](https://www.linkedin.com/) でサインアップできます。

1. LinkedIn アカウントの資格情報を使用して、[LinkedIn 開発者の Web サイト](https://www.developer.linkedin.com/)にサインインします。
1. **[マイ アプリ]** を選択してから、 **[アプリの作成]** をクリックします。
1. **[アプリ名]** 、 **[LinkedIn ページ]** 、 **[プライバシー ポリシーの URL]** 、 **[アプリのロゴ]** を入力します。
1. LinkedIn **API の使用条件**に同意し、 **[アプリの作成]** をクリックします。
1. **[Auth]\(認証\)** タブを選択します。 **[認証キー]** の下にある **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして LinkedIn を構成するには、両方の値が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
1. **[アプリの承認済みのリダイレクト URL]** の横にある編集用の鉛筆を選択してから、 **[リダイレクト URL を追加する]** を選択します。 「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力し、`your-tenant-name` をお使いのテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。 **[Update]\(更新\)** を選択します。
2. 既定では、サインインに関連するスコープに LinkedIn アプリが承認されていません。 レビューを要求するには、 **[製品]** タブを選択してから、 **[LinkedIn でサインインする]** を選択します。 レビューが完了すると、必要なスコープがアプリケーションに追加されます。
   > [!NOTE]
   > **[OAuth 2.0 のスコープ]** セクションの **[Auth] (承認)** タブで、アプリに現在許可されているスコープを確認できます。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>ID プロバイダーとして LinkedIn アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、**[LinkedIn]** を選択します。
1. **[名前]** を入力します。 たとえば、「*LinkedIn*」とします。
1. **[クライアント ID]** には、前に作成した LinkedIn アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。

## <a name="migration-from-v10-to-v20"></a>v1.0 から v2.0 に移行する

LinkedIn では最近、[API が v1.0 から v2.0 に更新](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)されました。 移行の一環として、Azure AD B2C がサインアップ中に取得できるのは LinkedIn ユーザーのフル ネームのみとなります。 メール アドレスが、サインアップ時に収集される属性の 1 つである場合、ユーザーがメール アドレスを手動で入力して検証する必要があります。
