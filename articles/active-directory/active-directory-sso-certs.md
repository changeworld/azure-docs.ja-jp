---
title: "Azure AD でフェデレーション証明書を管理する方法 |Microsoft Docs"
description: "フェデレーション証明書の有効期限をカスタマイズする方法と、有効期限が近づいている証明書を更新する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理
この記事では、SaaS アプリケーションにフェデレーション シングル サインオン (SSO) を確立するために Azure Active Directory で作成される証明書に関連する一般的な質問と情報について説明します。 これらのアプリケーションは、Azure AD アプリ ギャラリーから追加できるほか、ギャラリー以外のアプリケーション テンプレートを使用して追加することもできます。 アプリケーションは、フェデレーション シングル サインオンのオプションを使用して構成する必要があります。

次の例に示すように、この記事では、**SAML フェデレーション**を使用して **Azure AD のシングル サインオン**を使用するように構成されたアプリのみに関連する内容を取り上げます。

![Azure AD のシングル サインオン](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>ギャラリーおよびギャラリー以外のアプリケーションの証明書の自動生成
ギャラリーから新しいアプリケーションを追加して SAML ベースのサインオンを構成すると、Azure AD によって 3 年間有効なアプリケーションの証明書が生成されます。 この証明書は、**[SAML 署名証明書]** セクションからダウンロードできます。 ギャラリー アプリケーションの場合、このセクションでは、アプリケーションの要件に応じて**証明書**または**フェデレーション メタデータ**のダウンロードのオプションが表示されます。

![Azure AD のシングル サインオン](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>フェデレーション証明書の有効期限をカスタマイズして、新しい証明書をロールオーバーする方法
既定では、証明書は 3 年後に期限切れになるように設定されています。 下の手順に従って、証明書に別の有効期限を選択することができます。ここに用意したスクリーンショットでは、例として Salesforce を使用していますが、これらの手順は、他のフェデレーション SaaS アプリにも適用できます。

1. [Azure Active Directory](https://aad.portal.azure.com) の管理ポータルで、**[エンタープライズ アプリケーション]** のナビゲーションをクリックし、下に示すように **[概要]** ページの **[新しいアプリケーション]** をクリックします。

   ![SSO 構成ウィザードを開く](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. ギャラリー アプリケーションを検索し、追加するアプリケーションを選択します。 必要なアプリケーションが見つからない場合は、**[ギャラリー以外のアプリケーション]** オプションを使用してアプリケーションを追加します。 この機能は **Azure AD Premium (P1 および P2)** SKU でのみご利用いただけます。

    ![Azure AD のシングル サインオン](./media/active-directory-sso-certs/add_gallery_application.png)

3. 左側のナビゲーションの **[シングル サインオン]** リンクをクリックし、**[モード]**を **[SAML ベースのサインオン]** に変更します。 これにより、3 年間有効な証明書がアプリケーションに生成されます。

4. 新しい証明書を作成するには、**[SAML 署名証明書]** セクションの **[新しい証明書の作成]** リンクをクリックします。

    ![[新しい証明書の生成]](./media/active-directory-sso-certs/create_new_certficate.png)

5. [新しい証明書の作成] リンクでカレンダー コントロールが開きます。最大 3 年間で任意の日付と時刻を設定できます。 選択した日付と時刻が、新しい証明書の有効期限の日付と時刻になります。 **[保存]** をクリックして、証明書を保存します。

    ![証明書をダウンロードした後アップロードする](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. これで新しい証明書をダウンロードできます。 ダウンロードするには、**[証明書]** リンクをクリックします。 この時点で、証明書はアクティブではありません。 この時点でこの証明書をロールオーバーする場合は、**[Make new certificate active]\(新しい証明書をアクティブにする\)** チェック ボックスをオンにし、[保存] をクリックします。 Azure AD ではその時点から、応答の署名に新しい証明書が使用されるようになります。

7.  証明書を特定の SaaS アプリケーションにアップロードする方法については、ページの [View application configuration tutorial]\(アプリケーション構成チュートリアルの表示\) リンクをクリックします。

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>有効期限が近づいている証明書を更新する方法
次に示す更新手順を実行すると、ユーザーに対する大幅なダウンタイムがなくなり理想的です。 このセクションで使用するスクリーンショットでは、例として Salesforce を取り上げていますが、これらの手順は他のフェデレーション SaaS アプリにも適用できます。

1. **Azure Active Directory** アプリケーションの **[シングル サインオン]** ページで、アプリケーションの新しい証明書を生成します。 これを実行するには、**[SAML 署名証明書]** セクションの **[新しい証明書の作成]** リンクをクリックします。

    ![[新しい証明書の生成]](./media/active-directory-sso-certs/create_new_certficate.png)

2. 新しい証明書の適切な有効期限の日付と時刻を選択し、**[保存]** をクリックします。

3. [SAML 署名証明書] オプションで証明書をダウンロードします。 SaaS アプリのシングル サインオンの構成画面に新しい証明書をアップロードします。 特定の SaaS アプリケーションでこれを実行する方法については、ページの **[View application configuration tutorial]\(アプリケーション構成チュートリアルの表示\)** リンクをクリックします。
   
4. 新しい証明書を Azure AD でアクティブにするには、[Make new certificate active]\(新しい証明書をアクティブにする\) チェック ボックスをオンにし、ページ上部の **[保存]** をクリックします。 これにより、新しい証明書が Azure AD 側にロールオーバーされます。 証明書の状態が **[新規]** から **[アクティブ]** に変わります。 Azure AD ではその時点から、応答の署名に新しい証明書が使用されるようになります。 
   
    ![[新しい証明書の生成]](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>関連記事
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)
