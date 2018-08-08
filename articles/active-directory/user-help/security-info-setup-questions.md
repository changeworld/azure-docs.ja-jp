---
title: セキュリティの質問を使用するようにセキュリティ情報を設定する - Azure Active Directory | Microsoft Docs
description: あらかじめ定義されたセキュリティの質問を使用して本人確認をするようにセキュリティ情報を設定します。
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
ms.openlocfilehash: f1905e66bbe823f6c7c9c35d6540f3c8ca1d6ccd
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389635"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>あらかじめ定義されたセキュリティの質問を使用するようにセキュリティ情報を設定する (プレビュー)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

セキュリティ情報を設定するには、職場または学校アカウントにサインインし、登録プロセスを完了する必要があります。 セキュリティ情報を設定したことがない場合、今すぐ設定するように求められます。

## <a name="set-up-security-questions"></a>セキュリティの質問の設定

組織の設定によっては、サインインするとき、セキュリティ情報にセキュリティの質問を追加するように求められることがあります。 それ以外の場合は、「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の手順に従って、セキュリティ情報にセキュリティの質問を設定します。

セキュリティの質問を使用する場合、別の方法と併用することをお勧めします。 セキュリティの質問は、一部の人が別のユーザーの質問に対する回答を知っている可能性があるため、他の方法に比べて安全性が低い可能性があります。

>[!Note]
>セキュリティの質問は、ディレクトリ内のユーザー オブジェクトに非公開かつ安全に保存され、登録時にユーザーだけが回答できます。 管理者がユーザーの質問または回答を読み取ったり変更したりする方法はありません。<br>セキュリティの質問オプションが表示されない場合、検証にセキュリティの質問を使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に相談する必要があります。

### <a name="to-choose-and-answer-your-security-questions"></a>セキュリティの質問を選択して回答するには

1. **[セキュリティの質問]** を選択し、回答するセキュリティの質問を選択します。 

    選択する必要のあるセキュリティの質問の数は、管理者によって決定されます。

    ![セキュリティ情報ページでセキュリティの質問を選択する](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. 選択した質問の回答を入力し、**[完了]** を選択します。

## <a name="additional-security-info-options"></a>追加のセキュリティ情報オプション

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的説明が必要な場合、「[Set up security info to use an authenticator app (認証アプリを使用するようにセキュリティ情報を設定する)](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的説明が必要な場合、[テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する](security-info-setup-text-msg.md)方法に関するページを参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。
   
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に相談する必要があります。

## <a name="next-steps"></a>次の手順

- セキュリティ情報を更新する必要がある場合、「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」記事にある説明に従ってください。

- パスワードをなくしたか忘れた場合、[パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)でパスワードをリセットするか、「[Reset your work or school password (職場または学校のパスワードをリセットする)](user-help-reset-password.md)」記事の手順に従います。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」記事にサインイン問題のトラブルシューティングのヒントやヘルプがあります。