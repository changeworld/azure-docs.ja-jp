---
title: セキュリティ情報の管理 - Azure Active Directory | Microsoft Docs
description: 2 段階認証の使用方法など、セキュリティ情報の管理方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: abd2984574f80f03f276861782ff9ee51348d07e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391379"
---
# <a name="manage-your-security-info-preview"></a>セキュリティ情報の管理 (プレビュー)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

自分のセキュリティ情報を使用し、職場または学校の自分のアカウントにサインインしたり、自分のパスワードをリセットしたりすることができます。

サインインする際に、組織の設定によっては、**[今後 X 日間はこのメッセージを表示しない]** チェック ボックスが表示されることがあります。 このチェック ボックスを選択すると、管理者が許可する日数だけ、再認証を要求されることなく、お使いのデバイスがサインインされている状態を維持できます。

## <a name="change-your-info"></a>情報を変更する
管理者と組織が許可する項目に基づき、セキュリティ情報を更新または追加したり、既定値を変更したりできます。

### <a name="to-change-your-info"></a>情報を変更するには

1. 職場または学校のアカウントにサインインします。

2. myapps.microsoft.com に進み、ページの右上隅から自分の名前を選択し、**[プロファイル]** を選択します。

3. **[アカウントの管理]** 領域で **[セキュリティ情報の編集]** を選択します。

    ![[セキュリティ情報の編集] リンクが強調表示されているプロファイル画面](media/security-info/security-info-profile.png)

4. 既定の方法を利用してアクセスを承認し、現在のセキュリティ情報詳細を表示します (このような操作を管理者が組織に対して設定している場合)。

5. **[アカウントのセキュリティ保護]** ページでできること:

    - **[セキュリティ情報の追加]** を選択し、方法を追加します。

    - **[既定を変更する]** を選択し、既定の方法を変更します。

    - 既存の方法の隣にある**鉛筆**アイコンを選択し、情報を更新します。

    ![既存の編集可能な情報が表示されているセキュリティ情報画面](media/security-info/security-info-edit.png)

6. 変更後、ページを離れると、変更内容が保存されます。

これらのオプションが表示されない場合、あるいは myapps.microsoft.com ページにアクセスできない場合は、組織でカスタムのオプションまたはカスタムのページを使用している可能性があります。 管理者に連絡し、支援を要請する必要があります。

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>紛失したデバイスまたは侵入された可能性があるデバイスのセキュリティ情報を管理する

デバイスを紛失したか、デバイスに侵入された場合、以前に信頼されたすべてのデバイスに対して認証プロセスをやり直す必要があります。

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>紛失したデバイスまたは侵入された可能性があるデバイスのセキュリティ情報を管理するには

1. 職場または学校のアカウントにサインインします。

2. myapps.microsoft.com に進み、ページの右上隅から自分の名前を選択し、**[プロファイル]** を選択します。

3. **[アカウントの管理]** 領域で **[記憶されたデバイスで MFA を解除する]** を選択します。
    
    このオプションを選択すると、サインイン後、Multi-Factor Authentication プロセスをもう一度行う必要があります。

    ![[記憶されたデバイスで MFA を解除する] が強調表示されているプロファイル画面](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>セキュリティ情報に関する共通の問題と解決策

この記事は、2 段階検証関連の問題など、セキュリティ情報の問題の解決に役立ちます。

|問題点|解決策|
|-------|--------|
|自分の携帯電話が手元にありません|自分の電話をいつも持ち歩いているとは限りませんが、持っていないときでも職場や学校のアカウントにサインインしなければならないことがあります。 この問題を解決するには、電話を必要としない別の認証方法でサインインします。たとえば、メール アドレスやオフィスの電話番号を利用します。 セキュリティ情報に方法を追加するには、「[情報を変更する](#change-your-info)」セクションの手順を行います。|
|電話を紛失した場合や電話が盗まれた場合|残念ながら、電話は紛失したり、盗難に遭ったりすることがあります。 そのような場合、組織に知らせ、IT スタッフにアプリのパスワードをリセットしてもらい、信頼されているデバイスの一覧から、記憶されているデバイスをすべて消去してもらうことを強くお勧めします。 「[紛失したデバイスまたは侵入された可能性があるデバイスのセキュリティ情報を管理する](#manage-your-security-info-for-a-lost-or-potentially-compromised-device)」セクションの手順を行い、信頼されている自分のデバイスで MFA を解除することもできます。|
|電話番号が新しくなりました|この問題の解決方法は 2 つあります。 メールなど、電話番号を必要としない代替認証方法でサインインできます。それができない場合は、組織の IT スタッフに連絡し、設定を消去してもらいます。 セキュリティ情報に方法を追加するには、「[情報を変更する](#change-your-info)」セクションの手順を行います。|
|既定の方法が正しくありません|セキュリティ オプションで既定の方法を更新できます。 詳しくは、「[情報を変更する](#change-your-info)」セクションをご覧ください。|
|携帯電話でテキストまたは通話呼び出しを受け取ることができません|これまで同じ携帯電話でテキストまたは通話を正常に受信している場合は、アカウントではなく携帯電話会社に問題がある可能性があります。 良好な電波が届いていることと、テキスト メッセージや電話呼び出しを受信できることを確認してください。 友人に依頼して、テストとして電話をかけてもらうか、テキストを送信してもらいます。<br><br>テキストや通話で正常にメッセージを受信できる場合、別の方法をお試しください。 「[情報を変更する](#change-your-info)」セクションの手順でセキュリティ情報に方法を追加できます。 追加する別の方法がない場合は、会社のサポートに連絡し、設定を消去してもらいます。次回サインインするときに、方法を設定できます。<br><br>電波状態が良くないために問題が頻繁に起こる場合、携帯電話で Microsoft Authenticator アプリを使用することをお勧めします。 サインインするために使用するランダムなセキュリティコードをアプリで生成できます。これらのコードでは、携帯電話の信号もインターネット接続も必要ありません。 Microsoft Authenticator アプリの詳細については、「[Microsoft Authenticator アプリの概要](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)」の記事を参照してください。|
|この表の選択肢では問題が解決されませんでした|ここにあるトラブルシューティングの手順を試してもまだ問題が発生する場合、会社のサポートに連絡し、支援を要請してください。|

## <a name="next-steps"></a>次の手順

- 「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」でセキュリティ情報について学習します。

- 「[2 段階認証の概要](user-help-two-step-verification-overview.md)」の記事で 2 段階認証について学習します。 

- 以下のいずれかの操作方法記事を読み、セキュリティ情報領域でデバイスを設定する方法について学習します。

    - [認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)

    - [電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)

    - [テキスト メッセージを使用するようにセキュリティ情報を設定する](security-info-setup-text-msg.md)

    - [メールを使用するようにセキュリティ情報を設定する](security-info-setup-email.md)

    - [セキュリティの質問を使用するようにセキュリティ情報を設定する](security-info-setup-questions.md)

- パスワードをなくしたか忘れた場合は、[パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)でパスワードをリセットするか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の記事の手順に従います。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事にサインイン問題の解決のヒントやヘルプがあります。