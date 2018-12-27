---
title: メールを使用するようにセキュリティ情報を設定する - Azure Active Directory | Microsoft Docs
description: 職場または学校のメール アドレスを利用して本人確認をするようにセキュリティ情報を設定します。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: f6a574f169310f2779fb20353fda748e6893a4bd
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016742"
---
# <a name="set-up-security-info-to-use-email-preview"></a>メールを使用するようにセキュリティ情報を設定する (プレビュー)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

セキュリティ情報を設定するには、職場または学校のアカウントにサインインし、登録プロセスを完了する必要があります。 セキュリティ情報をまだ設定していない場合は、今すぐ設定するように求められます。

## <a name="set-up-email"></a>メールの設定

組織の設定によっては、サインインするとき、セキュリティ情報にメール アドレスを追加するように求められることがあります。 そうでなければ、「[セキュリティ情報の管理](security-info-manage-settings.md)」の手順でセキュリティ情報に電子メールを設定します。

>[!Note]
>アクセスにネットワーク パスワードを要求しないメール アカウントの使用をお勧めします。<br>電子メール オプションが表示されない場合、検証に電子メールを使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に支援を要請する必要があります。

### <a name="to-use-your-email-address"></a>メール アドレスを使用するには

1. **[電子メール]** オプションを選択し、ボックスにメール アドレスを入力します。 このメール アドレスには、職場または学校の電子メールを選択できません。

     ![電子メールの入力ボックスが表示されている [セキュリティ情報] ページ](media/security-info/security-info-keep-secure-setup-email.png)

2. Microsoft が組織に送信した電子メールを確認し、それに含まれる確認コードを **[電子メールの確認]** ボックスに入力し、**[完了]** を選択します。

     ![[セキュリティ情報] ページ。電子メールに送信された確認コードを入力するボックスが表示されています](media/security-info/security-info-verify-email.png)

    >[!Note]
    >組織の代理として Microsoft からの電子メールを表示できない場合、正しいメール アドレスを入力したことを確認し、迷惑メール フォルダーを確認してください。

3. **[アカウントのセキュリティ保護]** ページで、**[完了]** を選択します。

    パスワードのリセットを使用するときにご自分のメール アドレスを使って本人確認をするようにご利用のセキュリティ情報が更新されます。

## <a name="additional-security-info-options"></a>追加のセキュリティ情報オプション

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[Set up security info to use an authenticator app](security-info-setup-auth-app.md)」(認証アプリを使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use phone calls](security-info-setup-phone-number.md)」 (電話を使用するようにセキュリティ情報を設定する) を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。
    
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次の手順

- セキュリティ情報を更新する必要がある場合は、「[セキュリティ情報の管理](security-info-manage-settings.md)」にある説明に従ってください。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。
