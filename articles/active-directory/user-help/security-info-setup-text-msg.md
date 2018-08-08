---
title: テキスト メッセージを使用するようにセキュリティ情報を設定する - Azure Active Directory | Microsoft Docs
description: テキスト (SMS) メッセージを使用して本人確認をするようにセキュリティ情報を設定します。
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
ms.openlocfilehash: f20bcb4245a2870d8eaa442c6f747ee533b362db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347850"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>テキスト メッセージを使用するようにセキュリティ情報を設定する (プレビュー)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

セキュリティ情報を設定するには、職場または学校用のアカウントにサインインし、登録プロセスを完了する必要があります。 セキュリティ情報をまだ設定していない場合は、今すぐ設定するように求められます。

## <a name="set-up-text-messaging"></a>テキスト メッセージの設定

組織の設定によっては、サインインするとき、セキュリティ情報にテキスト メッセージを追加するように求められることがあります。 それ以外の場合は、「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の手順に従って、セキュリティ情報にテキスト メッセージを設定します。

テキスト メッセージ オプションは電話オプションの一部であるため、電話番号を使用する場合と同じ方法ですべてを設定しますが、Microsoft からの電話呼び出しではなく、テキスト メッセージを使用するように選択します。 電話番号オプションが表示されない場合、検証に電話呼び出しを使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に支援を要請する必要があります。

### <a name="to-use-a-text-message"></a>テキスト メッセージを使用するには

1. **[電話]** オプションを選択します。

    **電話のセットアップ** ウィザードが表示されます。

    ![国または地域コードと電話番号を設定する](media/security-info/security-info-keep-secure-setup-text.png)

2. ドロップダウン ボックスから**国または地域**を選択し、**[電話番号]** ボックスに電話番号 (該当する場合は市外局番を含む) を入力します。**[コードを SMS 送信する]** オプションを選択し、次に **[次へ]** を選択します。

    コードを含んだテキスト メッセージを受信したら、確認ページにコードを入力する必要があります。

    ![テキスト メッセージのコードを入力する確認ページ](media/security-info/security-info-keep-secure-verify-text-msg.png)

    2 段階認証またはセルフサービスのパスワード リセットを使用するときにテキスト メッセージを送信して本人確認をするようにセキュリティ情報が更新されます。

    >[!Note]
    >テキスト メッセージではなく電話呼び出しを希望する場合は、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」の手順に従います。

## <a name="additional-security-info-options"></a>追加のセキュリティ情報オプション

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。
   
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions (セキュリティの質問を使用するようにセキュリティ情報を設定する)](security-info-setup-questions.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- 「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の説明に従って、セキュリティ情報を更新する (更新が必要な場合)。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。