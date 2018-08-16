---
title: Microsoft Authenticator の電話によるサインイン - Azure Active Directory | Microsoft Docs
description: パスワードを入力する代わりに携帯電話を使用して職場や学校、および個人の Microsoft アカウントにサインインする方法の詳細情報と手順について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: a6df90a1ae1dddbf7f76a7ef6c7e13d9e645dd9c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528427"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>パスワードではなく携帯電話でサインインする

Microsoft Authenticator アプリを使用して、パスワードを入力した後で 2 段階認証を実行してアカウントの安全性を維持できるだけでなく、Microsoft アカウントのパスワードをまったく使用せずサインインすることもできるようになりました。

この機能は、Android 6.0 以上を実行している iOS および Android デバイスで使用できます。

## <a name="how-it-works"></a>動作のしくみ

多くのユーザーは、Microsoft アカウントにサインインするときの 2 段階認証用に Microsoft Authenticator アプリを使います。 パスワードを入力した後、アプリに移動して通知を承認するか、認証コードを取得します。 電話によるサインインを使用すると、パスワードの入力を省略し、本人確認のすべての操作を携帯電話を使って行うことができます。 電話によるサインインは 2 段階認証であるため、本人確認には引き続き "知っていること" と "持っているもの" を指定する必要があります。 引き続き、携帯電話が "持っているもの" で、携帯電話の暗証番号または生体認証キーが "知っていること" になります。

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>職場または学校アカウントで電話によるサインインを有効にする

お客様の組織で ID の管理および認証に Azure Active Directory を使用している場合は、Microsoft Authenticator アプリで有効にすると、電話によるサインインを使用できます。 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>電話によるサインインをアカウントで有効にするには

- **このアイコン![電話によるサインインのアイコン](./media/microsoft-authenticator-app-phone-signin-faq/icon.png)がスマート フォンにある場合**は、 電話によるサインインが既に設定されています。 アプリ外で認証要求に関する通知を受け取るには、プッシュ通知を追加するためにアカウントのアップグレードが要求される場合があります。

- **2 段階認証用に Microsoft Authenticator アプリを既に使用している場合。** アプリの **[アカウント]** 画面に移動して、職場または学校用アカウントのドロップダウン矢印を選択し、**[電話によるサインインを有効にする]** を選択します。

- **個人アカウントで Microsoft Authenticator アプリを使用しているが、職場または学校アカウントには使用していない場合。** 組織の 2 段階認証手順を実行してから、電話によるサインインをオンにします。 詳細については、「[Microsoft Authenticator アプリの概要](microsoft-authenticator-app-how-to.md)」を参照してください。

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>職場または学校アカウントで電話によるサインインを使用するには

