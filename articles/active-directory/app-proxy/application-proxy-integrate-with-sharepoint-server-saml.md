---
title: Azure Active Directory アプリケーション プロキシを使用してオンプレミス SharePoint ファームを発行する
description: SAML でオンプレミスの SharePoint ファームを Azure Active Directory アプリケーション プロキシと統合する方法の基礎について説明します。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2821cfeb2fc3df05d0b8862ad67adf9dd719ebd8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989287"
---
# <a name="integrate-azure-active-directory-application-proxy-with-sharepoint-saml"></a>Azure Active Directory アプリケーション プロキシと SharePoint を統合する (SAML)

このステップバイステップ ガイドでは、Azure AD アプリケーション プロキシを使用して、組織 (Azure AD、B2B) のユーザーがインターネット経由で SharePoint に接続する、[Azure Active Directory が統合されたオンプレミスの SharePoint (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) のアクセスをセキュリティで保護する方法について説明します。

> [!NOTE]
> Azure AD アプリケーション プロキシを使用したことがなく、詳細を確認したい場合は、[Azure AD アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](./application-proxy.md)に関する記事を参照してください。

このセットアップには、主に次の 3 つの利点があります。

- Azure AD アプリケーション プロキシにより、認証されたトラフィックが内部ネットワークと SharePoint に確実に到達します。
- ユーザーは、VPN を使用せずに通常どおり SharePoint のサイトにアクセスできます。
- Azure AD アプリケーション プロキシ レベルでのユーザー割り当てにより、アクセスを制御できます。また、条件付きアクセスや多要素認証 (MFA) などの Azure AD の機能を使用して、セキュリティを強化することもできます。

このプロセスには、2 つのエンタープライズ アプリケーションが必要です。 1 つは、ギャラリーからマネージド SaaS アプリのリストに発行する、SharePoint オンプレミスのインスタンスです。 もう 1 つは、最初のエンタープライズ ギャラリー アプリケーションの発行に使用する、オンプレミス アプリケーション (ギャラリー以外のアプリケーション) です。

## <a name="prerequisites"></a>前提条件

この構成を完了するには、次のリソースが必要です。
 - SharePoint 2013 以降のファーム。 SharePoint ファームは、[Azure AD と統合](../saas-apps/sharepoint-on-premises-tutorial.md)されている必要があります。
 - アプリケーション プロキシが含まれるプランを使用する Azure AD テナント。 詳細については、[Azure AD のプランと料金](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)をご覧ください。
 - Azure AD テナント内の[カスタム検証済みドメイン](../fundamentals/add-custom-domain.md)。 検証済みのドメインは、SharePoint の URL サフィックスと一致している必要があります。
 - SSL 証明書が必要です。 詳細については、[カスタム ドメインでの発行](./application-proxy-configure-custom-domain.md)に関するページを参照してください。
 - オンプレミスの Active Directory ユーザーを Azure AD Connect と同期し、[Azure にサインイン](../hybrid/plan-connect-user-signin.md)するように構成する必要があります。 
 - クラウド専用ユーザーと B2B ゲスト ユーザーについては、[Azure portal で SharePoint オンプレミスへのアクセス権をゲスト アカウントに付与する](../saas-apps/sharepoint-on-premises-tutorial.md#manage-guest-users-access)必要があります。
 - 企業ドメイン内のコンピューターにインストールされて実行されているアプリケーション プロキシ コネクタ。


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>手順 1: SharePoint オンプレミスを Azure AD と統合する

1. SharePoint オンプレミス アプリを構成します。 詳細については、「[チュートリアル: Azure Active Directory シングル サインオンと SharePoint オンプレミスの統合](../saas-apps/sharepoint-on-premises-tutorial.md)」を参照してください。
2. 次の手順に進む前に、構成を検証します。 検証するには、内部ネットワークから SharePoint オンプレミスにアクセスし、内部的にアクセスできることを確認します。


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>手順 2: アプリケーション プロキシを使用して SharePoint オンプレミス アプリケーションを発行する

この手順では、アプリケーション プロキシが使用されるアプリケーションを Azure AD テナントに作成します。 外部 URL を設定し、内部 URL を指定します。どちらも、後で SharePoint において使用します。

> [!NOTE]
> 内部 URL と外部 URL は、手順 1 の SAML ベースのアプリケーション構成の **サインオン URL** と一致している必要があります。

   ![サインオン URL の値を示すスクリーンショット。](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. カスタム ドメインを使用して、新しい Azure AD アプリケーション プロキシ アプリケーションを作成します。 詳細な手順については、「[Azure AD アプリケーション プロキシのカスタム ドメイン](./application-proxy-configure-custom-domain.md)」を参照してください。

    - 内部 URL: 'https://portal.contoso.com/ '
    - 外部 URL: 'https://portal.contoso.com/ '
    - [事前認証]: [Azure Active Directory]
    - [Translate URLs in Headers]\(ヘッダーの URL を変換する\): [いいえ]
    - [Translate URLs in Application Body]\(アプリケーション本文の URL を変換する\): [いいえ]

        ![アプリの作成に使用するオプションを示すスクリーンショット。](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. オンプレミスの SharePoint ギャラリー アプリケーションに割り当てたグループと[同じグループ](../saas-apps/sharepoint-on-premises-tutorial.md#grant-permissions-to-a-security-group)を割り当てます。

3. 最後に、 **[プロパティ]** セクションにアクセスし、 **[ユーザーに表示しますか?]** を **[いいえ]** に設定します。 このオプションを選択すると、最初のアプリケーションのアイコンのみがマイ アプリ ポータル (https://myapplications.microsoft.com) に表示されます。

   ![[ユーザーに表示しますか?] オプションを設定する場所を示すスクリーンショット。](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>手順 3: アプリケーションをテストする

外部ネットワーク上のコンピューターからブラウザーを使用して、発行の手順で構成したリンクに移動します。 設定したテスト アカウントでサインインできることを確認します。
