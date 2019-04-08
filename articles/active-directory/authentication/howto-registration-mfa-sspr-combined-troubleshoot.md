---
title: Azure AD SSPR と MFA のための結合された登録のトラブルシューティングを行う (プレビュー)
description: Azure AD Multi-Factor Authentication とパスワード リセットのセルフサービスの結合された登録のトラブルシューティングを行う (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b247a733c6874b023d3dc8114b0538e422baccfd
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56589372"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>結合されたセキュリティ情報の登録のトラブルシューティング (プレビュー)

この記事では、エンドユーザーによって報告される、結合された登録エクスペリエンスに関する問題のトラブルシューティングを行う管理者のガイドとなる情報を提供します。

|     |
| --- |
| Azure Multi-Factor Authentication と Azure AD パスワード リセットのセルフサービスのための結合されたセキュリティ情報の登録は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="audit-logs"></a>監査ログ

結合された登録について記録されたイベントは、Azure AD 監査ログの "認証方法" カテゴリに分類されます。

![ディレクトリ内の新しいユーザーに関するいくかのセキュリティ情報の登録イベントを示す、Azure AD 監査ログ インターフェイス](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

結合された登録で生成されるすべての監査イベントを以下にリストします。

| アクティビティ | Status | 理由 | 説明 |
| --- | --- | --- | --- |
| ユーザーが必要なすべてのセキュリティ情報を登録した | Success | ユーティリティが必要なすべてのセキュリティ情報を登録しました。 | このイベントは、ユーザーが登録を正常に完了したときに発生します。|
| ユーザーが必要なすべてのセキュリティ情報を登録した | 失敗 | ユーザーがセキュリティ情報の登録を取り消しました。 | このイベントは、ユーザーが中断モードから登録を取り消したときに発生します。|
| ユーザーがセキュリティ情報を登録した | Success | ユーザーが "方法" を登録しました。 | このイベントは、ユーザーが個々の方法を登録したときに発生します。 "方法" は、認証アプリ、電話、メール、セキュリティの質問、アプリのパスワード、代替の電話などにすることができます。| 
| ユーザーがセキュリティ情報を確認した | Success | ユーザーが正常にセキュリティ情報を確認しました。 | このイベントは、ユーザーがセキュリティ情報の確認ページで "良い" をクリックしたときに発生します。|
| ユーザーがセキュリティ情報を確認した | 失敗 | ユーザーがセキュリティ情報を確認できませんでした。 | このイベントは、ユーザーがセキュリティ情報の確認ページで "良い" をクリックしたが、バックエンドで何かが失敗したときに発生します。|
| ユーザーがセキュリティ問題を削除した | Success | ユーザーが "方法" を削除しました。 | このイベントは、ユーザーが個々の方法を削除したときに発生します。 "方法" は、認証アプリ、電話、メール、セキュリティの質問、アプリのパスワード、代替の電話などにすることができます。|
| ユーザーがセキュリティ問題を削除した | 失敗 | ユーザーが "方法" を削除できませんでした。 | このイベントは、ユーザーが方法を削除しようとしたが、何らかの理由で失敗したときに発生します。 "方法" は、認証アプリ、電話、メール、セキュリティの質問、アプリのパスワード、代替の電話などにすることができます。|
| ユーザーが既定のセキュリティ情報を変更した | Success | ユーザーが既定のセキュリティ情報を "方法" に変更しました。 | このイベントは、ユーザーが既定の方法を変更したときに発生します。 "方法" は、認証アプリ通知、認証アプリまたはトークンからのコード、+X XXXXXXXXXX の呼び出し、+X XXXXXXXXX へのコードの SMS 送信などにすることができます。|
| ユーザーが既定のセキュリティ情報を変更した | 失敗 | ユーザーが既定のセキュリティ情報を "方法" に変更できませんでした。 | このイベントは、ユーザーが既定の方法を変更しようとしたが、何らかの理由で失敗したときに発生します。 "方法" は、認証アプリ通知、認証アプリまたはトークンからのコード、+X XXXXXXXXXX の呼び出し、+X XXXXXXXXX へのコードの SMS 送信などにすることができます。|

## <a name="troubleshooting-interrupt-mode"></a>割り込みモードのトラブルシューティング

| 症状 | トラブルシューティングの手順 |
| --- | --- |
| 予期していた方法が表示されません。 | 1.ユーザーに Azure AD 管理者ロールがあるかどうかを確認します。 ある場合は、SSPR 管理者ポリシーの相違点を確認します。 <br> 2.ユーザーが中断される理由が、MFA 登録の適用か SSPR 登録の適用であるかを確認します。 どの方法が表示される必要があるかを判断するために、結合された登録モードでフローチャートを確認します。 <br> 手順 3.MFA または SSPR のポリシーがどのくらい最近に変更されたかを判別します。 最近、変更が加えられた場合、更新されたポリシーが反映されるまでしばらく時間がかかることがあります。|

## <a name="troubleshooting-manage-mode"></a>管理モードのトラブルシューティング

| 症状 | トラブルシューティングの手順 |
| --- | --- |
| 特定の方法を追加する選択肢がありません。 | 1.方法が MFA または SSPR に対して有効になっているかどうかを判別します。 <br> 2.方法が有効になっている場合、ポリシーを再度保存し、1、2 時間待ってからもう一度テストします。 <br> 手順 3.方法が有効になっている場合は、ユーザーが設定を許可されている、その最大数の方法を既に設定していないことを確認します。|

## <a name="disable-combined-registration"></a>結合された登録を無効にする

ユーザーが自分の電話番号やモバイル アプリを新しい結合されたエクスペリエンスに登録すると、サービスによってそのユーザーに対するこれらの方法に一連のフラグ (StrongAuthenticationMethods) が設定されます。 この機能により、ユーザーは、MFA が必要な場合は常に、これらの方法で Azure Multi-factor Authentication (MFA) を実行できます。

新しいエクスペリエンスを使ってユーザーが登録した方法には、StrongAuthenticationMethods プロパティが設定されます。 管理者がプレビューを有効にした場合、ユーザーは新しいエクスペリエンスを使って登録し、その後、管理者がプレビューを無効にすると、ユーザーは知らないうちに MFA にも登録される場合があります。

結合された登録を完了したユーザーが現在のパスワード リセットのセルフサービス (SSPR) 登録ページ ([https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)) に移動した場合、そのページにアクセスする前に MFA を実行するように求められます。 この手順は、技術的な観点からは予想される動作ですが、以前に SSPR のみに登録したユーザーにとっては新しい動作です。 この余分な手順により、ユーザーのセキュリティ体制はセキュリティ レベルが追加されることで向上しますが、管理者としてはユーザーが MFA を実行できないようにユーザーをロールバックしたい場合があります。  

### <a name="how-to-roll-back-users"></a>ユーザーをロールバックする方法

管理者がユーザーの MFA 設定をリセットできるように、ユーザーのモバイル アプリと電話番号の StrongAuthenticationMethods プロパティをクリアする PowerShell スクリプトが作成されています。 ユーザーに対してこのスクリプトを実行すると、ユーザーは必要に応じて MFA への再登録が必要になります。 影響を受けるすべてのユーザーをロールバックする前に、1 人または 2 人のユーザーでロールバックをテストすることをお勧めします。

以下の手順では、ユーザーまたはユーザーのグループをロールバックします。

#### <a name="prerequisites"></a>前提条件

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

#### <a name="rollback"></a>ロールバック

PowerShell ウィンドウで、強調表示されている場所を更新してから次のコマンドを実行します。 求められたら、グローバル管理者の資格情報を入力します。 スクリプトでは、各ユーザーの更新操作の結果が出力されます。

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>プレビュー エクスペリエンスを無効にする

ユーザーのプレビュー エクスペリエンスを無効にするには、次の手順のようにします。

1. Azure portal に全体管理者またはユーザー管理者としてサインインします。
2. **Azure Active Directory**、**[ユーザー設定]**、**[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** で、セレクターを **[なし]** に設定して、**[保存]** をクリックします。

ユーザーは、プレビュー エクスペリエンスを使った登録を求められなくなります。

## <a name="next-steps"></a>次の手順

[パスワード リセットのセルフサービスと Azure Multi-Factor Authentication の結合された登録のパブリック プレビューについてさらに学習する](concept-registration-mfa-sspr-combined.md)
