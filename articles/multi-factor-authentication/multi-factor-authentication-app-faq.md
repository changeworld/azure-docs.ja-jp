---
title: "Microsoft Authenticator アプリに関する FAQ"
description: "Microsoft 認証アプリと Multi-factor Authentication に関してよく寄せられる質問の一覧を提供します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dbe949dac15b45ba038ed675873f3da1fb1ff86a


---
# <a name="microsoft-authenticator-application-faq"></a>Microsoft Authenticator アプリケーションに関する FAQ
Microsoft Authenticator アプリは、Azure Authenticator アプリに置き換わり、Azure 多要素認証を使用するときに推奨されるアプリです。 このアプリは、Windows Phone、Android、iOS で利用できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>Azure Authenticator、多要素認証、および Microsoft アカウント アプリはどうなりましたか。
これらのアプリは、Microsoft Authenticator アプリに置き換えられています。 Azure Authenticator は Microsoft Authenticator にアップグレードされています。 多要素認証または Microsoft アカウント アプリを使用している場合は、Microsoft Authenticator をインストールし、アカウントをもう一度追加してください。 新しいアプリへのアカウントの追加を完了した後で、古いものを削除してください。

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>検証コードで Microsoft Authenticator アプリケーションを既に使用しています。 ワンクリック プッシュ通知に切り替えるにはどうすればよいですか。
プッシュ通知によるサインインの承認は、Microsoft アカウントでのみ可能であり、Google や Facebook などのサード パーティのアカウントでは使用できません。 ただし、職場または学校の Microsoft アカウントの場合は、所属している組織がこのオプションを無効にすることを選択している可能性があります。

Microsoft アカウントを個人のアカウントで使用しているときにプッシュ通知に切り替える場合は、自分のアカウントをもう一度追加する必要があります。 デバイスを自分のアカウントで再登録し、プッシュ通知を設定します。  

Microsoft Authenticator を職場または学校のアカウントで使用している場合、ワンクリック通知を許可するかどうかは所属している組織が決定します。

### <a name="when-will-i-be-able-to-use-one-click-push-notifications-on-iphone-or-ipad"></a>iPhone または iPad でワンクリック プッシュ通知を使用できるようになるのはいつですか。
この機能は、Microsoft アカウントに一般提供される 8 月末までベータ版です。 ベータ プログラムに参加するには、 msauthenticator@microsoft.com. 名、姓、および Apple ID をメッセージに含めてください。  

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>ワンクリック プッシュ通知は Microsoft 以外のアカウントでも機能しますか。
残念ですが、プッシュ通知は、Microsoft アカウントと Azure Active Directory アカウントでのみ機能します。 職場または学校で Azure AD アカウントを使用している場合は、この機能を無効にしている可能性があります。  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>デバイスをバックアップから復元しましたが、アカウント コードが見つからないか、機能していません。 なぜでしょうか?
セキュリティのため、アプリのバックアップからアカウントは復元されません。 iOS アプリをバックアップから復元した場合、アカウントは引き続き表示されますが、サインインの検証を受け取ることも、セキュリティ コードを生成することもできません。 アプリケーションを復元したら、アカウントを削除し、もう一度追加してください。

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>新しいデバイスを取得しました。 古いデバイスから Microsoft Authenticator アプリを削除して新しいデバイスに移すにはどうすればよいですか。
新しいデバイスに Microsoft Authenticator アプリを追加しても、他のデバイスからそのアプリが自動的に削除されることはありません。 アカウントで構成されるデバイスを管理するには、2 段階認証を管理するために使用するのと同じ Web サイトにアクセスして、古いアプリの削除を選択してください。

