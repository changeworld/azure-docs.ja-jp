---
title: 認証アプリを使用するようにセキュリティ情報を設定する - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator アプリを使って本人確認をするように、セキュリティ情報を設定します。
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
ms.openlocfilehash: bd62ff03c3158743337ce958eb1bda7c7bd2a7f6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347842"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>認証アプリを使用するようにセキュリティ情報を設定する (プレビュー)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

セキュリティ情報を設定するには、職場または学校用のアカウントにサインインし、登録プロセスを完了する必要があります。 セキュリティ情報をまだ設定していない場合は、今すぐ設定するように求められます。

## <a name="set-up-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの設定

組織の設定によっては、サインインするとき、Microsoft Authenticator アプリを設定するように求められることがあります。 それ以外の場合は、「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の手順に従って、セキュリティ情報に Microsoft Authenticator アプリを設定します。

Microsoft Authenticator アプリのダウンロード方法と詳細については、「[Microsoft Authenticator アプリの概要](microsoft-authenticator-app-how-to.md)」をご覧ください。

>[!Note]
>Microsoft Authenticator アプリを使用しない場合は、設定時に別のアプリを設定することもできます。 この記事では、Microsoft Authenticator アプリを使用します。 認証アプリ オプションが表示されない場合、検証に認証アプリを使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に支援を要請する必要があります。

### <a name="to-use-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用するには

1. **[認証アプリ]** オプションを選択します。

    **アプリの取得**ウィザードが表示されます。

    ![アプリの取得ウィザード (初期画面)](media/security-info/security-info-auth-app-wizard.png)

    Microsoft Authenticator アプリを使用しない場合は、**[アプリの取得]** 画面から **[別の認証アプリを使用します]** リンクをクリックできます。

2. Microsoft Authenticator アプリをインストールしたら、**[次へ]** を選択します。

    メッセージが表示されたら、通知を許可し、新しいアカウントを追加して、**[職場または学校アカウント]** を選択します。

3. **[次へ]** を選択します。

    **[QR コードをスキャンします]** 画面が表示されます。

    ![認証アプリを使用して QR コードをスキャンする](media/security-info/security-info-scan-qr.png)

4. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、**[職場または学校アカウント]** を選択します。 

5. QR コード リーダー アプリがある場合は、表示されたコードをスキャンします。 コード リーダー アプリがない場合は、**[QR コードをスキャンできませんか?]** リンクを選択し、コードと URL を Microsoft Authenticator アプリに手動で入力します。

6. Microsoft Authenticator アプリを使って通知を承認し、アプリをアクティブ化します。

    2 段階認証またはセルフサービスのパスワード リセットを使用するときに Microsoft Authenticator アプリを使用して本人確認をするようにセキュリティ情報が更新されます。

## <a name="additional-security-info-options"></a>追加のセキュリティ情報オプション

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions (セキュリティの質問を使用するようにセキュリティ情報を設定する)](security-info-setup-questions.md)」を参照してください。
    
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次の手順

- 「[Manage your security info (セキュリティ情報の管理)](security-info-manage-settings.md)」の説明に従って、セキュリティ情報を更新する (更新が必要な場合)。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。