---
title: チュートリアル - B2B の多要素認証 - Azure AD
description: このチュートリアルでは、Azure AD B2B を使用して外部ユーザーやパートナー組織とコラボレーションするときに多要素認証 (MFA) を要求する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a1be1add489b3b48fc4e0fbeeca34197340e4b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053909"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>チュートリアル: B2B ゲスト ユーザーに多要素認証を適用する

外部の B2B ゲスト ユーザーとコラボレーションするときは、多要素認証 (MFA) ポリシーを使用して貴社のアプリを保護することをお勧めします。 そうすると、外部ユーザーがリソースにアクセスするために必要なものがユーザー名とパスワードだけではなくなります。 Azure Active Directory (Azure AD) では、アクセスで MFA を要求する条件付きアクセス ポリシーを使用してこの目標を達成できます。 MFA ポリシーは、組織のメンバーに対して有効にするのと同じ方法で、テナント レベル、アプリ レベル、または個々のゲスト ユーザー レベルで適用できます。

例:

![会社のアプリにサインインしているゲスト ユーザーを示す図](media/tutorial-mfa/aad-b2b-mfa-example.png)

1. 会社 A の管理者または従業員は、アクセスで MFA を要求するように構成されたクラウドまたはオンプレミスのアプリケーションを使用するようにゲスト ユーザーを招待します。
1. ゲスト ユーザーは、自分の職場、学校、またはソーシャルの ID を使用してサインインします。
1. ユーザーは、MFA チャレンジを完了するように求められます。 
1. ユーザーは、会社 A で MFA を設定し、自分の MFA オプションを選択します。 ユーザーは、アプリケーションへのアクセスを許可されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> - MFA をセットアップする前に、サインイン エクスペリエンスをテストします。
> - 環境内のクラウド アプリへのアクセスで MFA を要求する条件付きアクセス ポリシーを作成します。 このチュートリアルでは、Microsoft Azure Management アプリを使用してこのプロセスを説明します。
> - What If ツールを使用して MFA サインインをシミュレートします。
> - 条件付きアクセス ポリシーをテストします。
> - テスト ユーザーとポリシーをクリーンアップします。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルのシナリオを完了するための要件を次に示します。

- **Azure AD Premium エディションへのアクセス**。条件付きアクセス ポリシー機能が含まれます。 MFA を適用するには、Azure AD 条件付きアクセス ポリシーを作成する必要があります。 パートナーが MFA 機能を持っているかどうかに関係なく、MFA ポリシーは常に自分の組織に適用されることに注意してください。
- **有効な外部電子メール アカウント**。ゲスト ユーザーとしてテナント ディレクトリに追加し、サインインするために使用できます。 ゲスト アカウントの作成方法がわからない場合は、[Azure portal での B2B ゲスト ユーザーの追加](add-users-administrator.md)に関する記事を参照してください。

## <a name="create-a-test-guest-user-in-azure-ad"></a>Azure AD にテスト用のゲスト ユーザーを作成する

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
1. Azure Portal で、 **[Azure Active Directory]** を選びます。
1. 左側のメニューの **[管理]** で、 **[ユーザー]** を選択します。
1. **[新しいゲスト ユーザー]** を選択します。

    ![[新しいゲスト ユーザー] オプションを選択する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-user-3.png)

1. **[ID]** の下に、外部ユーザーのメール アドレスを入力します。 必要に応じて、名前とウェルカム メッセージを入力します。

    ![ゲスト招待メッセージを入力する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-user-4.png)

1. **[招待]** を選択して、招待をゲスト ユーザーに自動的に送信します。 **ユーザーが正常に招待された** ことを示すメッセージが表示されます。
1. 招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>MFA を設定する前にサインイン エクスペリエンスをテストする

1. テスト用のユーザー名とパスワードを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. サインイン資格情報だけを使用して Azure portal にアクセスできることに注意してください。 追加の認証は要求されません。
1. サインアウトします。

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>MFA を要求する条件付きアクセス ポリシーを作成する

