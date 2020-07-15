---
title: クイック スタート:Azure Active Directory (Azure AD) テナントのアプリケーションへのシングル サインオン (SSO) を設定する
description: このクイックスタートでは、Azure Active Directory (Azure AD) テナントのアプリケーションへのシングル サインオン (SSO) を設定するプロセスについて説明します。
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
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038992"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントのアプリケーションへのシングル サインオン (SSO) を設定する

Azure AD テナントに追加したアプリケーションへの SSO を設定することで、簡略化されたユーザー ログインの利用を開始します。 SSO を設定すると、ユーザーは自分の Azure AD の資格情報を使用してアプリケーションにサインインできるようになります。 SSO は Azure AD の Free Edition に含まれています。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリケーションへの SSO を設定するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 既に事前構成済みで Azure AD ギャラリーに追加されている、シングル サインオンをサポートするアプリケーション。 ほとんどのアプリでは、シングル サインオンに Azure AD を使用できます。 Azure AD ギャラリー内のアプリは事前構成されています。 ご自分のアプリが一覧に表示されていない場合、またはカスタム開発されたアプリの場合でも、Azure AD で使用できます。 目次にあるチュートリアルやその他のドキュメントを確認してください。 このクイックスタートは、SSO 用に事前構成され、アプリ開発者によって Azure AD ギャラリーに追加されたアプリに重点を置いています。
- (省略可能: [アプリの表示](view-applications-portal.md)の完了)。
- (省略可能: [アプリの追加](add-application-portal.md)の完了)。
- (省略可能: [アプリの構成](add-application-portal-configure.md)の完了)。


>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用することをお勧めします。


## <a name="enable-single-sign-on-for-an-app"></a>アプリへのシングル サインオンを有効にする

Azure AD テナントへのアプリケーションの追加が完了すると、すぐにその概要ページが表示されます。 既に追加されているアプリケーションを構成する場合は、最初のクイックスタートを参照して、テナントに追加されたアプリケーションを表示します。 

アプリケーションへのシングル サインオンを設定するには、次を行います。

1. Azure AD ポータルで、 **[エンタープライズ アプリケーション]** を選択し、シングル サインオンを設定するアプリケーションを見つけて選択します。
2. [管理] セクションで、 **[シングル サインオン]** を選択して編集用のプロパティ ウィンドウを開きます。
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Azure AD ポータルのシングル サインオンの構成ページを表示。":::
3. [SAML] を選択して SSO の構成ページを開きます。 この例では、SSO 用に構成しているアプリケーションは GitHub です。 GitHub が設定されると、あなたのユーザーは自分の Azure AD テナントの資格情報を使用して GitHub にサインインできるようになります。
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="GitHub のシングル サインオンの構成ページを表示。":::
4. SAML ベースの SSO に Azure AD を使用するようにアプリケーションを構成するプロセスは、アプリケーションによって異なります。 GitHub のガイダンスへのリンクがあることに注目してください。 他のアプリに関するガイドは、 https://docs.microsoft.com/azure/active-directory/saas-apps/ で見つけることができます。
5. アプリケーションへの SSO を設定するには、そのガイドに従ってください。 多くのアプリケーションには、SSO 機能に関する特定のサブスクリプション要件があります。 たとえば、GitHub の場合は Enterprise サブスクリプションが必要です。
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="GitHub の価格設定ページの Enterprise サブスクリプションのシングル サインオン オプションを表示。":::


## <a name="next-steps"></a>次のステップ

- [アプリを削除する](delete-application-portal.md)
