---
title: Azure Active Directory アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン | Microsoft Docs
description: SAML 認証でセキュリティ保護されているオンプレミスのアプリケーションにシングル サインオンを提供する方法を説明します。 アプリケーション プロキシを使用したオンプレミス アプリへのリモート アクセスを提供します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f4ab79a9fc9b8fec26cce98d9a878b6fa2a0c3
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853745"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン

SAML 認証でセキュリティ保護されているオンプレミスのアプリケーションに対してシングル サインオン (SSO) を提供し、アプリケーション プロキシを通じてこれらのアプリケーションへのリモート アクセスを提供できます。 SAML によるシングル サインオンでは、ユーザーの Azure Active Directory (Azure AD) アカウントを使用して、Azure AD がアプリケーションに対する認証を行います。 Azure AD は、接続プロトコルを通してアプリケーションにシングル サインオンの情報を伝達します。 また、SAML 要求で定義するルールに基づいて、ユーザーを特定のアプリケーション ロールにマップできます。 SAML SSO に加えてアプリケーション プロキシを有効にすると、ユーザーには、アプリケーションへの外部アクセスと、シームレスな SSO エクスペリエンスが提供されます。

アプリケーションでは、**Azure Active Directory** によって発行された SAML トークンを使用できる必要があります。 この構成は、オンプレミスの ID プロバイダーを使用するアプリケーションには適用されません。 このようなシナリオの場合は、[アプリケーションを Azure AD に移行するためのリソース](migration-resources.md)に関するページを参照することをお勧めします。

アプリケーション プロキシを使用する SAML SSO は、SAML トークンの暗号化機能でも動作します。 詳しくは、「[Azure AD SAML トークン暗号化の構成](howto-saml-token-encryption.md)」をご覧ください。

次のプロトコル図は、サービス プロバイダーによって開始される (SP によって開始される) フローと ID プロバイダーによって開始される (IdP によって開始される) フローの両方に対するシングル サインオン シーケンスを示しています。 アプリケーション プロキシは、オンプレミス アプリケーションとの間で SAML 要求と応答をキャッシュすることによって、SAML SSO と連携します。

  ![SAML SP フロー](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP フロー](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>アプリケーションの作成と SAML SSO の設定

1. Azure portal 内で、 **[Azure Active Directory] > [エンタープライズ アプリケーション]** の順に選択し、 **[新規アプリケーション]** を選択します。

2. **[独自のアプリを追加する]** で、 **[ギャラリー以外のアプリケーション]** を選択します。

3. 新しいアプリケーションの表示名を入力し、 **[追加]** を選択します。

4. アプリの **[概要]** ページで、 **[シングル サインオン]** を選択します。

5. シングル サインオンの方法として、 **[SAML]** を選択します。

6. まず、企業ネットワーク上で動作するように SAML SSO を設定します。 **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の見出しに移動し、その **[編集]** アイコン (鉛筆の形) を選択します。 [基本的な SAML 構成の入力](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on)に関するページに記載されている手順に従って、アプリケーションに対する SAML ベースの認証を構成します。

7. アプリケーションに少なくとも 1 人のユーザーを追加し、テスト アカウントにアプリケーションへのアクセス権があることを確認します。 企業ネットワークに接続しているときに、テスト アカウントを使用して、アプリケーションへのシングル サインオンがあるかどうかを確認します。 

   > [!NOTE]
   > アプリケーション プロキシを設定した後、戻って SAML **応答 URL** を更新します。

## <a name="publish-the-on-premises-application-with-application-proxy"></a>アプリケーション プロキシを使用してオンプレミスのアプリケーションを発行する

オンプレミスのアプリケーションに SSO を提供する前に、アプリケーション プロキシを有効にして、コネクタをインストールする必要があります。 オンプレミス環境を準備する方法、コネクタをインストールして登録する方法、コネクタをテストする方法については、チュートリアル「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)」をご覧ください。 次に、これらの手順に従って、アプリケーション プロキシを使用して新しいアプリケーションを発行します。 以下に記載されていないその他の設定については、チュートリアルの「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」セクションをご覧ください。

1. Azure portal 内でアプリケーションを開いたまま、 **[アプリケーション プロキシ]** を選択します。 アプリケーションの **[内部 URL]** を指定します。 カスタム ドメインを使用している場合は、アプリケーションの SSL 証明書もアップロードする必要があります。 
   > [!NOTE]
   > ベスト プラクティスとして、最適化されたユーザー エクスペリエンスのため、可能な限り、カスタム ドメインを使用します。 詳しくは、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」をご覧ください。

2. アプリケーションの **[事前認証]** 方法として **[Azure Active Directory]** を選択します。

3. アプリケーションの**外部 URL** をコピーします。 SAML 構成を完了するには、この URL が必要になります。

4. テスト アカウントを使用して、**外部 URL** によってアプリケーションを開き、アプリケーション プロキシが正しく設定されていることを確認します。 問題がある場合は、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)」をご覧ください。

## <a name="update-the-saml-configuration"></a>SAML 構成の更新

1. Azure portal 内でアプリケーションを開いたまま、 **[シングル サインオン]** を選択します。 

2. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の見出しに移動し、その **[編集]** アイコン (鉛筆の形) を選択します。 アプリケーション プロキシ内で構成した**外部 URL** によって、 **[識別子]** 、 **[応答 URL]** 、 **[ログアウト URL]** の各フィールドが自動的に入力されます。 これらの URL は、アプリケーション プロキシが正常に動作するために必要なため、編集しないでください。

3. アプリケーション プロキシによってドメインに到達できるように、前に構成した**応答 URL** を編集します。 たとえば、**外部 URL** が `https://contosotravel-f128.msappproxy.net` で、元の**応答 URL** が `https://contosotravel.com/acs` の場合、元の**応答 URL** を `https://contosotravel-f128.msappproxy.net/acs` に更新する必要があります。 

    ![基本的な SAML 構成データを入力する](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 更新された**応答 URL** の横にあるチェックボックスをオンにして、既定値としてマークします。

   * 必須の**応答 URL** が既に表示されている場合は、この**応答 URL** を既定値としてマークし、以前に構成した**応答 URL** を削除します。

   * SP によって開始されたフローの場合、バックエンド アプリケーションで認証トークンを受け取るための適切な**応答 URL** または Assertion Consumer Service URL が指定されていることを確認します。

    > [!NOTE]
    > バックエンド アプリケーションで**応答 URL** が内部 URL である必要がある場合は、[カスタム ドメイン](application-proxy-configure-custom-domain.md)を使用して内部 URL と外部 URL を一致させるか、マイ アプリのセキュリティで保護されたサインイン拡張機能を、ユーザーのデバイスにインストールする必要があります。 この拡張機能では、適切なアプリケーション プロキシ サービスに自動的にリダイレクトされます。 拡張機能のインストールについては、「[マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)」をご覧ください。
    
## <a name="test-your-app"></a>アプリをテストする

以上の手順がすべて完了すれば、アプリは正常に動作します。 アプリをテストするには:

1. ブラウザーを開き、アプリを発行したときに作成した**外部 URL** に移動します。 
1. サインインには、アプリに割り当てたテスト アカウントを使用します。 アプリケーションを読み込み、アプリケーションにシングル サインオンできるようになります。

## <a name="next-steps"></a>次の手順

- [Azure AD アプリケーション プロキシを使用したシングル サインオンの提供](application-proxy-single-sign-on.md)
- [アプリケーション プロキシのトラブルシューティング](application-proxy-troubleshoot.md)