1. [Azure portal](https://portal.azure.com/) にセキュリティ管理者または条件付きアクセス管理者としてサインインします。
1. Azure Portal で、 **[Azure Active Directory]** を選びます。
1. 左側のメニューの **[管理]** で、 **[セキュリティ]** を選択します。
1. **[保護]** で、 **[条件付きアクセス]** を選択します。
1. **[条件付きアクセス]** ページで、上部のツール バーの **[新しいポリシー]** を選択します。
1. **[新規]** ページの **[名前]** テキスト ボックスに、「**B2B ポータルにアクセスするには MFA が必要**」と入力します。
1. **[割り当て]** セクションで、 **[ユーザーとグループ]** の下にあるリンクを選択します。
1. **[ユーザーとグループ]** ページで、 **[ユーザーとグループの選択]** を選択し、 **[すべてのゲストと外部ユーザー]** を選択します。

    ![すべてのゲスト ユーザーを選択する方法を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-6.png)
1. **[割り当て]** セクションで、 **[クラウド アプリまたは操作]** の下にあるリンクを選択します。
1. **[アプリを選択]** を選択し、 **[選択]** の下にあるリンクを選択します。

    ![[クラウド アプリ] ページと [選択] オプションを示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-10.png)

1.  **[選択]** ページで **[Microsoft Azure Management]** を選択し、 **[選択]** を選択します。

    ![[Microsoft Azure Management] オプションが強調表示されているスクリーンショット。](media/tutorial-mfa/tutorial-mfa-policy-11.png)

1.  **[新規]** ページの **[アクセス制御]** セクションで、 **[許可]** の下にあるリンクを選択します。
1.  **[付与]** ページで、 **[アクセスの許可]** を選択し、 **[多要素認証を要求する]** チェック ボックスをオンにし、 **[選択]** を選択します。

    ![[多要素認証を要求する] オプションを示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-13.png)

1.  **[ポリシーの有効化]** で、 **[オン]** を選択します。

    ![[ポリシーの有効化] オプションが [オン] に設定された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-policy-14.png)

1.  **作成** を選択します。

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>What If オプションを使用してサインインをシミュレートする

1. **[条件付きアクセス | ポリシー]** ページで、 **[What If]** を選択します。

    ![[条件付きアクセス - ポリシー] ページの [What-if] オプションを選択する場所を示すスクリーンショット。](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

1. **[ユーザー]** の下にあるリンクを選択します。 
1. 検索ボックスに、テスト用のゲスト ユーザーの名前を入力します。 検索結果内のユーザーを選択し、 **[選択]** を選択します。

    ![ゲスト ユーザーが選択された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

1. **[Cloud apps, actions, or authentication content]\(クラウド アプリ、アクション、または認証コンテンツ\)** の下にあるリンクを選択します。 . **[アプリを選択]** を選択し、 **[選択]** の下にあるリンクを選択します。

    ![[Microsoft Azure の管理] アプリが選択された状態を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

1. **[クラウド アプリ]** ページのアプリケーションの一覧で、 **[Microsoft Azure Management]\(Microsoft Azure の管理\)** を選択し、 **[選択]** を選択します。
1. **[What If]** を選択し、 **[適用するポリシー]** タブの **[評価結果]** の下に新しいポリシーが表示されることを確認します。

    ![[What if] オプションを選択する場所を示すスクリーンショット](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

1. テスト用のユーザー名とパスワードを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. 追加の認証方法を要求するメッセージが表示されます。 ポリシーが有効になるまで時間がかかる可能性があることに注意してください。

    !["詳細情報が必要" メッセージが表示された状態を示すスクリーンショット](media/tutorial-mfa/mfa-required.png)

1. サインアウトします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、テスト ユーザーとテスト用の条件付きアクセス ポリシーを削除します。

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. テスト ユーザーを選択し、 **[ユーザーの削除]** を選択します。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[セキュリティ]** で、 **[条件付きアクセス]** を選択します。
1. **[ポリシー名]** の一覧で、テスト用のポリシーのコンテキスト メニュー (...) を選択し、 **[削除]** を選択します。 **[はい]** を選択して確定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ゲスト ユーザーがいずれかのクラウド アプリのサインインするときに MFA を使用することを要求する条件付きアクセス ポリシーを作成しました。 コラボレーションするためのゲスト ユーザーを追加する方法の詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)」を参照してください。
