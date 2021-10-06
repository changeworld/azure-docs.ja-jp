---
title: ユーザーごとの Multi-Factor Authentication を有効にする - Azure Active Directory
description: ユーザーの状態を変更することで、ユーザーごとの Azure AD Multi-Factor Authentication を有効にする方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 066e0a48442825785bc2c413c0e7d40ca470f45d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353244"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>ユーザーごとの Azure AD Multi-Factor Authentication を有効にしてサインイン イベントのセキュリティを確保する

Azure AD でユーザーのサインイン イベントをセキュリティで保護するには、多要素認証 (MFA) を要求することができます。 ユーザーを保護するには、条件付きアクセス ポリシーを使って Azure AD Multi-Factor Authentication を有効にするのが推奨されるアプローチです。 条件付きアクセスは Azure AD Premium P1 または P2 の機能であり、特定のシナリオで必要に応じて MFA を要求するルールを適用できます。 条件付きアクセスの使用を開始するには、「[チュートリアル:Azure AD Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](tutorial-enable-azure-mfa.md)」を参照してください。

条件付きアクセスを使用しない Azure AD 無料テナントについては、[セキュリティの既定値を使用してユーザーを保護](../fundamentals/concept-fundamentals-security-defaults.md)できます。 ユーザーは必要に応じて MFA の入力を求められますが、独自のルールを定義して動作を制御することはできません。

代わりに、ユーザーごとの Azure AD Multi-Factor Authentication を必要に応じて各アカウントで有効にすることができます。 ユーザーを個別に有効にした場合は、サインインするたびに多要素認証が実行されます (信頼できる IP アドレスからサインインするときや、_信頼されたデバイスで MFA を記憶する_ 機能が有効なときなど、一部例外があります)。

