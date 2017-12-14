---
title: "Windows 10 ログイン画面からの Azure AD SSPR | Microsoft Docs"
description: "Windows 10 ログイン画面の Azure AD パスワード リセットと PIN を忘れた場合の対処方法を構成します。"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 46d5a673c530c6fbcbd1a88a8e74a753ae04998b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>ログイン画面からの Azure AD パスワード リセット

Azure AD のセルフ サービス パスワード リセット (SSPR) をデプロイしたにもかかわらず、パスワードを忘れたユーザーからヘルプデスクに問い合わせの電話がかかってきます。 ヘルプデスクに電話がかかってくる理由は、ユーザーが Web ブラウザーで SSPR にアクセスできないためです。

新しい Windows 10 Fall Creators Update では、Azure AD に参加済みのデバイスを所有しているユーザーのログイン画面に "パスワードのリセット" リンクが表示されます。 このリンクをユーザーがクリックすると、使い慣れたセルフ サービス パスワード リセット (SSPR) 機能が利用できます。

ユーザーが Windows 10 のログイン画面から自分の Azure AD パスワードをリセットできるようにするには、次の要件を満たしておく必要があります。

* [Azure AD に参加](device-management-azure-portal.md)している Windows 10 (バージョン 1709 以降) のクライアント。
* Azure AD のセルフ サービス パスワード リセットを有効にする必要があります。
* "パスワードのリセット" リンクを有効にするための設定を、次のいずれかの方法で構成し、デプロイしてください。
   * [Intune デバイス構成プロファイル](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [レジストリ キー](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Intune を使用して "パスワードのリセット" リンクを構成する

### <a name="create-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを作成する

1. [Azure Portal](https://portal.azure.com) にログインし、**[Intune]** をクリックします。
2. **[デバイス構成]** > **[プロファイル]** > **[プロファイルの作成]** の順に移動して、新しいデバイス構成プロファイルを作成します。
   * わかりやすいプロファイル名を入力します。
   * 必要に応じて、そのプロファイルについてのわかりやすい説明を入力します。
   * プラットフォーム: **Windows 10 以降**
   * プロファイルの種類: **カスタム**

   ![CreateProfile][CreateProfile]

3. **設定**を構成します。
   * "パスワードのリセット" リンクを有効にするには、次の OMA-URI 設定を**追加**します。
      * 設定の内容を説明するわかりやすい名前を入力します。
      * 必要に応じて、その設定についてのわかりやすい説明を入力します。
      * **OMA-URI**: `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` に設定します。
      * **データ型**: **整数**に設定します。
      * **値**: **1** に設定します。
      * **[OK]**
   * **[OK]**
4. **[作成]**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを割り当てる

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>デバイス構成ポリシーの適用先となるグループを作成する

1. [Azure Portal](https://portal.azure.com) にログインし、**[Azure Active Directory]** をクリックします。
2. **[ユーザーとグループ]** > **[すべてのグループ]** > **[新しいグループ]** の順に選択します。
3. グループの名前を入力し、**[メンバーシップの種類]** で **[割り当て済み]** を選択します。 
   * **[メンバー]** で、ポリシーの適用先となる Azure AD 参加済みの Windows 10 デバイスを選択します。
   * **[選択]** をクリックします。
4. **[作成]**

グループの作成の詳細については、「[Azure Active Directory のグループによるリソースへのアクセス管理](active-directory-manage-groups.md)」の記事を参照してください。

#### <a name="assign-device-configuration-policy-to-device-group"></a>デバイス構成ポリシーをデバイス グループに割り当てる

1. [Azure Portal](https://portal.azure.com) にログインし、**[Intune]** をクリックします。
2. 作成済みのデバイス構成プロファイルを探します。**[デバイス構成]** > **[プロファイル]** に移動し、先ほど作成したプロファイルをクリックしてください。
3. デバイスのグループにプロファイルを割り当てる 
   * **[割り当て]** をクリックし、**[含める]** > **[含めるグループを選択]** の順に選択します。
   * 先ほど作成したグループを選択し、**[選択]** をクリックします。
   * [設定] メニューの **[保存]**

   ![割り当て][Assignment]

以上で Intune を使用したデバイス構成ポリシーの作成と割り当ては完了です。これでログオン画面の "パスワードのリセット" リンクが有効になります。

## <a name="configure-reset-password-link-using-the-registry"></a>レジストリを使用して "パスワードのリセット" リンクを構成する

次の方法は、設定の変更をテストする目的でのみ使用することをお勧めします。

1. 管理者資格情報を使用して Azure AD 参加済みデバイスにログインします。
2. **regedit** を管理者として実行します。
3. 次のレジストリ キーを設定します。
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>ユーザーに表示される画面

ポリシーの構成と割り当てが完了すると、画面にどのような変化が現れるのでしょうか。 パスワードをリセットできることが、ログオン画面にどのように示されるのかを説明します。

![ログイン画面][LoginScreen]

ユーザーがログインを試みると、ログオン画面に "パスワードのリセット" というリンクが表示されます。このリンクを選択することで、セルフ サービスによるパスワード リセット機能が作動します。 ユーザーがパスワードをリセットするには、この機能を使用するだけでよく、別のデバイスを使用して Web ブラウザーにアクセスする必要はありません。

[職場または学校アカウントのパスワードをリセットする方法](active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)に関するページで、この機能の使い方がユーザー向けに説明されています。

## <a name="common-issues"></a>一般的な問題

Hyper-V を使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* テストに使用している VM に移動して **[表示]** をクリックし、**[拡張セッション]** チェック ボックスをオフにしてください。

リモート デスクトップを使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* リモート デスクトップからのパスワードのリセットは現在サポートされていません。

## <a name="next-steps"></a>次のステップ
次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [SSPR をデプロイする方法](active-directory-passwords-best-practices.md)
* [ログイン画面からの PIN のリセットを有効にする方法](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [MDM 認証ポリシーの詳細](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "Windows 10 ログオン画面に表示される "パスワードのリセット" リンクを有効にするための Intune デバイス構成プロファイルを作成する"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "Intune デバイス構成ポリシーを Windows 10 デバイスのグループに割り当てる"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Windows 10 ログオン画面に表示される "パスワードのリセット" リンク"
