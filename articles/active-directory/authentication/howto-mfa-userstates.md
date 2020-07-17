---
title: ユーザーごとの Multi-Factor Authentication を有効にする - Azure Active Directory
description: ユーザーの状態を変更することで、ユーザーごとの Azure Multi-Factor Authentication を有効にする方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0db72e30fbced17665c112ad56510d7c2ca23d12
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639622"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>ユーザーごとの Azure Multi-Factor Authentication を有効にしてサインイン イベントのセキュリティを確保する

Azure AD で多要素認証を要求することによってユーザーのサインイン イベントのセキュリティを確保するには、2 つの方法があります。 推奨される最初のオプションは、特定の条件下で多要素認証を要求する条件付きアクセス ポリシーを設定することです。 2 つ目のオプションは、個々のユーザーに Azure Multi-Factor Authentication を有効にする方法です。 ユーザーを個別に有効にした場合は、サインインするたびに多要素認証が実行されます (信頼できる IP アドレスからサインインするときや、_記憶されたデバイス_の機能が有効なときなど、一部例外があります)。

> [!NOTE]
> 条件付きアクセス ポリシーを使って Azure Multi-Factor Authentication を有効にするのが推奨されるアプローチです。 ライセンスに条件付きアクセスが含まれていない場合 (この場合、ユーザーはサインインするたびに MFA を実行する必要があります) を除き、ユーザーの状態を変更することは推奨されなくなりました。
>
> 条件付きアクセスの使用を開始するには、「[チュートリアル: Azure Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](tutorial-enable-azure-mfa.md)」を参照してください。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication におけるユーザーの状態

Azure Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

> [!IMPORTANT]
> 条件付きアクセス ポリシーを使用して Azure Multi-Factor Authentication を有効にしても、ユーザーの状態は変更されません。 ユーザーが無効に見えても問題ありません。 条件付きアクセスでは、状態は変更されません。
>
> **条件付きアクセス ポリシーを使用している場合は、ユーザーを有効または強制の状態にしないでください。**

| Status | 説明 | 非ブラウザー アプリに影響があるか | ブラウザー アプリに影響があるか | 影響を受ける先進認証 |
|:---:| --- |:---:|:--:|:--:|
| 無効 | Azure Multi-Factor Authentication に登録されていない、新しいユーザーの既定の状態です。 | いいえ | いいえ | いいえ |
| Enabled | ユーザーは多要素認証に登録されていますが、まだ登録されていません。 次回のサインイン時に登録することを求められます。 | いいえ。  これらは登録プロセスが完了するまで機能し続けます。 | はい。 セッションの有効期限が切れると、Azure Multi-Factor Authentication の登録が必要になります。| はい。 アクセス トークンの有効期限が切れると、Azure Multi-Factor Authentication の登録が必要になります。 |
| 強制 | ユーザーは登録されており、Azure Multi-Factor Authentication の登録プロセスが完了しています。 | はい。 アプリはアプリ パスワードを必要とします。 | はい。 ログイン時に Azure Multi-Factor Authentication が必要です。 | はい。 ログイン時に Azure Multi-Factor Authentication が必要です。 |

ユーザーの状態には、管理者がユーザーを Azure Multi-Factor Authentication に登録したかどうか、およびユーザーが登録プロセスを完了したかどうかが反映されます。

すべてのユーザーの状態は、 *[無効]* から始まります。 管理者がユーザーを Azure Multi-Factor Authentication に登録すると、ユーザーの状態は *[有効]* に変わります。 [有効] 状態のときにユーザーがサインインして登録プロセスを完了すると、ユーザーの状態は *[適用]* に変わります。

> [!NOTE]
> 電話や電子メールなどの登録の詳細が既にあるユーザー オブジェクトで MFA を再度有効にする場合には、管理者は Azure portal または PowerShell を使用して MFA を再登録する必要があります。 ユーザーが再登録していない場合、MFA の状態は *有効* から MFA 管理 UI で *強制* に移行されません。

## <a name="view-the-status-for-a-user"></a>ユーザーの状態を表示する