個人の Microsoft アカウントでは、この Web サイトは、[アカウント セキュリティ](https://account.microsoft.com/security) ページです。 職場または学校の Microsoft アカウントでは、この Web サイトは、[MyApps](https://myapps.microsoft.com) または所属する組織がセットアップしたカスタム ポータルのいずれかです。

### <a name="how-do-i-remove-an-account-from-the-app"></a>アプリからアカウントを削除するにはどうすればよいですか。
* iOS: メイン画面で、アカウント タイルを左にスワイプします。 **[削除]**を選択します。
* Windows Phone: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。
* Android: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。

所属している組織に登録されている Android デバイスの場合、自分のアカウントを削除するには、追加手順の完了が必要な可能性があります。 これらのデバイスでは、Microsoft Authenticator アプリがデバイスの管理者として自動的に登録されます。 アプリを完全にアンインストールする場合は、その前にアプリ設定でアプリの登録を解除する必要があります。

### <a name="why-does-the-app-request-so-many-permissions"></a>アプリで多くのアクセス許可が要求されるのはなぜですか。
要求されるアクセス許可の完全な一覧と、アプリでの用途を次に示します。

* **カメラ**: 職場のアカウント、学校のアカウント、または Microsoft 以外のアカウントを追加すると、カメラを使用して QR コードがスキャンされます。
* **連絡先と電話番号**: 個人の Microsoft アカウントでサインインすると、電話で使用されている既存のアカウントを検索することでプロセスの簡略化が図られます。
* **SMS**: 個人の Microsoft アカウントで初めてサインインすると、電話番号が記録されているものと一致することの確認が必要になります。 アプリをダウンロードした電話にテキスト メッセージが送信されます。 このメッセージには、6 ～ 8 桁の検証コードが含まれています。 このコードを見つけてアプリに入力する代わりに、テキスト メッセージ内のテキストが自動的に検索されます。
* **他のアプリの上に表示**: 本人確認通知を受信すると、他のアプリが実行されていれば、その上に通知が表示されます。
* **インターネットからのデータの受信**: このアクセス許可は、通知を送信するために必要です。
* **電話がスリープしないようにする**: 所属する組織にデバイスを登録している場合は、組織が電話に関するこのポリシーを変更している可能性があります。
* **振動の制御**: 本人確認通知を受信したときに、振動させるかどうかを選択できます。
* **指紋ハードウェアの使用**: 一部の職場と学校のアカウントでは、本人確認を行うときに、追加の PIN が必要です。 プロセスを簡単にするために、PIN を入力する代わりに指紋を使用できます。
* **ネットワーク接続の表示**: アプリに Microsoft アカウントを追加するにはネットワーク/インターネット接続が必要です。
* **ストレージの内容の読み取り**: このアクセス許可は、アプリ設定で技術的な問題を報告するときにのみ使用されます。 問題を診断するために、ストレージから情報が収集されます。
* **完全なネットワーク アクセス**: このアクセス許可は、本人確認通知を送信するために必要です。
* **スタートアップ時に実行**: このアクセス許可は、電話機を再起動した場合でも、引き続き本人確認通知を受信できるようにします。

## <a name="contact-us"></a>お問い合わせ
このページで質問の答えが見つからない場合は、ページ下部の該当欄にコメントをご記入ください。 または、[サポートにお問い合わせ](https://support.microsoft.com/contactus)ください。できる限り早く回答いたします。

サポートにお問い合わせいただく際は、次の情報をできるだけお知らせください。

* **ユーザー ID** – サインインするために使用した電子メール アドレスは何ですか。
* **エラーの一般的な説明** – どのようなエラー メッセージが表示されましたか。  エラー メッセージが表示されなかった場合は、気が付いた予期しない動作について詳しく説明してください。
* **ページ** – エラーが表示されたときに、どのページを表示していましたか (URL を含む)。
* **エラー コード** - 受信した特定のエラー コード。
* **セッション ID** - 受信した特定のセッション ID。
* **関連付け ID** – エラーが表示されたときに生成された関連付け ID コード。
* **タイムスタンプ** – エラーが表示された正確な日時 (タイムゾーンを含む)。

情報の大部分は、サインイン ページで確認できます。 サインイン時に確認していない場合は、**[詳細の表示]** を選択します。

![サインイン エラーの詳細](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

これらの情報を含めることで、迅速に問題を解決するのに役立ちます。

## <a name="related-topics"></a>関連トピック
* [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)  
* Microsoft アカウントの [2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* [2 段階認証で発生する問題](multi-factor-authentication-end-user-troubleshoot.md)




<!--HONumber=Nov16_HO3-->