Azure AD ライセンスに条件付きアクセスが含まれておらず、セキュリティの既定値を使用しない場合を除き、[ユーザーの状態](#azure-ad-multi-factor-authentication-user-states)を変更することは推奨されません。 MFA を有効にする他の方法の詳細については、「[Azure AD Multi-Factor Authentication の機能とライセンス](concept-mfa-licensing.md)」を参照してください。

> [!IMPORTANT]
>
> この記事では、ユーザーごとの Azure AD Multi-Factor Authentication の状態を表示および変更する方法について詳しく説明します。 条件付きアクセスまたはセキュリティの既定値を使用する場合、これらの手順を使用してユーザー アカウントを確認したり、有効にしたりしません。
>
> 条件付きアクセス ポリシーを使用して Azure AD Multi-Factor Authentication を有効にしても、ユーザーの状態は変更されません。 ユーザーが無効に見えても問題ありません。 条件付きアクセスでは、状態は変更されません。
>
> **条件付きアクセス ポリシーを使用する場合は、ユーザーごとの AD Azure Multi-Factor Authentication を有効にしたり、適用したりしないでください。**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD Multi-Factor Authentication におけるユーザーの状態

ユーザーの状態には、管理者がユーザーをユーザーごとの Azure AD Multi-Factor Authentication に登録したかどうかが反映されます。 Azure AD Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

| State | 説明 | 影響を受けるレガシ認証 | ブラウザー アプリに影響があるか | 影響を受ける先進認証 |
|:---:| --- |:---:|:--:|:--:|
| 無効 | ユーザーごとの Azure AD Multi-Factor Authentication に登録されていないユーザーの既定の状態です。 | いいえ | いいえ | いいえ |
| Enabled | ユーザーはユーザーごとの Azure AD Multi-Factor Authentication に登録されていますが、レガシ認証では引き続きパスワードを使用できます。 ユーザーが MFA 認証方法にまだ登録されていない場合は、先進認証 (Web ブラウザーなど) を使用して次回サインインするときに登録するように求められます。 | いいえ。 レガシ認証は、登録プロセスが完了するまで機能し続けます。 | はい。 セッションの有効期限が切れると、Azure AD Multi-Factor Authentication の登録が必要になります。| はい。 アクセス トークンの有効期限が切れると、Azure AD Multi-Factor Authentication の登録が必要になります。 |
| 強制 | ユーザーは、ユーザーごとに Azure AD Multi-Factor Authentication に登録されています。 ユーザーが認証方法にまだ登録されていない場合は、先進認証 (Web ブラウザーなど) を使用して次回サインインするときに登録するように求められます。 *[有効]* 状態で登録が完了したユーザーは、 *[強制]* 状態に自動的に移動されます。 | はい。 アプリはアプリ パスワードを必要とします。 | はい。 サインイン時に Azure AD Multi-Factor Authentication が必要です。 | はい。 サインイン時に Azure AD Multi-Factor Authentication が必要です。 |

すべてのユーザーの状態は、 *[無効]* から始まります。 管理者がユーザーをユーザーごとの Azure AD Multi-Factor Authentication に登録すると、ユーザーの状態は *[有効]* に変わります。 [有効] 状態のときにユーザーがサインインして登録プロセスを完了すると、ユーザーの状態は *[適用]* に変わります。 管理者は、ユーザーを状態間 ( *[強制]* から *[有効]* または *[無効]* を含む) で移動させることができます。

> [!NOTE]
> ユーザーごとの MFA がユーザーに対して再度有効にされ、ユーザーが再登録されていない場合、MFA 管理 UI で MFA の状態は *[有効]* から *[強制]* に移行されません。 管理者は、そのユーザーを *[強制]* に直接移動する必要があります。

## <a name="view-the-status-for-a-user"></a>ユーザーの状態を表示する

ユーザーの状態を表示および管理するには、次の手順を実行して Azure portal ページにアクセスします。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
1. *[Azure Active Directory]* を探して選択してから、 **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. **[Multi-Factor Authentication]** を選択します。 このメニュー オプションを表示するには、必要に応じて右にスクロールします。 次のスクリーンショット例を選択すると、完全な Azure portal ウィンドウとメニューの場所が表示されます。[![Azure AD の [ユーザー] ウィンドウから [Multi-Factor Authentication] を選択します。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 次の例に示すように、ユーザーの状態を表示する新しいページが開きます。
   ![Azure AD Multi-Factor Authentication のユーザーの状態情報の例を示すスクリーンショット](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>ユーザーの状態を変更する

ユーザーのユーザーごとの Azure AD Multi-Factor Authentication の状態を変更するには、次の手順を実行します。

1. 前の手順を使用して、[ユーザーの状態を表示し](#view-the-status-for-a-user)、Azure AD Multi-Factor Authentication の **ユーザー** ページに移動します。
1. ユーザーごとの Azure AD Multi-Factor Authentication を有効にするユーザーを検索します。 上部でビューを **[ユーザー]** に変更することが必要になる場合があります。
   ![ユーザー タブで状態を変更するユーザーを選択する](./media/howto-mfa-userstates/enable1.png)
1. 状態を変更するユーザーの名前の横にあるチェックボックスをオンにします。
1. 右側にある **[クイック操作]** で **[有効にする]** または **[無効にする]** を選択します。 次の例では、ユーザー *John Smith* の名前の横にチェックマークが付いており、使用が有効になっています。![[クイック操作] メニューの [有効にする] をクリックして、選択したユーザーを有効にする](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *[有効]* なユーザーは、Azure AD Multi-Factor Authentication に登録すると自動的に *[Enforced]\(強制\)* に切り替えられます。 ユーザーが既に登録されている場合や、レガシ認証プロトコルへの接続が中断されてもユーザーがそれを容認できる場合を除き、ユーザーの状態を *[強制]* に手動で変更しないでください。

1. 開いたポップアップ ウィンドウで選択内容を確認します。

ユーザーを有効にした後は、ユーザーにメールで通知します。 次回のサインイン時に登録を要求するプロンプトが表示されることをユーザーに伝えます。 また、最新の認証をサポートしていない非ブラウザー アプリを組織で使用している場合は、アプリ パスワードを作成する必要があります。 詳細については、開始をサポートするための [Azure AD Multi-Factor Authentication エンドユーザー ガイド](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)を参照してください。

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する

ユーザーごとの MFA の使用が有効になっていたユーザーが Azure AD Multi-Factor Authentication を強制された場合は、次の PowerShell が、条件付きアクセス ベースの Azure AD Multi-Factor Authentication への変換に役立ちます。

この PowerShell を ISE ウィンドウで実行するか、`.PS1` ファイルとして保存し、ローカルで実行します。 この操作は、[MSOnline モジュール](/powershell/module/msonline#msonline)を使用することによってのみ実行できます。 

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

## <a name="next-steps"></a>次のステップ

Azure AD Multi-Factor Authentication 設定を構成するには、「[Azure AD Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。

Azure AD Multi-Factor Authentication のユーザー設定を管理するには、「[Azure AD Multi-Factor Authentication によるユーザー設定の管理](howto-mfa-userdevicesettings.md)」を参照してください。

MFA の実行を求めるメッセージがユーザーに表示された理由、または表示されない理由について理解するには、[Azure AD Multi-Factor Authentication レポート](howto-mfa-reporting.md)に関する記事を参照してください。