ユーザーの状態を表示および管理できる Azure portal ページにアクセスするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
1. *[Azure Active Directory]* を探して選択してから、 **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. **[Multi-Factor Authentication]** を選択します。 このメニュー オプションを表示するには、必要に応じて右にスクロールします。 次のスクリーンショット例を選択すると、完全な Azure portal ウィンドウとメニューの場所が表示されます。[![](media/howto-mfa-userstates/selectmfa-cropped.png "Azure AD の [ユーザー] ウィンドウから [Multi-Factor Authentication] を選択します")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 次の例に示すように、ユーザーの状態を表示する新しいページが開きます。
   ![Azure Multi-Factor Authentication のユーザーの状態情報の例を示すスクリーンショット](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>ユーザーの状態を変更する

ユーザーの Azure Multi-Factor Authentication の状態を変更するには、次の手順を実行します。

1. 前述の手順を使用して、Azure Multi-Factor Authentication の**ユーザー** ページを取得します。
1. Azure Multi-Factor Authentication を有効にするユーザーを検索します。 上部でビューを **[ユーザー]** に変更することが必要になる場合があります。
   ![ユーザー タブで状態を変更するユーザーを選択する](./media/howto-mfa-userstates/enable1.png)
1. 状態を変更するユーザーの名前の横にあるチェックボックスをオンにします。
1. 右側にある **[クイック操作]** で **[有効にする]** または **[無効にする]** を選択します。 次の例では、ユーザー *John Smith* の名前の横にチェックマークが付いており、使用が有効になっています。![[クイック操作] メニューの [有効にする] をクリックして、選択したユーザーを有効にする](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *[有効]* なユーザーは、Azure Multi-Factor Authentication に登録すると自動的に *[Enforced]\(強制\)* に切り替えられます。 ユーザーの状態を手動で *[Enforced]\(強制\)* に変更しないでください。

1. 開いたポップアップ ウィンドウで選択内容を確認します。

ユーザーを有効にした後は、ユーザーにメールで通知します。 次回のサインイン時に登録を要求するプロンプトが表示されることをユーザーに伝えます。 また、最新の認証をサポートしていない非ブラウザー アプリを組織で使用している場合は、アプリ パスワードを作成する必要があります。 詳細については、開始をサポートするための [Azure Multi-Factor Authentication エンドユーザー ガイド](../user-help/multi-factor-authentication-end-user.md)を参照してください。

## <a name="change-state-using-powershell"></a>PowerShell を使用して状態を変更する

[Azure AD PowerShell](/powershell/azure/overview) を使用してユーザーの状態を変更するには、ユーザー アカウントの `$st.State` パラメーターを変更します。 ユーザー アカウントの状態は 3 つあります。

* *有効*
* *Enforced (強制)*
* *Disabled*  

ユーザーを直接 "*適用*" の状態に移さないでください。 そのようにしても、ユーザーは Azure Multi-Factor Authentication の登録を終えておらず、[アプリのパスワード](howto-mfa-mfasettings.md#app-passwords)を取得していないため、ブラウザーベースでないアプリは動作を停止します。

使用を開始するには、以下のように [Install-Module](/powershell/module/powershellget/install-module) を使用して *MSOnline* モジュールをインストールします。

```PowerShell
Install-Module MSOnline
```

次に、[Connect-MsolService](/powershell/module/msonline/connect-msolservice) を使用して接続します。

```PowerShell
Connect-MsolService
```

次の PowerShell スクリプトの例では、 *bsimon@contoso.com* という名前の単独のユーザーに対して MFA を有効にします。

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

一括でユーザーを有効にする必要がある場合は、PowerShell の使用をお勧めします。 次のスクリプトでは、ユーザーのリストをループ処理し、それらのアカウントに対して MFA を有効にします。 次のように、ユーザー アカウントを定義し、`$users` の最初の行で設定します。

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

MFA を無効にするために、次の例では [Get-MsolUser](/powershell/module/msonline/get-msoluser) でユーザーを取得し、[Set-MsolUser](/powershell/module/msonline/set-msoluser) を使用して、定義済みユーザーに対して設定されている *StrongAuthenticationRequirements* を削除します。

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

次のように [Set-MsolUser](/powershell/module/msonline/set-msoluser) を使用して、ユーザーの MFA を直接無効にすることもできます。

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する

次の PowerShell が、条件付きアクセス ベースの Azure Multi-Factor Authentication への変換に役立ちます。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 最近、この動作とこの PowerShell スクリプトを変更しました。 以前は、このスクリプトにより、MFA メソッドが保存され、MFA が無効化され、メソッドが復元されました。 無効化の既定の動作でメソッドが消去されないため、これは不要になりました。
>
> 電話や電子メールなどの登録の詳細が既にあるユーザー オブジェクトで MFA を再度有効にする場合には、管理者は Azure portal または PowerShell を使用して MFA を再登録する必要があります。 ユーザーが再登録していない場合、MFA の状態は *有効* から MFA 管理 UI で *強制* に移行されません。

## <a name="next-steps"></a>次のステップ

信頼できる IP、カスタム音声メッセージ、不正アクセスのアラートなどの Azure Multi-Factor Authentication 設定を構成するには、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。 Azure Multi-Factor Authentication のユーザー設定を管理するには、「[Azure Multi-Factor Authentication によるユーザー設定の管理](howto-mfa-userdevicesettings.md)」を参照してください。

MFA の実行を求めるメッセージがユーザーに表示された理由、または表示されない理由について理解するには、[Azure Multi-Factor Authentication レポート](howto-mfa-reporting.md)に関する記事を参照してください。
