---
title: 電話呼び出しを使用するようにセキュリティ情報を設定する - Azure Active Directory | Microsoft Docs
description: モバイル デバイスまたは職場の電話番号を使用して本人確認をするようにセキュリティ情報を設定します。
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
ms.openlocfilehash: 78b085bfad4dd3bab9d8b83f540b6ea315f911b9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162106"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>電話呼び出しを使用するようにセキュリティ情報を設定する (プレビュー)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

セキュリティ情報を設定するには、職場または学校アカウントにサインインし、登録プロセスを完了する必要があります。 セキュリティ情報をまだ設定していない場合は、今すぐ設定するように求められます。

## <a name="set-up-phone-calls"></a>音声通話の設定

組織の設定によっては、サインインするとき、セキュリティ情報に電話番号を追加するように求められることがあります。 それ以外の場合は、「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の手順に従って、セキュリティ情報に電話の呼び出しを設定します。

>[!Note]
>セキュリティ情報では、電話の内線番号の使用はサポートされません。 電話番号が "+1 4255551234X12345" の適切な形式であっても、電話がかけられる前に内線番号は削除されます。<br>電話オプションが表示されない場合、検証に電話呼び出しを使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に相談する必要があります。

### <a name="to-use-your-phone-number"></a>電話番号を使用するには

1. **[電話]** オプションを選択します。

    **電話のセットアップ** ウィザードが表示されます。

    ![国または地域コードと電話番号を設定する](media/security-info/security-info-keep-secure-setup-phone.png)

2. ドロップダウン ボックスから**国または地域**を選択し、**[電話番号]** ボックスに電話番号 (該当する場合は市外局番を含む) を入力します。**[電話する]** オプションを選択し、次に **[次へ]** を選択します。

    正しい電話番号を入力したことを確認するための電話がかかってきます。 その時点で、シャープ記号 (#) キーを押して確認し、設定を完了するよう求められます。

    ![呼び出しが正常に応答されたことを示す電話番号の確認画面](media/security-info/security-info-keep-secure-verify-phone-call.png)

    2 段階認証またはセルフサービスのパスワード リセットを使用するときに電話番号を使用して本人確認をするようにセキュリティ情報が更新されます。

    >[!Note]
    >モバイル デバイスへの電話呼び出しではなくテキスト メッセージの受信を希望する場合は、[テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する](security-info-setup-text-msg.md)方法に関するページの手順に従います。

## <a name="additional-security-info-options"></a>追加のセキュリティ情報オプション

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[Set up security info to use an authenticator app](security-info-setup-auth-app.md)」(認証アプリを使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的説明が必要な場合、[テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する](security-info-setup-text-msg.md)方法に関するページを参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。
    
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次の手順

- セキュリティ情報を更新する必要がある場合は、「[セキュリティ情報の管理](security-info-manage-settings.md)」にある説明に従ってください。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。