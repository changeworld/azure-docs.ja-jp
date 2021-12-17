---
title: 'チュートリアル: Azure AD SSO と Zero Networks の統合'
description: Azure Active Directory と Zero Networks の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 533e1dee16a303931987bc83f15f929d89d38b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669373"
---
# <a name="tutorial-azure-ad-sso-integration-with-zero-networks"></a>チュートリアル: Azure AD SSO と Zero Networks の統合

このチュートリアルでは、Zero Networks と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Zero Networks を統合すると、次のことが可能になります。

* Zero Networks にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Zero Networks に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zero Networks でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Zero Networks Admin Portal と Access Portal の Azure AD SSO を構成します。

* Zero Networks では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-zero-networks-from-the-gallery"></a>ギャラリーからの Zero Networks の追加

Azure AD への Zero Networks の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Zero Networks を追加する必要があります。

1. Azure portal に、職場の Microsoft アカウントを使用してサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zero Networks**」と入力します。
1. 結果のパネルから **[Zero Networks]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zero Networks** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、URL として「`https://portal.zeronetworks.com/#/login`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Zero Networks のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

## <a name="configure-zero-networks-sso&quot;></a>Zero Networks SSO の構成

1. Zero Networks Admin Portal に管理者としてログインします。

1. **[設定]**  >  **[ID プロバイダー]** に移動します。

1. **[Microsoft Azure]** をクリックして、次の手順を実行します。
    
    ![SSO 構成の設定を示すスクリーンショット。](./media/zero-networks-tutorial/settings.png &quot;Account")

    1. **[Identifier(Entity ID)]\(識別子 (エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキスト ボックスにこの値を貼り付けます。

    1. **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (アサーション コンシューマー サービス URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    1. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[ログアウト URL]** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    1. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[証明書 (Base64)]** テキスト ボックスに貼り付けます。

    1. **[保存]** をクリックします。

## <a name="configure-user-assignment-requirement"></a>ユーザー割り当ての要件を構成する

1. Azure portal の **Zero Networks** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[プロパティ]** を選択します。
1. **[ユーザーの割り当てが必要]** を **[いいえ]** に変更します。

![[ユーザーの割り当てが必要] のスクリーンショット。](./media/zero-networks-tutorial/user-assignment.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zero Networks のサインオン URL にリダイレクトされます。 

* Zero Networks のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zero Networks] タイルをクリックすると、Zero Networks のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。
