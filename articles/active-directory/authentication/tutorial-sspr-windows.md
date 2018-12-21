---
title: Windows 10 ログイン画面からの Azure AD SSPR
description: このチュートリアルでは、Windows 10 のログイン画面でパスワードをリセットできるようにして、ヘルプデスクへの電話を減らします。
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5c40e6c681a4f37c61519040eb32531d3c8f071c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437148"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>チュートリアル: ログイン画面からの Azure AD パスワード リセット

このチュートリアルでは、ユーザーが Windows 10 のログイン画面から自分のパスワードをリセットできるようにします。 新しい Windows 10 April 2018 Update では、**Azure AD に参加済み**または**ハイブリッド Azure AD に参加済み**のデバイスを所有しているユーザーが、ログイン画面の "パスワードのリセット" リンクを使用できます。 このリンクをユーザーがクリックすると、使い慣れたセルフサービスによるパスワードのリセット (SSPR) 機能が利用できます。

> [!div class="checklist"]
> * Intune を使用して "パスワードのリセット" リンクを構成する
> * 必要に応じて Windows レジストリを使用して構成する
> * ユーザーに何が表示されるかを理解する

## <a name="prerequisites"></a>前提条件

* 以下の条件を満たす Windows 10 April 2018 Update 以降のクライアント。
   * [Azure AD 参加済みマシン](../device-management-azure-portal.md)または
   * [Hybrid Azure AD 参加済みマシン](../device-management-hybrid-azuread-joined-devices-setup.md)と、ドメイン コントローラーへのネットワーク接続。
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

3. **設定**を構成します。
   * "パスワードのリセット" リンクを有効にするには、次の OMA-URI 設定を**追加**します。
      * 設定の内容を説明するわかりやすい名前を入力します。
      * 必要に応じて、その設定についてのわかりやすい説明を入力します。
      * **OMA-URI**: `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` に設定します。
      * **データ型**: **整数**に設定します。
      * **値**: **1** に設定します。
      *  **[OK]**
   *  **[OK]**
4.  **[作成]**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを割り当てる

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>デバイス構成ポリシーの適用先となるグループを作成する

1. [Azure portal](https://portal.azure.com) にサインインし、**[Azure Active Directory]** をクリックします。
2. **[ユーザーとグループ]** > **[すべてのグループ]** > **[新しいグループ]** の順に選択します。
3. グループの名前を入力し、**[メンバーシップの種類]** で **[割り当て済み]** を選択します。
   * **[メンバー]** で、ポリシーの適用先となる Azure AD 参加済みの Windows 10 デバイスを選択します。
   * **[選択]** をクリックします。
4.  **[作成]**

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

[職場または学校アカウントのパスワードをリセットする方法](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)に関するページで、この機能の使い方がユーザー向けに説明されています。

Azure AD 監査ログには、パスワードのリセットが発生した IP アドレスと ClientType に関する情報が含まれます。

![Azure AD 監査ログにおけるログオン画面のパスワードのリセットの例](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

## <a name="limitations"></a>制限事項

Hyper-V を使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* テストに使用している VM に移動して **[表示]** をクリックし、**[拡張セッション]** チェック ボックスをオフにしてください。

リモート デスクトップまたは拡張 VM セッションを使用してこの機能をテストすると、"パスワードのリセット" リンクが表示されません。

* リモート デスクトップからのパスワードのリセットは現在サポートされていません。

ポリシーにより Ctrl + Alt + Del キーが必須になっている場合、またはロック画面の通知がオフになっている場合、"**パスワードのリセット**" が正しく機能しません。

次のポリシー設定を選択すると、パスワードのリセット機能が無効になります

   * HideFastUserSwitching を有効または 1 に設定する
   * DontDisplayLastUserName を有効または 1 に設定する
   * NoLockScreen を有効または 1 に設定する
   * EnableLostMode をデバイスに対して設定する
   * Explorer.exe をカスタム シェルで置き換える

この機能は、802.1x ネットワーク認証がデプロイされ、[ユーザー ログオンの直前に実行する] オプションが有効になっているネットワークでは動作しません。 802.1x ネットワーク認証がデプロイされているネットワークでこの機能を有効にするには、マシン認証を使用することをお勧めします。

お使いの Windows 10 マシンがプロキシ サーバーまたはファイアウォールの内側にある場合は、passwordreset.microsoftonline.com および ajax.aspnetcdn.com への HTTPS トラフィック (443) を許可する必要があります。

ハイブリッド ドメイン参加済みのシナリオでは、SSPR ワークフローが完了する場所にシナリオが存在し、Active Directory のドメイン コントローラーが必要ありません。 ドメイン コントローラーとの接続は、新しいパスワードを初めて使用するときに必要です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルの一部として構成した機能を今後は使用しない場合は、作成した Intune デバイス構成プロファイルまたはレジストリ キーを削除します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ユーザーが Windows 10 のログイン画面から自分のパスワードをリセットできるようにしました。 次のチュートリアルに進み、Azure Identity Protection をセルフサービスによるパスワードのリセットと Multi-Factor Authentication のエクスペリエンスに統合する方法を参照してください。

> [!div class="nextstepaction"]
> [サインイン時にリスクを評価する](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Intune デバイス構成ポリシーを Windows 10 デバイスのグループに割り当てる"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Windows 10 ログイン画面に表示される "パスワードのリセット" リンク"
