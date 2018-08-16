---
title: Azure AD SSPR と MFA の集中型登録を無効にする (パブリック プレビュー)
description: Azure AD Multi-Factor Authenticaiton とセルフサービスのパスワード リセット登録を無効にします (パブリック プレビュー)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 3ce08f67f001a7c43602627b9eeda3ad60f867c1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623220"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Azure AD の集中型登録を無効にする (パブリック プレビュー)

ユーザーが自分の電話番号やモバイル アプリを新しい集約型エクスペリエンスに登録すると、サービスによってそのユーザーに対するこれらの方法に一連のフラグ (StrongAuthenticationMethods) が設定されます。 この機能により、ユーザーは、MFA が必要な場合は常に、これらの方法で Azure Multi-factor Authentication (MFA) を実行できます。

新しいエクスペリエンスを使ってユーザーが登録した方法には、StrongAuthenticationMethods プロパティが設定されます。 この動作は、パブリック プレビューが利用可能になるときにも発生します。 管理者がプレビューを有効にした場合、ユーザーは新しいエクスペリエンスを使って登録し、その後、管理者がプレビューを無効にすると、ユーザーは知らないうちに MFA にも登録される場合があります。

集中型登録を完了したユーザーが現在の SSPR 登録ページ ([https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)) に移動した場合、そのページにアクセスする前に MFA を実行するように求められます。 この手順は、技術的な観点からは予想される動作ですが、以前に SSPR のみに登録したユーザーにとっては新しい動作です。 この余分な手順により、ユーザーのセキュリティ体制はセキュリティ レベルが追加されることで向上しますが、管理者としてはユーザーが MFA を実行できないようにユーザーをロールバックしたい場合があります。  

## <a name="how-to-roll-back-users"></a>ユーザーをロールバックする方法

管理者がユーザーの MFA 設定をリセットできるように、ユーザーのモバイル アプリと電話番号の StrongAuthenticationMethods プロパティをクリアする PowerShell スクリプトが作成されています。 ユーザーに対してこのスクリプトを実行すると、ユーザーは必要に応じて MFA への再登録が必要になります。 影響を受けるすべてのユーザーをロールバックする前に、1 人または 2 人のユーザーでロールバックをテストすることをお勧めします。

以下の手順では、ユーザーまたはユーザーのグループをロールバックします。

### <a name="pre-requisites"></a>前提条件

1. 適切な Azure AD PowerShell モジュールをインストールする必要があります。 PowerShell ウィンドウで、次のコマンドを実行してモジュールをインストールします。

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 影響を受けるユーザー オブジェクト ID のリストを、1 行に 1 つの ID が記述されたテキスト ファイルとしてコンピューターに保存します。 ファイルの場所を書き留めておきます。
1. 次のスクリプトをコンピューターに保存し、スクリプトの場所を書き留めておきます。

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>ロールバック

PowerShell ウィンドウで、強調表示されている場所を更新してから次のコマンドを実行します。 求められたら、グローバル管理者の資格情報を入力します。 スクリプトでは、各ユーザーの更新操作の結果が出力されます。

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>プレビュー エクスペリエンスを無効にする

ユーザーのプレビュー エクスペリエンスを無効にするには、次の手順のようにします。

1. Azure portal に全体管理者またはユーザー管理者としてサインインします。
2. **Azure Active Directory**、**[ユーザー設定]**、**[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** で、セレクターを **[なし]** に設定して、**[保存]** をクリックします。

ユーザーは、プレビュー エクスペリエンスを使った登録を求められなくなります。

## <a name="next-steps"></a>次の手順

[セルフサービスのパスワード リセットと Azure Multi-Factor Authentication の集中型登録のパブリック プレビューについてさらに学習する](concept-registration-mfa-sspr-converged.md)