1.  職場または学校アカウントに通常どおりサインインします。

    ユーザー名を入力すると、2 桁の数字が表示されたページに移り、Microsoft Authenticator アプリを使ってサインインを承認するように求められます。 このサインイン方法を使用しない場合は、**[Use your password instead]\(代わりにパスワードを使用する\)** を選択し、パスワードを使用してサインインします。

    ![サインイン ページ](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. Microsoft 認証アプリで **[サインインを承認する]** ことを求める通知を受け取ります。 サインインを承認する場合は **[承認]** を選択します。 承認しない場合は、**[拒否]** を選択します。

3. **[サインインの承認]** 画面に表示される同じ番号をタップします。 携帯電話の暗証番号または生体認証キーを使用して、認証を完了します。
 
    ![[サインインの承認] ページ](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>電話によるサインインを個人アカウントで有効にする

Outlook.com、Xbox、または Skype などにサインインするために使用するアカウントなど、個人の Microsoft アカウントで電話によるサインインを有効にできます。

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>電話によるサインインを個人アカウントで有効にするには

- **Microsoft Authenticator アプリを使用していない場合** Microsoft Authenticator アプリをダウンロードしインストールしてから、Microsoft Authenticator アプリの **[アカウントを追加する]** 画面からアカウントにサインインして Microsoft の個人アカウントを追加します。 詳細については、「[Microsoft Authenticator アプリの概要](microsoft-authenticator-app-how-to.md)」を参照してください。
新しいアカウントは、電話によるサインインの使用をすぐに開始できるように、自動的に有効になります。

- **2 段階認証用に Microsoft Authenticator アプリを既に使用している場合。** アプリの **[アカウント]** 画面に移動して、職場または学校アカウントのドロップダウン矢印を選択し、**[電話によるサインインを有効にする]** を選択します。

>[!NOTE]
>アプリがアカウントを保護するには、または暗証番号生体認証ロックがデバイスに必要です。 携帯電話がロック解除状態のままにされている場合、アプリは、電話によるサインインを有効にする前にロックを設定するよう求めるメッセージを表示します。

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>個人アカウントで電話によるサインインを使用するには

1.  個人用の Microsoft アカウントでサインイン ページに移動し、パスワードを入力する代わりに **[代わりに Microsoft Authenticator アプリを使用する]** リンクを選択します。

    Microsoft から携帯電話に通知が送信されます。 

2.  通知を承認します。

## <a name="phone-sign-in-faq"></a>電話によるサインインの FAQ

|**質問**|**ソリューション**|
|--------------|-------------|
|**電話によるサインインは、パスワードの入力よりどの程度安全ですか。**|現在、ほとんどの人は、ユーザー名とパスワードを使って Web サイトやアプリにサインインします。 残念なことに、パスワードは、なくしたり、盗まれたり、ハッカーに推測されたりすることがよくあります。<br><br>Microsoft Authenticator アプリを設定すると、携帯電話の暗証番号または生体認証ロックによって保護されているアカウントのロックを解除するためのキーが電話上に作成されます。 このキーは、サインイン時に本人確認のために使用されます。<br><br>**重要**<br>お客様のデータは、キーを保護する目的にのみ、ローカルで使用されます。 クラウドに送信されたり格納されることはありません。|
|**この機能は 2 段階認証に代わるものですか。2 段階認証を無効にする必要がありますか。**| Microsoft では、電話によるサインインを使用できる範囲を広げる作業を行っていますが、現時点では、Microsoft のエコシステムの中にはこの機能をサポートしない部分がまだ存在します。 そのような部分では、サインインのセキュリティ保護に 2 段階認証がまだ使用されています。 そのため、アカウントの 2 段階認証は無効にしないでください。|
|**アカウントの 2 段階認証を有効にしたままにする場合、2 つの通知を承認する必要がありますか。**| いいえ。 電話を使用して Microsoft アカウントにサインインすると 2 段階認証とみなされ、2 回目の承認は必要ありません。|
|**電話を紛失した場合、または電話が手元にない場合は、どのようにしてアカウントにアクセスできますか。アカウントへのアクセス方法を教えてください。**| サインイン ページで [Use a password instead]\(代わりにパスワードを使用する)\ リンクを選択すれば、いつでもパスワードの使用に戻すことができます。 ただし、2 段階認証を使用している場合は、第 2 の方法で身元を証明する必要があることに注意してください。<br><br>**重要**<br>アカウントには、最新式の複数の認証方法を関連づけることをお勧めします。<br><br>個人アカウントの認証方法は [[セキュリティ設定]](https://account.live.com/proofs/manage) ページから管理することができます。 職場または学校アカウントの場合、管理者がセキュリティ情報をオンに設定している場合は、組織の [[追加のセキュリティ確認]](https://aka.ms/MFASetup) ページまたは **[Keep your account secure]\(アカウントのセキュリティ保護を維持\)** ページに移動します。 セキュリティ情報の詳細については、[セキュリティ情報の管理](security-info-manage-settings.md)に関する記事を参照してください。<br><br>認証方法を管理できない場合は、管理者に連絡する必要があります。|
|**この機能の使用を停止してパスワードの入力に戻すにはどうすればよいですか。**|個人アカウントの場合は、サインイン時に **[Use a password instead]\(代わりにパスワードを使用する)\** リンクを選択します。 前回の選択内容が記憶され、次にサインインするときは既定で表示されます。 電話によるサインインに戻る場合は、サインイン時に **[Use an app instead]\(代わりにアプリを使用する)\** リンクを選択します。<br><br>職場または学校アカウントの場合、Microsoft Authenticator アプリを開くことでアカウントを削除する必要があります。**[アカウントの編集]** メニューを選択してからアカウントを削除します。|
|**電話によるサインインに複数の職場または学校アカウントを使用できないのはなぜですか。**| 携帯電話は、1 つの職場または学校アカウントに登録する必要があります。 電話によるサインインを別の職場または学校アカウントで有効にするには、**[設定]** ページから古いアカウントの登録を解除する必要があります。|
|**携帯電話を使用して自分の PC にサインインできますか。**| PC には、Windows 10 で Windows Hello を使用してサインインすることをお勧めします。 Windows Hello を使用すると、顔、指紋、または暗証番号を使用してサインインできます。|
|**Windows Phone で電話によるサインインを使用できますか。**| いいえ。 この機能は、Windows Phone の Microsoft Authenticator アプリでサポートされていません。|

## <a name="next-steps"></a>次の手順

-   アプリに関する一般的な質問については、「[Microsoft Authenticator アプリに関する FAQ](microsoft-authenticator-app-faq.md)」をご覧ください

-   2 段階認証の詳細については、[アカウントの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)に関する記事を参照してください。

-   セキュリティ情報の詳細については、[セキュリティ情報の管理](security-info-manage-settings.md)に関する記事を参照してください。
