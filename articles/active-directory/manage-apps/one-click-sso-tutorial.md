---
title: Azure AD アプリ ギャラリーのアプリケーションに One Click SSO を構成する | Microsoft Docs
description: Azure AD アプリ ギャラリーのアプリケーションに One Click SSO を構成する手順。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064816"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Azure AD ギャラリー アプリケーション用の One Click SSO 機能

 このチュートリアルでは、SSO 構成用の UI を提供するすべての SAML アプリケーションに対して One Click SSO を実行する方法を説明します。

## <a name="introduction-to-one-click-sso"></a>One Click SSO の概要

One Click SSO 機能は、SAML プロトコルをサポートする Azure AD ギャラリー アプリのシングル サインオンを構成するために導入されました。 Azure AD SSO 構成ページでは、お客様がアプリケーション側で Azure AD メタデータを自動的に構成できるようにするために、このオプションを提供しています。 お客様が最小限の手作業で SSO を迅速に設定できるように支援することが目的です。 

## <a name="advantages-of-the-one-click-sso"></a>One Click SSO の利点

- お客様がアプリケーション側で手動設定を行う必要があるギャラリー アプリケーションの迅速な SSO 構成。
- より効率的で正確な構成方法。
- アプリケーションによって SAML 構成用の UI が提供されるため、設定にパートナー通信やサポートが不要。

## <a name="prerequisites"></a>前提条件

- OneClick SSO を使用して構成する、アプリケーションのアクティブなサブスクリプションと管理者の資格情報。
- ブラウザーにインストールされた、Microsoft の **My Apps Secure Sign-in ブラウザー拡張機能**。 この拡張機能の詳細については、この[リンク](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)を参照してください。

## <a name="one-click-sso-feature-step-by-step-details"></a>One Click SSO 機能のステップ バイ ステップの詳細

1. Azure AD アプリ ギャラリーからアプリケーションを追加します。

2. [シングル サインオン] をクリックします。

3. [シングル サインオンを有効にする] をクリックします。

4. [基本的な SAML 構成] セクションで、必須の構成値を設定します。

    > [!NOTE] 
    > アプリケーションでカスタム要求の構成が必要な場合は、OneClick SSO を実行する前にそれらを構成してください。

5. いずれかのギャラリー アプリケーションに One Click SSO 機能が実装されている場合は、次の画面が表示されます。 **My Apps Secure Sign-in ブラウザー拡張機能**がまだインストールされていない場合は、 **[拡張機能のインストール]** オプションをクリックする必要があります。

    ![My Apps Secure Sign-in ブラウザー拡張機能をインストールする](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. ブラウザーに拡張機能を追加した後、 **[Setup Application Name]\(アプリケーション名の設定)** をクリックすると、アプリケーション管理者ポータルに移動します。 アプリケーションを使用するには、管理者としてサインインする必要があります。

    ![Setup application name (アプリケーション名の設定)](./media/one-click-sso-tutorial/setup-sso.png)

7. ブラウザー拡張機能により、アプリケーションが自動的に構成されます。 最初に、続行するかどうかを確認するメッセージが表示されます。 **[はい]** をクリックします。

    ![自動設定データの保存](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > アプリケーションに特別な移動や手順が必要な場合は、それらの手順の実行を求める適切なメッセージが表示されます。 

8. 構成が済んだら、 **[OK]** をクリックして変更を保存します。

    ![自動設定データを保存する](./media/one-click-sso-tutorial/save-data.png)

9. 成功を確認するポップアップ メッセージが表示され、SSO の設定が正常に構成されます。 その後、アプリケーションをテストできます。

    ![構成された SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 構成が正常に完了すると、アプリケーションはログオフされ、Azure portal に戻ります。

11. シングル サインオンをテストするには、[テスト] ボタンをクリックします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [My Apps Secure Sign-in ブラウザー拡張機能の概要](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)に関するページ
 