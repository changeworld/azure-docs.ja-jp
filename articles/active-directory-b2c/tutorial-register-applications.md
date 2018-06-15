---
title: 'チュートリアル: アプリケーションを登録して Azure Active Directory B2C を使用したサインアップとサインインを有効にする | Microsoft Docs'
description: Azure Portal を使用して、Azure AD B2C テナントを作成し、それにアプリケーションを登録します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebfe4a2d8c6a5b1d5334034e406131fac81f0c19
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713900"
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>チュートリアル: アプリケーションを登録して Azure Active Directory B2C を使用したサインアップとサインインを有効にする

このチュートリアルを読むと、数分で Microsoft Azure Active Directory (Azure AD) B2C テナントを作成し、アプリケーションを登録できるようになります。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * アプリケーションの登録

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

既存のテナントで B2C の機能を有効にすることはできません。 Azure AD B2C テナントを作成する必要があります。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

おめでとうございます、Azure Active Directory B2C テナントが作成されました。 あなたはこのテナントのグローバル管理者です。 必要に応じて、他のグローバル管理者を追加できます。 新しいテナントに切り替えるには、*新しいテナントの管理リンク* をクリックします。

![新しいテナントの管理リンク](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> B2C テナントを運用アプリ向けに使用する予定がある場合は、 [運用スケールとプレビュー B2C テナントの比較](active-directory-b2c-reference-tenant-type.md)に関する記事を参照してください。 既存の B2C テナントを削除し、同じドメイン名を使用して再作成したときに発生する既知の問題があります。 B2C テナントは異なるドメイン名で作成する必要があります。
>
>

## <a name="link-your-tenant-to-your-subscription"></a>サブスクリプションへのテナントのリンク

すべての B2C 機能を有効にして使用料を支払うには、Azure AD B2C テナントを Azure サブスクリプションにリンクする必要があります。 詳しくは、[こちらの記事](active-directory-b2c-how-to-enable-billing.md)をご覧ください。 Azure AD B2C テナントを Azure サブスクリプションにリンクしていない場合は、一部の機能がブロックされ、B2C 設定に警告メッセージ ("この B2C テナントにリンクされたサブスクリプションがありません。サブスクリプションを確認してください。") が表示されます。 アプリを運用環境に移行する前に、この手順を実行することが重要です。


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

ポータル上部にある **[リソースの検索]** に `Azure AD B2C` を入力してブレードにアクセスすることもできます。 結果リストで **[Azure AD B2C]** を選択して [B2C setting]\(B2C 設定\) ブレードにアクセスします。

## <a name="register-your-application"></a>アプリケーションの登録

コンシューマーのサインアップおよびサインインを受け付けるアプリケーションを作成するには、最初にアプリケーションを Azure Active Directory B2C テナントに登録する必要があります。 「 [Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に書かれている手順に従って独自のテナントを取得してください。

Azure ポータルで作成したアプリケーションは、同じ場所から管理する必要があります。 PowerShell または別のポータルを使用して Azure AD B2C アプリケーションを編集すると、そのアプリケーションはサポート対象外となり、Azure AD B2C で動作しなくなります。 詳細については、「[アプリの障害](#faulted-apps)」セクションを参照してください。 

この記事では、サンプルを使い始めるために役立つ例を使用しています。 これらのサンプルの詳細については、後続の記事で説明しています。

### <a name="navigate-to-b2c-settings"></a>B2C の設定に移動する

B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にログインします。 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>アプリケーションの種類に基づいて次の手順を選択する

* [Web アプリケーションの登録](#register-a-web-app)
* [Web API の登録](#register-a-web-api)
* [モバイル/ネイティブ アプリケーションの登録](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Web アプリの登録

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Web アプリのクライアント シークレットを作成する

Web アプリケーションから、Azure AD B2C によって保護された Web API を呼び出す場合、次の手順を実行します。
   1. **[キー]** ブレードに移動して **[キーの生成]** ボタンをクリックし、アプリケーション シークレットを作成します。 **[アプリ キー]** の値をメモしておきます。 アプリケーションのコード内では、この値をアプリケーション シークレットとして使用します。
   2. **[API アクセス]**、**[追加]** の順にクリックし、Web API とスコープ (アクセス許可) を選択します。

> [!NOTE]
> **アプリケーション シークレット** は重要なセキュリティ資格情報であり、適切にセキュリティで保護する必要があります。
> 

[**次の手順**に進む](#next-steps)

### <a name="register-a-web-api"></a>Web API の登録

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

必要に応じて **[公開済みスコープ ]** をクリックしてスコープを追加します。 既定では、"user_impersonation" スコープが定義されます。 user_impersonation スコープによって、他のアプリケーションが、サインイン ユーザーの代わりにこの API にアクセスできるようになります。 不要であれば user_impersonation スコープは削除してかまいません。

[**次の手順**に進む](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>モバイル/ネイティブ アプリの登録

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[**次の手順**に進む](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>Web アプリ/API の応答 URL の選択

現時点では、Azure AD B2C に登録されるアプリは、限られた応答 URL 値に制限されています。 Web アプリと Web サービスの応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次の応答 URL のいずれかを使用する Web アプリを登録することはできません。

`https://login-east.contoso.com`

`https://login-west.contoso.com`

登録システムによって、既存の応答 URL の DNS 名全体と、追加しようとしている応答 URL の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。

* 新しい応答 URL の DNS 名全体が、既存の応答 URL の DNS 名と一致しない場合。
* 新しい応答 URL の DNS 名全体が、既存の応答 URL のサブドメインではない場合。

たとえば、アプリで次の応答 URL を使用しているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。 応答 URL として login-east.contoso.com と login-west.contoso.com を使用するアプリが必要な場合は、これらの応答 URL を次の順番で追加する必要があります。

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の contoso.com という応答 URL のサブドメインであるためです。

### <a name="choosing-a-native-app-redirect-uri"></a>ネイティブ アプリケーションのリダイレクト URI の選択

モバイル/ネイティブ アプリケーションのリダイレクト URI を選択する際には、2 つの重要な考慮事項があります。

* **一意**: リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 この例 (com.onmicrosoft.contoso.appname://redirect/path) では、com.onmicrosoft.contoso.appname がスキームです。 このパターンに従うことをお勧めします。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーには [アプリの選択] ダイアログが表示されます。 ユーザーが不適切な選択を行った場合、ログインは失敗します。
* **完全**: リダイレクト URI には、スキームとパスが必要です。 パスには、ドメインの後に 1 つ以上のスラッシュを含める必要があります (たとえば、//contoso/ は機能しますが、//contoso は失敗します)。

リダイレクト URI にアンダースコアなどの特殊文字がないことを確認します。

### <a name="faulted-apps"></a>アプリの障害

B2C アプリケーションは次の手段で編集しないでください。

* 他のアプリケーション管理ポータル ([アプリケーション登録ポータル](https://apps.dev.microsoft.com/)など)
* Graph API または PowerShell

前に説明したように、Azure AD B2C アプリケーションを編集し、それを Azure ポータルの Azure AD B2C 機能でもう一度編集しようとすると、アプリに障害が発生し、Azure AD B2C で使用できなくなります。 この場合、アプリケーションを削除し、もう一度作成する必要があります。

アプリケーションを削除するには、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/)に移動し、そこでアプリケーションを削除します。 アプリケーションを表示するためには、そのアプリケーションの所有者である必要があります (テナントの管理者であるだけでは不十分です)。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * アプリケーションの登録

> [!div class="nextstepaction"]
> [Web アプリケーションのアカウントの認証を有効にする](active-directory-b2c-tutorials-web-app.md)