---
title: クイック スタート:Azure Active Directory (Azure AD) テナント内のアプリケーションへのシングル サインオン (SSO) を設定する
description: このクイックスタートでは、Azure Active Directory (Azure AD) テナント内のアプリケーションへのシングル サインオン (SSO) を設定するプロセスについて説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b19427070d982918584c13c25518cffe55497000
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223331"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナント内のアプリケーションへのシングル サインオン (SSO) を設定する

Azure Active Directory (Azure AD) テナントに追加したアプリケーションへのシングル サインオン (SSO) を設定することで、簡略化されたユーザー ログインの利用を開始します。 SSO を設定すると、ユーザーが自分の Azure AD の資格情報を使用してアプリケーションにサインインできるようになります。 SSO は Azure AD の Free Edition に含まれています。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリケーションへの SSO を設定するには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 既に事前構成済みで Azure AD ギャラリーに追加されている、SSO をサポートするアプリケーション。 ほとんどのアプリでは、SSO に Azure AD を使用できます。 Azure AD ギャラリー内のアプリは事前構成されています。 ご自分のアプリが一覧に表示されていない場合、またはカスタム開発されたアプリの場合でも、Azure AD で使用できます。 目次にあるチュートリアルやその他のドキュメントを確認してください。 このクイックスタートは、SSO 用に事前構成され、アプリ開発者によって Azure AD ギャラリーに追加されたアプリに重点を置いています。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。


>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。


## <a name="enable-single-sign-on-for-an-app"></a>アプリへのシングル サインオンを有効にする

Azure AD テナントへのアプリケーションの追加が完了すると、概要ページが表示されます。 既に追加されているアプリケーションを構成する場合は、最初のクイックスタートを参照してください。 テナントに追加されているアプリケーションを表示する手順が説明されています。 

アプリケーションへのシングル サインオンを設定するには、次を行います。

1. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 シングル サインオンを設定するアプリケーションを見つけて選択します。
1. **[管理]** セクションで、 **[シングル サインオン]** を選択して編集用の **[シングル サインオン]** ペインを開きます。

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Azure AD ポータル上のシングル サインオンの構成ページを示すスクリーンショット。":::

1. **[SAML]** を選択して SSO の構成ページを開きます。 この例では、SSO 用に構成しているアプリケーションは GitHub です。 GitHub が設定されると、ユーザーは自分の Azure AD テナントの資格情報を使用して GitHub にサインインできるようになります。

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="GitHub 上のシングル サインオンの構成ページを示すスクリーンショット。":::

1. SAML ベースの SSO に Azure AD を使用するようにアプリケーションを構成するプロセスは、アプリケーションによって異なります。 GitHub に関するガイダンスへのリンクが表示されています。 他のアプリに関するガイドは、「[SaaS アプリケーションと Azure Active Directory の統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/)」で見つけることができます。
1. アプリケーションへの SSO を設定するには、そのガイドに従ってください。 多くのアプリケーションには、SSO 機能に関する特定のサブスクリプション要件があります。 たとえば、GitHub の場合は Enterprise サブスクリプションが必要です。

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="GitHub の価格設定ページの Enterprise サブスクリプション内に表示されたシングル サインオン オプションを示すスクリーンショット。":::


## <a name="next-step"></a>次のステップ

- [アプリを削除する](delete-application-portal.md)
