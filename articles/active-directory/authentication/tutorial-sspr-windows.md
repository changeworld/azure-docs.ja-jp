---
title: Windows 10 ログイン画面からの Azure AD SSPR
description: このチュートリアルでは、Windows 10 のログイン画面でパスワードをリセットできるようにして、ヘルプデスクへの電話を減らします。
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 27f271a20af2bb9910f1cf7d63e6033d78e67b83
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41917735"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>チュートリアル: ログイン画面からの Azure AD パスワードのリセット

このチュートリアルでは、ユーザーが Windows 10 のログイン画面から自分のパスワードをリセットできるようにします。 新しい Windows 10 April 2018 Update では、**Azure AD に参加済み**または**ハイブリッド Azure AD に参加済み**のデバイスを所有しているユーザーが、ログイン画面の "パスワードのリセット" リンクを使用できます。 このリンクをユーザーがクリックすると、使い慣れたセルフサービスによるパスワードのリセット (SSPR) 機能が利用できます。

> [!div class="checklist"]
> * Intune を使用して "パスワードのリセット" リンクを構成する
> * 必要に応じて Windows レジストリを使用して構成する
> * ユーザーに何が表示されるかを理解する

## <a name="prerequisites"></a>前提条件

* 以下の条件を満たす Windows 10 April 2018 Update 以降のクライアント。
   * [Azure AD 参加済み](../device-management-azure-portal.md)または 
   * [ハイブリッド Azure AD 参加済み](../device-management-hybrid-azuread-joined-devices-setup.md)
* Azure AD のセルフ サービス パスワード リセットを有効にする必要があります。

## <a name="configure-reset-password-link-using-intune"></a>Intune を使用して "パスワードのリセット" リンクを構成する

ログイン画面からパスワードをリセットできるようにする構成変更を Intune を使用してデプロイするのは、最も柔軟な方法です。 Intune では、定義する特定のマシンのグループに構成変更をデプロイすることができます。 この方法では、デバイスの Intune 登録が必要です。

### <a name="create-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを作成する

1. [Azure portal](https://portal.azure.com) にサインインし、**[Intune]** をクリックします。
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

1. [Azure portal](https://portal.azure.com) にサインインし、**[Azure Active Directory]** をクリックします。
2. **[ユーザーとグループ]** > **[すべてのグループ]** > **[新しいグループ]** の順に選択します。
3. グループの名前を入力し、**[メンバーシップの種類]** で **[割り当て済み]** を選択します。
   * **[メンバー]** で、ポリシーの適用先となる Azure AD 参加済みの Windows 10 デバイスを選択します。
   * **[選択]** をクリックします。
4. **[作成]**

グループの作成の詳細については、「[Azure Active Directory のグループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)」の記事を参照してください。

#### <a name="assign-device-configuration-policy-to-device-group"></a>デバイス構成ポリシーをデバイス グループに割り当てる

1. [Azure portal](https://portal.azure.com) にサインインし、**[Intune]** をクリックします。
2. 作成済みのデバイス構成プロファイルを探します。**[デバイス構成]** > **[プロファイル]** に移動し、先ほど作成したプロファイルをクリックしてください。
3. デバイスのグループにプロファイルを割り当てる 
   * **[割り当て]** をクリックし、**[含める]** > **[含めるグループを選択]** の順に選択します。
   * 先ほど作成したグループを選択し、**[選択]** をクリックします。
   * [設定] メニューの **[保存]**

   ![割り当て][Assignment]

以上で Intune を使用したデバイス構成ポリシーの作成と割り当ては完了です。これでログイン画面の "パスワードのリセット" リンクが有効になります。

## <a name="configure-reset-password-link-using-the-registry"></a>レジストリを使用して "パスワードのリセット" リンクを構成する

1. 管理者の資格情報を使用して Windows PC にログインします
2. **regedit** を管理者として実行します。
3. 次のレジストリ キーを設定します。
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>ユーザーに表示される画面

ポリシーの構成と割り当てが完了すると、画面にどのような変化が現れるのでしょうか。 ログイン画面でパスワードをリセットできることを、ユーザーはどのようにして知るのでしょうか。

![ログイン画面][LoginScreen]

ユーザーがログインを試みると、ログイン画面に "パスワードのリセット" というリンクが表示されます。このリンクを選択することで、セルフ サービスによるパスワードのリセット機能が作動します。 ユーザーがパスワードをリセットするには、この機能を使用するだけでよく、別のデバイスを使用して Web ブラウザーにアクセスする必要はありません。
ユーザーがログインを試みると、ログイン画面に "パスワードのリセット" というリンクが表示されます。このリンクを選択することで、セルフ サービスによるパスワードのリセット機能が作動します。 ユーザーがパスワードをリセットするには、この機能を使用するだけでよく、別のデバイスを使用して Web ブラウザーにアクセスする必要はありません。

[職場または学校アカウントのパスワードをリセットする方法](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)に関するページで、この機能の使い方がユーザー向けに説明されています。

## <a name="common-issues"></a>一般的な問題

Hyper-V を使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* テストに使用している VM に移動して **[表示]** をクリックし、**[拡張セッション]** チェック ボックスをオフにしてください。

リモート デスクトップを使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* リモート デスクトップからのパスワードのリセットは現在サポートされていません。

Windows のロック画面がレジストリ キーまたはグループ ポリシーを使って無効にされている場合、"**パスワードのリセット**" が利用できなくなります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルの一部として構成した機能を今後は使用しない場合は、作成した Intune デバイス構成プロファイルまたはレジストリ キーを削除します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ユーザーが Windows 10 のログイン画面から自分のパスワードをリセットできるようにしました。 次のチュートリアルに進み、Azure Identity Protection をセルフサービスによるパスワードのリセットと Multi-Factor Authentication のエクスペリエンスに統合する方法を参照してください。

> [!div class="nextstepaction"]
> [サインイン時にリスクを評価する](tutorial-risk-based-sspr-mfa.md)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Windows 10 ログイン画面に表示される "パスワードのリセット" リンクを有効にするための Intune デバイス構成プロファイルを作成する"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune デバイス構成ポリシーを Windows 10 デバイスのグループに割り当てる"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Windows 10 ログイン画面に表示される "パスワードのリセット" リンク"
