---
title: Azure AD アプリのパスワード シングル サインオンを構成する方法 | Microsoft Docs
description: Microsoft ID プラットフォーム (Azure AD) で Azure AD エンタープライズ アプリケーションに対するパスワード シングル サインオン (SSO) を構成する方法
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063528"
---
# <a name="configure-password-single-sign-on"></a>パスワード シングル サインオンの構成

[ギャラリー アプリ](add-gallery-app.md)または[ギャラリー以外の Web アプリ](add-non-gallery-app.md)を Azure AD エンタープライズ アプリケーションに追加するときに利用できるシングル サインオン オプションの 1 つに、[パスワードベースのシングル サインオン](what-is-single-sign-on.md#password-based-sso)があります。 このオプションは、HTML のサインイン ページを備えたあらゆる Web で利用できます。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

パスワードベースの SSO は、Azure AD へのアプリケーションの統合を迅速に開始する優れた方法で、以下のことを実現します。

-   Azure AD と統合したアプリケーションのユーザー名とパスワードを安全に保存し、再生して、**ユーザーのシングル サインオン**を有効にする

-   サインインするためにユーザー名とパスワード以外のフィールドも必要とするアプリケーションに対して**複数のサインイン フィールドを必要とするアプリケーションをサポートする**

-   ユーザーが資格情報を入力するときに[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に表示されるユーザー名とパスワードの入力フィールドの**ラベルをカスタマイズ**する

-   **ユーザー**が既存のアプリケーション アカウントに対する自分のユーザー名とパスワードを[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に手動で入力して指定できるようにする

-   **ビジネス グループのメンバー**が、[[アプリケーションのセルフサービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 機能を使用して、ユーザーに割り当てられているユーザー名とパスワードを指定できるようにする

-   個人またはグループがアプリケーションにサインインする際に使用するユーザー名とパスワードを、**管理者**が資格情報の更新機能を使用して指定できるようにする。 

## <a name="before-you-begin"></a>開始する前に

アプリケーションが Azure AD テナントに追加されていない場合は、[ギャラリー アプリの追加](add-gallery-app.md)に関するページまたは[ギャラリー以外のアプリの追加](add-non-gallery-app.md)に関するページを参照してください。

## <a name="open-the-app-and-select-password-single-sign-on"></a>アプリを開いてパスワード シングル サインオンを選択する

1. クラウド アプリケーション管理者か Azure AD テナントのアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。 ご自分の Azure AD テナント内にあるアプリケーションのランダム サンプルが表示されます。 

3. **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択した後、 **[適用]** を選択します。

4. 検索ボックスにアプリケーションの名前を入力し、結果からアプリケーションを選択します。

5. **[管理]** セクションの **[シングル サインオン]** を選択します。 

6. **[パスワードベース ]** を選択します。

7. アプリケーションの Web ベースのサインイン ページの URL を入力します。 この文字列は、ユーザー名入力フィールドを含んだページである必要があります。

   ![パスワードベースのシングル サインオン](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. **[保存]** を選択します。 Azure AD は、ユーザー名とパスワードを入力するためのサインイン ページを解析しようとします。 試行に成功すれば完了です。 
 
> [!NOTE]
> 次の手順は、[アプリケーションへのユーザーまたはグループの割り当て](methods-for-assigning-users-and-groups.md)です。 ユーザーとグループを割り当てたら、アプリケーションへのサインインに使用する資格情報をユーザーに代わって指定できます。 **[ユーザーとグループ]** を選択し、目的のユーザーまたはグループの行のチェック ボックスをオンにして、 **[資格情報の更新]** をクリックします。 次に、そのユーザーまたはグループの代理で、使用するユーザー名とパスワードを入力します。 そうしなかった場合は、起動時に自分で資格情報を入力するように求めるプロンプトがユーザーに表示されます。
 

## <a name="manual-configuration"></a>手動構成

Azure AD による解析の試行が失敗した場合、サインオンを手動で構成することができます。

1. **[\<アプリケーション名> の構成]** の **[\<アプリケーション名> パスワード シングル サインオン設定の構成]** を選択し、 **[サインオンの構成]** ページを表示します。 

2. **[サインイン フィールドの手動検出]** を選択します。 サインイン フィールドの手動検出について説明した追加手順が表示されます。

   ![パスワードベースのシングル サインオンの手動構成](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **[サインイン フィールドの取り込み]** を選択します。 キャプチャ ステータス ページが新しいタブで開き、「**メタデータ キャプチャは現在進行中です**」というメッセージが示されます。

4. **[Access Panel Extension Required]** (アクセス パネル拡張機能必要) ボックスが新しいタブに表示された場合、 **[今すぐインストール]** を選択して、 **[マイ アプリによるセキュリティで保護されたサインイン拡張機能]** ブラウザー拡張機能をインストールします。 (ブラウザー拡張機能には、Microsoft Edge、Chrome、または Firefox が必要です)。続いて拡張機能をインストールし、起動し、有効にして、キャプチャ ステータス ページを更新します。

   ブラウザー拡張機能は次に、入力した URL を表示する別のタブを開きます。
5. 入力した URL のタブで、サインイン プロセスを実行します。 ユーザー名とパスワードのフィールドを入力し、サインインを試みます。 (正しいパスワードを指定する必要がありません)。

   プロンプトでは、キャプチャされたサインイン フィールドを保存するように要求されます。
6. **[OK]** を選択します。 ブラウザー拡張機能により、"**メタデータはアプリケーション用に更新されました**" というメッセージでキャプチャ ステータス ページが更新されます。 ブラウザー タブが閉じます。

7. Azure AD の **[サインオンの構成]** ページで、 **[正常にアプリにサインインできました]** を選択します。

8. **[OK]** を選択します。

サインイン ページのキャプチャ後に、ユーザーとグループを割り当てることができ、通常の[パスワード SSO アプリケーション](what-is-single-sign-on.md)と同様に資格情報ポリシーを設定できます。

> [!NOTE]
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。

## <a name="next-steps"></a>次のステップ

- [アプリケーションにユーザーまたはグループを割り当てる](methods-for-assigning-users-and-groups.md)
- [自動ユーザー アカウント プロビジョニングを構成する](../app-provisioning/configure-automatic-user-provisioning-portal.md)
