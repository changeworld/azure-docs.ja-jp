---
title: "Azure Active Directory での B2B コラボレーション用の SaaS アプリの構成 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションのコードと PowerShell サンプル"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B コラボレーション用の SaaS アプリの構成

Azure Active Directory (Azure AD) B2B コラボレーションは、Azure AD と統合されているほとんどのアプリケーションで動作します。 このセクションでは、いくつかの一般的な SAS アプリを Azure AD B2B と共に使用できるように構成する手順について説明します。
アプリケーション固有の手順を説明する前に、いくつかの一般的な原則を示しておきます。

* ほとんどのアプリケーションで、ユーザー プロビジョニングを手動で行う必要があることに注意してください (つまり、アプリケーションでもユーザーを手動で作成する必要があります)。

* 自動プロビジョニングをサポートしているアプリ (Dropbox など) の場合は、アプリケーションによって個別の招待が作成されます。 ユーザーは各招待に確実に応じる必要があります。

* [ユーザー属性] で、常に [ユーザー識別子] を [user.mail] に設定します (ゲスト ユーザーの壊れた UPD の問題を緩和するため)。


##<a name="dropbox-for-business"></a>DropBox for Business

ユーザーが組織アカウントを使用してサインインできるようにするには、Dropbox for Business を手動で構成して、Azure AD が SAML ID プロバイダーとして使用されるようにする必要があります。 このように構成されていない場合、Dropbox for Business は、ユーザーにメッセージを表示するなどの方法で、ユーザーが Azure AD を使用してサインインすることを許可することができません。

1. スクリーンショットに示すように、DropBox for Business アプリケーションを Azure AD に追加します。

  ![Dropbox を Azure AD に追加する](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![Dropbox を Azure AD に追加する](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. アプリケーションを構成します。

  ![アプリのシングル サインオンを構成する](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. シングル サインオンの構成を選択し、ユーザーの識別子を user.mail (既定では UPN) に変更します。

4. DropBox の構成に使用する証明書をダウンロードします。

  ![証明書のダウンロード](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. DropBox の構成オプションを使用します (手順の詳細は以下の各スクリーンショットに示されています)。

6. 構成で使用する SAML シングル サインオン URL を取得します。

7. DropBox の構成ページからサインオン URL を取得します。

  ![Dropbox へのサインイン](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Dropbox のメニュー](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![折りたたまれた状態の Dropbox の認証ダイアログ](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![展開された状態の Dropbox の認証ダイアログ](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. 証明書をアップロードし、ここに SAML シングル サインオン URL を貼り付けます。

  ![SAML SSO URL の貼り付け](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Azure Portal で自動ユーザー プロビジョニングを構成します。

  ![自動ユーザー プロビジョニングをセットアップ](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. ゲスト/メンバー ユーザーは、DropBox アプリケーションにプロビジョニングされると、DropBox から個別の招待を受け取ります。 招待された人は、DropBox へのシングル サインオンを使用するには、リンクをクリックして招待に応じる必要があります。

## <a name="box"></a>Box
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Box に対するゲスト ユーザーの認証を行えるようにする方法を説明します。 この手順の途中で、メタデータを Box.com にアップロードする必要があります。

1. エンタープライズ アプリケーションから Box を追加します。

2. Configure single sign-on

  ![Box のシングル サインオンを構成する](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. まず、Microsoft Azure 管理ポータルで、[サインオン URL] が Box 用に適切に設定されていることを確認します。 これは、Box.com テナントの URL であり、https://.box.com という形式である必要があります。

4. [識別子] はこのアプリケーションには該当しませんが、必須フィールドとして表示されます。対応は不要です。

5. [ユーザー識別子] を user.mail にします (ゲスト アカウントの SSO を確実に行うため)。

6. 新しい SAML 証明書を作成します。

7. Azure Active Directory を ID プロバイダーとして使用するように Box.com テナントを構成するには、まず、Download メタデータ ファイルをダウンロードし、コンピューターにローカルに保存します (必ずアクティブにしておいてください)。

8. メタデータ ファイルを Box サポート チームに転送します。 サポート チームが、シングル サインオンを構成します。

9. Azure AD 自動ユーザー プロビジョニング用にセットアップします。

  ![Box への接続を Azure AD に承認する](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

招待される人も、Box アプリケーションからの招待に応じる必要があります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


