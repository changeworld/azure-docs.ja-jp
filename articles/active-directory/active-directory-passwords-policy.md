---
title: "ポリシー: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセット ポリシーのオプション"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD セルフサービスによるパスワードのリセット"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 9d61f46070e6956c60f1135b98a9ebe71011b922
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory のパスワード ポリシーと制限

この記事では、Azure AD テナントに格納されているユーザー アカウントと関連付けられたパスワード ポリシーと複雑さの要件について説明します。

## <a name="administrator-password-policy-differences"></a>管理者パスワード ポリシーの相違点

Microsoft では、Azure 管理者ロール (例: 全体管理者、ヘルプデスク管理者、パスワード管理者など) に対して、既定の強力な **2 ゲート** パスワード リセット ポリシーを適用します。

これにより、管理者がセキュリティの質問を使用できなくなり、次のことが適用されます。

認証データの 2 つの部分 (電子メール アドレス**および**電話番号) を必要とする 2 つのゲート ポリシーは、次の状況で適用されます。

* すべての Azure 管理者ロール
  * ヘルプデスク管理者
  * サービス サポート管理者
  * 課金管理者
  * パートナー レベル 1 のサポート
  * パートナー レベル 2 のサポート
  * Exchange サービス管理者
  * Lync サービス管理者
  * ユーザー アカウント管理者
  * ディレクトリ ライター
  * グローバル管理者/会社の管理者
  * SharePoint サービス管理者
  * コンプライアンス管理者
  * アプリケーション管理者
  * セキュリティ管理者
  * 特権ロール管理者
  * Intune サービス管理者
  * アプリケーション プロキシ サービス管理者
  * CRM サービス管理者
  * Power BI サービス管理者
  
* 30 日間の試用期間が経過した、**または**
* バニティ ドメインが存在する (contoso.com)、**または**
* Azure AD Connect がオンプレミスのディレクトリからの ID を同期している

### <a name="exceptions"></a>例外
認証データの 1 つの部分 (電子メール アドレス**または**電話番号) を必要とする 1 つのゲート ポリシーは、次の状況で適用されます。

* 最初の 30 日間の試用期間、**または**
* バニティ ドメインが存在しない (*.onmicrosoft.com)、**かつ**、Azure AD Connect が ID を同期していない


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>すべてのユーザー アカウントに適用される UserPrincipalName ポリシー

Azure AD にサインインする必要があるすべてのユーザー アカウントは、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。 下の表は、クラウドと同期するオンプレミスの Active Directory ユーザー アカウントとクラウドのみのユーザー アカウントの両方に適用されるポリシーの概要です。

| プロパティ | UserPrincipalName の要件 |
| --- | --- |
| 使用できる文字 |<ul> <li>A - Z</li> <li>a - z</li><li>0 – 9</li> <li> に関するページを参照してください。 - \_ ! \# ^ \~</li></ul> |
| 使用できない文字 |<ul> <li>ユーザー名とドメインの間以外にある '@' 文字</li> <li>'@' 記号の直前のピリオド文字 '.'</li></ul> |
| 長さの制限 |<ul> <li>全体の長さは 113 文字以内にする必要があります</li><li>‘@’ 記号の前に 64 文字</li><li>‘@’ 記号の後に 48 文字</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>クラウド ユーザー アカウントにのみ適用されるパスワード ポリシー

次の表は、Azure AD で作成し、管理しているユーザー アカウントに適用できるパスワード ポリシー設定の説明です。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |<ul><li>A - Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 使用できない文字 |<ul><li>Unicode 文字</li><li>スペース</li><li> **強力なパスワードのみ**: '@' 記号の直前にピリオド文字 ('.') を含めることはできません</li></ul> |
| パスワードの制限 |<ul><li>8 文字以上 16 文字以下</li><li>**強力なパスワードのみ**: 次の 4 つのうち、3 つが必要です。<ul><li>小文字</li><li>大文字</li><li>数値 (0 - 9)</li><li>記号 (上述のパスワード制限を参照してください)</li></ul></li></ul> |
| パスワードの有効期間 |<ul><li>規定値: **90** 日 </li><li>値を構成するには、Windows PowerShell の Azure Active Directory モジュールから Set-MsolPasswordPolicy コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 |<ul><li>既定値: **14** 日 (パスワードの有効期限が切れる前)</li><li>値は Set-MsolPasswordPolicy コマンドレットを使用して構成できます。</li></ul> |
| パスワードの期限切れ |<ul><li>既定値: **false** 日 (パスワードの有効期限が有効になっていることを示します) </li><li>各ユーザー アカウントの値を構成するには、Set-MsolUser コマンドレットを使用します。 </li></ul> |
| パスワード**変更**履歴 |パスワードを**変更する**ときに、前回のパスワードをもう一度使用することは**できません**。 |
| パスワード **リセット**履歴 | 忘れたパスワードを**リセット**するときに、前回のパスワードをもう一度使用することが**できます**。 |
| アカウントのロックアウト |正しくないパスワードを入力してサインイン試行に 10 回失敗したユーザーを、1 分間ロックアウトします。 不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Azure Active Directory でパスワード有効期限ポリシーを設定する

Microsoft クラウド サービスのグローバル管理者は、Windows PowerShell 用 Microsoft Azure Active Directory モジュールを使用して、ユーザーのパスワードの有効期限が切れないように設定できます。 また、Windows PowerShell コマンドレットを使用すると、期限が切れない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。 このガイダンスは、ID およびディレクトリ サービスについては Microsoft Azure Active Directory にも依存する、Microsoft Intune や Office 365 などの他のプロバイダーに適用されます。 これが、ポリシーで変更できる唯一の部分です。

> [!NOTE]
> 有効期限が切れないように構成できるのは、ディレクトリ同期によって同期されていないユーザー アカウントのパスワードだけです。 ディレクトリ同期の詳細については、「[AD と Azure AD を接続する](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)」を参照してください。
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>PowerShell を使用したパスワード ポリシーの設定または確認

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

### <a name="how-to-check-expiration-policy-for-a-password"></a>パスワードの有効期限ポリシーを確認する方法
1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーについてパスワードの有効期限が切れないかどうかを確認するには、確認するユーザーのユーザー プリンシパル名 (UPN) (例: aprilr@contoso.onmicrosoft.com) またはユーザー ID を使用して、次のコマンドレットを実行します。`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * すべてのユーザーについて「パスワードを無期限にする」設定を表示するには、次のコマンドレットを実行します。 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>パスワードを期限付きに設定する

1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを期限付きに設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 組織内のすべてのユーザーのパスワードを期限付きに設定するには、次のコマンドレットを使用します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>パスワードを無期限に設定する

1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを無期限に設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 組織内のすべてのユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > `-PasswordNeverExpires $true` を設定した場合、パスワードは引き続き、`pwdLastSet` 属性に基づいて使用時間が計測されます。 つまり、パスワードを無期限に設定した場合でも、`pwdLastSet` に基づいて 90 日以上が経過し `-PasswordNeverExpires $false` を変更したときは、`pwdLastSet` が 90 日よりも古いすべてのパスワードを次回のログオン時に変更する必要があります。 これは多数のユーザーに影響を与える可能性があります。 

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問。](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)
