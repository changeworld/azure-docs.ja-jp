---
title: Azure AD SSPR と MFA の集中型登録 (パブリック プレビュー)
description: Azure AD Multi-Factor Authenticaiton とセルフサービスのパスワード リセット登録 (パブリック プレビュー)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: d6915ce659d96021d4185be3818919fcfb9d4371
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492894"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>セルフサービスのパスワード リセットと Azure Multi-Factor Authentication の集中型登録 (パブリック プレビュー)

これまでは、Azure Multi-Factor Authentication (MFA) とセルフサービスのパスワード リセット (SSPR) の認証方法を 2 つの異なるポータルに登録する必要がありました。 Azure MFA と SSPR の両方で同様の方法が使用され、両方のポータルに登録されないため、多くのユーザーは混乱しました。 この不一致により、一部のユーザーは必要なときに Azure MFA または SSPR を使用できず、ヘルプ デスクに問い合わせることになり、ユーザーを困惑させることがありました。 現在は、一度の登録で Azure MFA と SSPR の両方を利用できるようになりました。これらの機能の認証方法を 2 回登録する必要はありません。  

組織でこの新しいエクスペリエンスを有効にする前に、この記事と[エンド ユーザーのドキュメント](https://aka.ms/securityinfoguide)を読み、このエクスペリエンスがユーザーに与える影響を理解することをお勧めします。 [エンド ユーザーのドキュメント](https://aka.ms/securityinfoguide)を使用して、新しいエクスペリエンスに備えてユーザーを訓練し、準備することで、ロールアウトを成功させることができます。

|     |
| --- |
| セルフサービスのパスワード リセットと Azure Multi-Factor Authentication の集中型登録は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Azure Multi-Factor Authentication とセルフサービスのパスワード リセットの認証方法を 1 回のエクスペリエンスで登録できるようにするには、次の手順を実行します。

1. Azure portal に全体管理者またはユーザー管理者としてサインインします。
2. **Azure Active Directory**、**[ユーザー設定]**、**[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** では、**[選択済み]** のユーザーのグループまたは **[すべて]** のユーザーに対して有効にすることができます。

ユーザーは [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) にアクセスして、MFA と SSPR の両方の認証方法を登録できるようになります。 この新しいエクスペリエンスでユーザーに表示される内容の詳細については、[エンド ユーザーのドキュメント](https://aka.ms/securityinfoguide)を参照してください。  

> [!NOTE]
> このエクスペリエンスを有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、MFA と SSPR ポリシーでこれらの方法が有効な場合は、MFA と SSPR に使用できます。 このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (aka.ms/ssprsetup) にアクセスするユーザーは、ページにアクセスする前に MFA を実行する必要があります。  

## <a name="how-it-works"></a>動作のしくみ

ユーザーが別の MFA および SSPR の登録エクスペリエンスを使用して以前に認証方法を登録している場合、その情報を再度登録する必要はありません。 ただし、MFA または SSPR の登録をユーザーに必須にする設定の場合は、サインイン時にセキュリティ情報を確認するようにメッセージが表示されることがあります。

ユーザーが MFA に使用できる方法を登録している場合は、新しいエクスペリエンスにアクセスする前に MFA を実行するかどうかを確認するメッセージが表示されます。

MFA または SSPR の登録を適用し、ユーザーがまだ登録していない場合は、サインイン時に登録を求めるメッセージが表示されます。

サインイン時に登録を求められたユーザーには、次のエクスペリエンスが表示されます。

![集中型登録。 新しいユーザーとしてメソッドを設定する](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> このエクスペリエンスは、ユーザーがサインイン時に登録を求められるときにのみ表示されます。 新しいエクスペリエンス (aka.ms/setupsecurityinfo) に直接アクセスするユーザーには、この記事で後述する別バージョンのエクスペリエンスが表示されます。

表示される認証方法は、MFA または SSPR ポリシーで有効な方法に基づいて変わります。 ユーザーには、MFA ポリシー、SSPR ポリシー、またはその両方に準拠するために必要な認証方法の最小数を登録するように求められます。 ユーザーが登録できる認証方法に柔軟性がある場合は、**[セキュリティ情報の選択]** を選択して他の認証方法を選択できます。  

以前の MFA の登録エクスペリエンスとは異なり、新しい登録エクスペリエンスを使用する場合、ユーザーはアプリのパスワードを登録するように求められません。 その代わりに、アプリのパスワードのチュートリアルに記載されている手順に従って、新しいエクスペリエンスでアプリのパスワードを登録する必要があります。  

ユーザーが登録を完了すると、既定の MFA 方法が自動的に設定されます。 ユーザーが認証アプリを登録している場合、既定の方法はアプリに設定されます。 ユーザーが認証アプリを登録しておらず、自分の電話番号のみを登録している場合、既定の方法は電話呼び出しに設定されます。 ユーザーは、[https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) にアクセスし、**[既定を変更する]** を選択して既定の設定を変更できます。  

登録が強制されていない場合、ユーザーは [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) で独自の認証方法を管理できます。 ユーザーが MFA の実行に使用できる方法を以前に登録していた場合、ページにアクセスする前に MFA を実行するように求められます。  

![集中型登録。 登録ユーザーとして方法を編集する](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

このページには、以前に登録した認証方法と、会社電話などに登録されている認証方法が表示されます。 ユーザーは、認証方法 (会社電話を除く) を追加、編集、または削除することもできます。  

この新しいエクスペリエンスの監査ログは、監査ログの [認証方法] カテゴリにあります。  

## <a name="known-issues"></a>既知の問題

**ユーザーがテキスト メッセージを使用して電話を登録すると、既定の MFA 方法が電話呼び出しに設定される**

   * テキスト メッセージを使用して電話番号を登録した後、既定の MFA 方法が電話呼び出しに設定されていることに気づくことがあります。 ユーザーは、「[セキュリティ情報の管理 (プレビュー)](../user-help/security-info-manage-settings.md#change-your-info)」に記載されている手順に従って既定の方法を変更することで、この問題を解決できます。

**管理者が既定の方法を無効にした後、ユーザーが新しい登録エクスペリエンスにアクセスできなくなる**

   * 以前に登録された既定の MFA 方法を管理者が無効にした場合、一部のユーザーが新しい登録エクスペリエンスにアクセスできなくなることがあります。 シナリオの例を次に示します。
      1. ユーザーは以前に自分の電話番号を登録し、既定の方法を電話呼び出しに設定していました。
      2. 管理者はテナントの MFA 方法として電話呼び出しを無効にします。
      3. テナントの SSPR ポリシーを満たす追加の方法を登録する必要があるため、ユーザーがサインインするときに登録を求めるメッセージが表示されます。
      4. ユーザーは登録を試みますが、既定の方法が設定されていないためページにアクセスできず、ループ状態になります。

## <a name="next-steps"></a>次の手順

[Azure AD のセルフサービスによるパスワードのリセットをデプロイする方法](howto-sspr-deployment.md)

[サインイン時に多要素認証を要求する方法](howto-mfa-getstarted.md)

[エンドユーザーの認証方法の構成に関するドキュメント](https://aka.ms/securityinfoguide)