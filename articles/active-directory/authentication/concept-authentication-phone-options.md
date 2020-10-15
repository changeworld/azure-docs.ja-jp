---
title: 電話の認証方法 - Azure Active Directory
description: Azure Active Directory で電話の認証方法を使用して、サインイン イベントの改善と安全性の確保に役立てる方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6b39852668e1b545ce576ac0d04a208e2058e8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965422"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Azure Active Directory の認証方法 - 電話オプション

テキスト メッセージを使用した直接認証では、[ユーザーが SMS ベースの認証を構成して有効にする (プレビュー) ](howto-authentication-sms-signin.md)ことができます。 SMS ベースのサインインは、最前線の従業員に最適です。 SMS ベースのサインインでは、ユーザーはアプリケーションやサービスにアクセスするためのユーザー名とパスワードを知る必要がありません。 代わりに、登録済みの携帯電話番号を入力し、確認コードを含むテキスト メッセージを受信して、サインイン インターフェイスに入力します。

また、ユーザーは、Azure Multi-Factor Authentication またはセルフサービス パスワード リセット (SSPR) で使用される認証のセカンダリ形式として、携帯電話または会社電話を使用して自身を確認することもできます。

正常に動作させるには、電話番号の形式が " *+<国コード> <電話番号>* " (例: *+1 4251234567*) になっている必要があります。

> [!NOTE]
> 国/地域番号と電話番号の間にスペースを入れる必要があります。
>
> パスワードのリセットは内線番号をサポートしていません。 *+1 4251234567X12345* の形式であっても、電話がかけられる前に内線番号は削除されます。

## <a name="mobile-phone-verification"></a>携帯電話の確認

Azure Multi-Factor Authentication または SSPR の場合、ユーザーは、サインイン インターフェイスに入力する確認コードを含むテキスト メッセージを受信するか、定義された PIN コードを入力するように求めるメッセージを表示する電話番号を受信するかを選択できます。

ユーザーが、携帯電話番号をディレクトリに表示したくなく、それでもパスワードのリセットにその番号を使いたい場合は、管理者がその電話番号をディレクトリに設定しないようにする必要があります。 一方、ユーザーは、[https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) の統合されたセキュリティ除法登録で **[認証用電話]** 属性を設定する必要があります。 管理者はこの情報をユーザーのプロファイルで確認できますが、他の場所には公開されません。

![電話番号が設定された認証方法を示す Azure portal のスクリーンショット](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft では、SMS または音声ベースの Multi-Factor Authentication プロンプトを常に同一番号で配信するとは限りません。 ユーザーのために、Microsoft は、ルートを調整して SMS の配信率を向上させる際に任意のタイミングでショート コードを追加または削除する場合があります。 Microsoft は、米国とカナダ以外の国/地域ではショート コードをサポートしていません。

### <a name="text-message-verification"></a>テキスト メッセージの確認

SSPR または Azure Multi-Factor Authentication でテキスト メッセージの確認を行うと、認証コードを含む SMS が携帯電話番号に送信されます。 サインイン プロセスを完了するには、指定された確認コードをサインイン インターフェイスに入力します。

### <a name="phone-call-verification"></a>音声通話の確認

SSPR または Azure Multi-Factor Authentication で音声通話の確認を使用すると、ユーザーが登録した電話番号に対して自動音声通話が行われます。 サインイン プロセスを完了するには、ユーザーはテンキーで PIN 番号とそれに続いて # を入力するように求められます。

## <a name="office-phone-verification"></a>会社電話の確認

会社電話の属性は Azure AD 管理者によって管理されているので、ユーザー自身が登録することはできません。

SSPR または Azure Multi-Factor Authentication で音声通話の確認を使用すると、ユーザーが登録した電話番号に対して自動音声通話が行われます。 サインイン プロセスを完了するには、ユーザーはテンキーで PIN 番号とそれに続いて # を入力するように求められます。

## <a name="troubleshooting-phone-options"></a>電話オプションのトラブルシューティング

Azure AD の電話認証で問題が発生した場合は、次のトラブルシューティングの手順を確認してください。

* 1 つのデバイスで発信者 ID がブロックされる。
   * デバイスで構成されているすべてのブロック済み番号を確認します。
* 電話番号が間違っているか、国/地域コードが正しくない。または、個人の電話番号と勤務先の電話番号を混同している。
   * ユーザー オブジェクトおよび構成されている認証方法をトラブルシューティングします。 正しい電話番号が登録されていることを確認します。
* 間違った PIN の入力。
   * ユーザーが自分のアカウントに登録されている正しい PIN を使用していることを確認します。
* ボイスメールへの通話の転送。
   * ユーザーの電話の電源が入っていて、ユーザーがいる場所でサービスを利用できることを確認するか、または別の方法を使います。
* ユーザーがブロックされている
   * Azure AD 管理者に Azure portal でユーザーのブロックを解除させます。
* デバイスで SMS がサブスクライブされていない。
   * ユーザーに方法を変更させるか、またはデバイスで SMS をアクティブ化させます。
* 通信プロバイダーの障害 (電話入力が検出されない、DTMF トーンが発行されない、複数のデバイスで発信者 ID がブロックされる、複数のデバイスで SMS がブロックされるなど)。
   * Microsoft では、認証用の電話呼び出しおよび SMS メッセージのルーティングに、複数の通信プロバイダーが使われています。 上記の問題のいずれかが発生する場合は、ユーザーにその方法を 5 分以内に 5 回以上使わせて、Microsoft サポートに問い合わせるときにそのユーザーの情報を提供できるようにします。

## <a name="next-steps"></a>次のステップ

最初に、[セルフサービス パスワード リセット (SSPR) のチュートリアル][tutorial-sspr]と [Azure Multi-Factor Authentication][tutorial-azure-mfa] に関するページを参照してください。

SSPR の概念の詳細については、[Azure AD のセルフサービス パスワード リセットのしくみ][concept-sspr]に関するページを参照してください。

MFA の概念の詳細については、[Azure Multi-Factor Authentication のしくみ][concept-mfa]に関するページを参照してください。

[Microsoft Graph REST API ベータ版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)を使用した認証方法の構成の詳細について確認してください。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
