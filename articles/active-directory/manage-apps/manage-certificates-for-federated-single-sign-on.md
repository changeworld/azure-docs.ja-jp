---
title: Azure AD でのフェデレーション証明書の管理 |Microsoft Docs
description: フェデレーション証明書の有効期限をカスタマイズする方法と、有効期限が近づいている証明書を更新する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 7bae891bd16ecd3fbbad88022fbbffd32ff41eae
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577551"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理
この記事では、SaaS アプリケーションにフェデレーション シングル サインオン (SSO) を確立するために Azure Active Directory (Azure AD) で作成される証明書に関連する一般的な質問と情報について説明します。 アプリケーションは Azure AD アプリ ギャラリーから追加するか、ギャラリー以外のアプリケーション テンプレートを使用して追加します。 アプリケーションの構成には、フェデレーション SSO オプションを使用します。

次の例に示すように、この記事は、SAML フェデレーションを使用して Azure AD SSO を使用するように構成されたアプリにのみに関連します。

![Azure AD Single Sign-On](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>ギャラリーおよびギャラリー以外のアプリケーションの証明書の自動生成
ギャラリーから新しいアプリケーションを追加して SAML ベースのサインオンを構成すると、Azure AD によって、3 年間有効なアプリケーションの証明書が生成されます。 この証明書は、**[SAML 署名証明書]** セクションからダウンロードできます。 ギャラリー アプリケーションの場合、このセクションには、アプリケーションの要件に応じて、証明書またはメタデータのダウンロードのオプションが表示されることがあります。

![Azure AD シングル サインオン](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>フェデレーション証明書の有効期限のカスタマイズと、新しい証明書へのロールオーバー
既定では、証明書は 3 年後に期限切れになるように設定されています。 次の手順を実行して、証明書の別の有効期限を選択できます。
スクリーンショットでは、例として Salesforce を使用していますが、これらの手順は、他のフェデレーション SaaS アプリにも適用できます。

1. [Azure Portal](https://aad.portal.azure.com) で、左側のウィンドウの **[エンタープライズ アプリケーション]** をクリックし、**[概要]** ページの **[新しいアプリケーション]** をクリックします。

   ![SSO 構成ウィザードを開く](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. ギャラリー アプリケーションを検索し、追加するアプリケーションを選択します。 必要なアプリケーションが見つからない場合は、**[ギャラリー以外のアプリケーション]** オプションを使用して、アプリケーションを追加します。 この機能は Azure AD Premium (P1 および P2) SKU でのみご利用いただけます。

    ![Azure AD シングル サインオン](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. 左側のウィンドウの **[シングル サインオン]** リンクをクリックし、**[モード]** を **[SAML ベースのサインオン]** に変更します。 これにより、アプリケーションに対して 3 年間有効な証明書が生成されます。

4. 新しい証明書を作成するには、**[SAML 署名証明書]** セクションの **[新しい証明書の作成]** リンクをクリックします。

    ![[新しい証明書の生成]](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. **[新しい証明書の作成]** リンクにより、カレンダー コントロールが開きます。 現在の日付を起点に最大 3 年間の任意の日付と時刻を設定できます。 選択した日付と時刻が、新しい証明書の有効期限の日付と時刻になります。 **[Save]** をクリックします。

    ![証明書をダウンロードした後アップロードする](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. これで新しい証明書をダウンロードできます。 ダウンロードするには、**[証明書]** リンクをクリックします。 この時点で、証明書はアクティブではありません。 この証明書にロールオーバーする場合は、**[新しい証明書をアクティブにする]** チェック ボックスをオンにし、**[保存]** をクリックします。 Azure AD ではその時点から、応答の署名に新しい証明書が使用されるようになります。

7.  証明書を特定の SaaS アプリケーションにアップロードする方法については、**[View application configuration tutorial]\(アプリケーション構成チュートリアルの表示\)** リンクをクリックします。

## <a name="certificate-expiration-notification-email"></a>証明書期限切れ通知メール

Azure AD は、SAML 証明書の有効期限が切れる 60 日前、30 日前、7 日前に、通知をメールで送信します。 通知の送り先のメール アドレスを指定するには:

- Azure Active Directory アプリケーションの [シングル サインオン] ページで、[通知用メール] フィールドに移動します。
- 証明書期限切れ通知メールを受け取る必要があるメール アドレスを入力します。 このフィールドの既定値には、アプリケーションを追加した管理者のメール アドレスが使われます。

aadnotification@microsoft.com から通知メールを受け取ります。 メールがスパムの場所に入れられるのを避けるため、必ずこのメール アドレスをアドレス帳に追加してください。 

## <a name="renew-a-certificate-that-will-soon-expire"></a>有効期限が近づいている証明書の更新
次に示す更新手順を実行すると、ユーザーの大幅なダウンタイムがなくなります。 このセクションのスクリーンショットでは、例として Salesforce を取り上げていますが、これらの手順は他のフェデレーション SaaS アプリにも適用できます。

1. **Azure Active Directory** アプリケーションの **[シングル サインオン]** ページで、アプリケーションの新しい証明書を生成します。 これを実行するには、**[SAML 署名証明書]** セクションの **[新しい証明書の作成]** リンクをクリックします。

    ![[新しい証明書の生成]](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. 新しい証明書の適切な有効期限の日付と時刻を選択し、**[保存]** をクリックします。

3. **[SAML 署名証明書]** オプションで証明書をダウンロードします。 SaaS アプリケーションのシングル サインオンの構成画面に新しい証明書をアップロードします。 特定の SaaS アプリケーションでこれを実行する方法については、**[View application configuration tutorial]\(アプリケーション構成チュートリアルの表示\)** リンクをクリックします。
   
4. 新しい証明書を Azure AD でアクティブにするには、**[新しい証明書をアクティブにする]** チェック ボックスをオンにし、ページ上部の **[保存]** をクリックします。 これにより、新しい証明書が Azure AD 側にロールオーバーされます。 証明書の状態が **[新規]** から **[アクティブ]** に変わります。 Azure AD ではその時点から、応答の署名に新しい証明書が使用されるようになります。 
   
    ![[新しい証明書の生成]](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>関連記事
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](what-is-single-sign-on.md)
* [SAML に基づいたシングル サインオンのトラブルシューティング](../develop/howto-v1-debug-saml-sso-issues.md)
