---
title: アプリ ロールを追加してトークンから取得する | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory に登録されたアプリケーションにアプリ ロールを追加し、それらのロールにユーザーとグループを割り当てて、トークンの 'roles' 要求で受け取る方法について説明します。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706015"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>方法:アプリケーションにアプリ ロールを追加してトークンで受け取る

ロールベースのアクセス制御 (RBAC) は、アプリケーションにおいて承認を実施する一般的なメカニズムです。 RBAC を使用するとき、管理者は、個々のユーザーまたはグループではなく、ロールにアクセス許可を付与します。 その後、管理者はロールをさまざまなユーザーやグループに割り当てて、コンテンツや機能にだれがアクセスできるかを制御できます。

RBAC をアプリケーション ロールおよびロール要求と一緒に使用すると、開発者はあまり手間をかけずにアプリでの承認を確実に行うことができます。

もう 1 つの方法として、GitHub 上の [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) コード サンプルで示されているように、Azure AD グループおよびグループ要求を使用することもできます。 Azure AD グループとアプリケーション ロールは相互排他ではありません。一緒に使用することで、さらにきめ細かいアクセス制御を提供することができます。

## <a name="declare-roles-for-an-application"></a>アプリケーションのロールを宣言する

アプリケーション ロールは、[Azure portal](https://portal.azure.com) で定義されています。  ユーザーがアプリケーションにサインインすると、Azure AD は、各ロール (ユーザーに個別に付与されたロール、およびグループ メンバーシップを通じて付与されているロール) の `roles` 要求を生成します。  ユーザーとグループのロールへの割り当ては、ポータルの UI を介して行うか、[Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) を使用してプログラミングによって行うことができます。

アプリのロールを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、アプリのロールを追加するアプリ登録を含む Azure Active Directory テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]** を選択し、アプリ ロールを定義するアプリケーションを選択します。
1. **[App roles | Preview]\(アプリ ロール | プレビュー\)** を選択し、 **[Create app role]\(アプリ ロールの作成\)** を選択します。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure portal の [アプリの登録] の [アプリのロール] ペイン":::
1. **[Create app role]\(アプリのロールの作成\)** ペインで、ロールの設定を入力します。 図の下の表では、各設定とそのパラメーターについて説明します。

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Azure portal の [アプリの登録] のアプリ ロールによって作成されるコンテキスト ペイン":::

    | フィールド | 説明 | 例 |
    |-------|-------------|---------|
    | **表示名** | 管理者の同意やアプリの割り当て時に表示されるアプリのロールの表示名です。 この値にはスペースを含めることができます。  | `Survey Writer` |
    | **Allowed member types (許可されるメンバーの種類)** | このアプリのロールをユーザー、アプリケーション、またはその両方に割り当てることができるかどうかを指定します。 | `Users/Groups` |
    | **Value** | アプリケーション側でトークンに想定するロール要求の値を指定します。 この値は、アプリケーションのコードで参照される文字列と正確に一致する必要があります。 値にスペースを含めることはできません。 | `Survey.Create` |
    | **説明** | 管理者のアプリの割り当てと同意エクスペリエンスの間に表示されるアプリのロールの詳細な説明。 | `Writers can create surveys.` |
    | **Do you want to enable this app role? (このアプリのロールを有効にしますか?)** | アプリのロールを有効にするかどうか。 アプリのロールを削除するには、このチェックボックスをオフにして、変更を適用してから削除操作を試行してください。 | *オン* |

1. **[適用]** を選択して変更を保存します。

> [!NOTE]
> 追加するロールの数は、アプリケーション マニフェストに定義されている制限までカウントされます。 このような制限については、[Azure Active Directory のアプリ マニフェスト リファレンス](reference-app-manifest.md)に関するページの「[マニフェストの制限](./reference-app-manifest.md#manifest-limits)」セクションを参照してください。

## <a name="assign-users-and-groups-to-roles"></a>ロールにユーザーとグループを割り当てる

アプリケーションにアプリ ロールを追加したら、それらのロールにユーザーとグループを割り当てることができます。

1. Azure portal の **Azure Active Directory** で、左側のナビゲーション メニューの **[エンタープライズ アプリケーション]** を選択します。
1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

     アプリケーションが一覧に表示されない場合は、 **[すべてのアプリケーション]** 一覧の上部にあるフィルターを使用して一覧を制限するか、一覧を下にスクロールしてアプリケーションを見つけます。

1. ユーザーまたはグループをロールに割り当てるアプリケーションを選択します。
1. **[管理]** で **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ペインを開きます。
1. **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターを選択します。

     ユーザーとセキュリティ グループの一覧が表示されます。 特定のユーザーまたはグループを検索することや、一覧に表示される複数のユーザーやグループを選択することができます。

1. ユーザーとグループを選択したら、 **[選択]** ボタンを選択して続行します。
1. **[割り当ての追加]** ペインで **[ロールの選択]** を選択します。 アプリケーションに対して定義されているすべてのロールが表示されます。
1. ロールを選択し、 **[選択]** ボタンを選択します。
1. **[割り当て]** ボタンを選択して、アプリへのユーザーとグループの割り当てを完了します。
1. 追加したユーザーとグループが **[ユーザーとグループ]** の一覧に表示されることを確認します。

## <a name="receive-roles-in-tokens"></a>トークンでロールを受信する

さまざまなアプリのロールに割り当てられたユーザーがアプリケーションにサインインすると、`roles` 要求で割り当てられたロールがトークンに付与されます。

## <a name="web-api-application-permissions"></a>Web API アプリケーションのアクセス許可

**[ユーザー/グループ]** 、 **[アプリケーション]** 、または **[両方]** を対象とするアプリ ロールを定義できます。 **[アプリケーション]** または **[両方]** を選択すると、アプリのロールはクライアント アプリの **[API のアクセス許可]** にアプリケーション アクセス許可として表示されます。

**[アプリケーション]** または **[両方]** を対象とするロールを定義した後、クライアント アプリの登録でアプリケーションのアクセス許可を選択するには:

1. Azure portal の **[Azure Active Directory]** で、 **[アプリの登録]** を選択し、クライアント アプリの登録を選択します。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[アクセス許可の追加]**  >  **[自分の API]** を選択します
1. アプリケーション ロールを追加したアプリを選択し、 **[アプリケーションのアクセス許可]** を選択します。

## <a name="next-steps"></a>次の手順

アプリ ロールの詳細については、次のリソースを参照してください。

- コード サンプル:[アプリ ロールおよびロール要求を使用して ASP.NET Core Web アプリに承認を追加する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- ビデオ:[Microsoft ID プラットフォームを使用してアプリケーションで承認を実装する](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Azure Active Directory のアプリ マニフェスト](./reference-app-manifest.md)
- [Azure AD のアクセス トークン](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
