---
title: Microsoft Authenticator アプリに関するヘルプ - Azure AD | Microsoft Docs
description: Microsoft 認証アプリと Multi-factor Authentication に関してよく寄せられる質問の一覧を提供します。
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 9027b09c186dfb7661fc63f200f4d2e5da96a70a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130103"
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator アプリに関する FAQ

この記事では、Microsoft Authenticator アプリに関する一般的な質問にお答えします。 質問に対する回答がここで見つからない場合は、[Microsoft Authenticator アプリ フォーラム](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)をご覧ください。 アプリの特定の機能については、[携帯電話でのサインインに関する FAQ](microsoft-authenticator-app-phone-signin-faq.md) も参照できます。

Microsoft Authenticator アプリは、Azure Authenticator アプリに置き換わり、Azure 多要素認証を使用するときに推奨されるアプリです。 Microsoft Authenticator アプリは、[Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594)、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) で利用できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Authenticator でユーザーについてどのようなデータが格納されますか? また、どうすれば削除できますか?

Microsoft Authenticator には、アカウントの追加時に作成するアカウント情報が格納されます。 Authenticator を使用すると、診断ログがデバッグ目的で作成されます。これには、予期しない問題をマイクロソフトが診断するときに役立つ情報が格納されます。 ログ データにアクセスするには、**[ヘルプ]** > **[ログの送信]** > **[ログの表示]** の順に開きます。

データを削除するには、アカウント タイルを削除します。 アカウント タイルを削除すると、ログを含め、アプリケーションによって使用されているすべてのアカウント情報が削除されます。 

マイクロソフトがユーザー データをどのように使用するかについて詳しくは、https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted をご覧ください。

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>アプリのコードの目的は何ですか。 番号が常にカウントダウンされるのはなぜですか。

Microsoft Authenticator アプリを開くと、追加したアカウントが表示され、各アカウントのそばに 6 桁または 8 桁の番号が表示されます。 30 秒タイマーがカウントダウンしているのがわかります。

これらのコードは、アカウントにサインインするときに使用されます。 ユーザー名とパスワードを入力した後に、確認コードの入力を求められる場合があります。 Microsoft Authenticator アプリを開き、現在表示されているコードをコピーします。 サインイン ページにそのコードを入力すると、操作が完了します。

コードが 30 秒ごとに変更されるのは、同じコードを 2 回使用できないようにするためです。 パスワードとは異なり、コードを覚えておく必要はありません。 つまり、その携帯電話にアクセスできるユーザーにしか確認コードはわからないということです。

コードにはインターネットもデータも不要であるため、電話サービスのサインインについて心配する必要はありません。 アプリを閉じると、アプリがバックグラウンドで動作し続けることはないので、バッテリが消費されることはありません。 アプリを閉じたら、次回サインインするときまでアプリを無視できます。  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>アプリを開いているときにのみ通知を受け取ります。 アプリを開いていないと、まったく通知を受け取りません。

着信音がオンであるのに、通知の受け取り時に、着信音もバイブレーションも作動しない場合、まずアプリの設定を確認します。 通知時のサウンドまたはバイブレーション使用を有効にします。

通知をまったく受け取らない場合は、次の項目を確認します。

- 電話が応答しないモード、または静音モードになっていませんか。 そのモードでは、アプリは通知を送信しません。
- 他のアプリから通知を受け取ることができますか。 受け取らない場合は、電話のネットワーク接続、または Android や Apple からの通知チャネルに問題がある可能性があります。 前者の場合は、電話の設定で対応できますが、後者の場合は、サービス プロバイダーに問い合わせて、サポートを依頼する必要があります。
- アプリケーションの一部のアカウントでは通知を受け取るが、他では受け取れない状態ですか。 その場合は、アプリから問題のあるアカウントを削除し、そのアカウントを再び追加して、プッシュ通知を再有効化します。

これらのトラブルシューティング案を試しても問題が解決しない場合は、診断のためにログを送信できます。 アプリ設定に移動し、**[ヘルプとフィードバック]**、**[ログの送信]** の順に選択します。 次に [Microsoft Authenticator アプリ フォーラム](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)に移動し、発生している問題の内容とこれまでに試した手順をお知らせください。

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>検証コードで Microsoft Authenticator アプリケーションを既に使用しています。 ワンクリック プッシュ通知に切り替えるにはどうすればよいですか。
プッシュ通知によるサインインの承認は、個人の Microsoft アカウントと職場または学校の Microsoft アカウントでのみ可能であり、Google や Facebook などのサード パーティのアカウントでは使用できません。 職場または学校の Microsoft アカウントを使用している場合、所属している組織がこのオプションを無効にすることを選択している可能性があります。

Microsoft アカウントを個人のアカウントで使用しているときにプッシュ通知に切り替える場合は、自分のアカウントをもう一度追加する必要があります。 デバイスを自分のアカウントで再登録し、プッシュ通知を設定します。  

Microsoft Authenticator を職場または学校のアカウントで使用している場合、ワンクリック通知を許可するかどうかは所属している組織が決定します。

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>ワンクリック プッシュ通知は Microsoft 以外のアカウントでも機能しますか。
残念ですが、プッシュ通知は、Microsoft アカウントと Azure Active Directory アカウントでのみ機能します。 職場または学校で Azure AD アカウントを使用している場合は、この機能を無効にしている可能性があります。  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>新しいデバイスを取得しました。または自分のデバイスをバックアップから復元しました。 Microsoft Authenticator アプリで自分のアカウントをもう一度設定するにはどうすればよいですか。
IOS デバイスを実行している場合は、**[自動バックアップ]** をオンにして、アカウントのバックアップを古いデバイス 上に作成します。そのバックアップを使用して、新しいデバイス上に自分のアカウントの資格情報を回復できます。 詳細については、「[Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-backup-and-recovery.md)」を参照してください。 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>デバイスを紛失しました。または新しいデバイスに移行しました。 通知が古いデバイスに引き続き送信されないようにするにはどうすればよいですか。  
新しい iOS デバイスに Microsoft Authenticator アプリを追加しても、古いデバイスからアプリが自動的に削除されることはありません。 古いデバイスからのアプリの削除でも十分ではありません。 古いデバイスからアプリを削除することと、古いデバイスを置き忘れたため、アカウントから登録を解除するように Microsoft または組織に通知することの両方を行う必要があります。
- **個人の Microsoft アカウントを使用してデバイスからアプリを削除するには:**  [[アカウント セキュリティ]](https://account.microsoft.com/security)  ページの 2 段階認証領域に移動し、古いデバイスの検証をオフにすることを選択します。  
- **職場または学校の Microsoft アカウントを使用してデバイスからアプリを削除するには:**  [[MyApps]](https://myapps.microsoft.com/) ページの 2 段階認証領域または組織のカスタム ポータルに移動し、古いデバイスの検証をオフにすることを選択します。 



### <a name="how-do-i-remove-an-account-from-the-app"></a>アプリからアカウントを削除するにはどうすればよいですか。
* iOS: メイン画面で、アカウント タイルを左にスワイプします。 **[削除]** を選択します。
* Windows Phone: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。
* Android: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。

所属している組織に登録されているデバイスの場合、自分のアカウントを削除するには、追加手順の完了が必要な可能性があります。 これらのデバイスでは、Microsoft Authenticator アプリがデバイスの管理者として自動的に登録されます。 アプリを完全にアンインストールする場合は、その前にアプリ設定でアプリの登録を解除する必要があります。

### <a name="why-does-the-app-request-so-many-permissions"></a>アプリで多くのアクセス許可が要求されるのはなぜですか。
要求される可能性があるアクセス許可の完全な一覧と、アプリでの用途を次に示します。 表示される具体的なアクセス許可は、電話の種類によって異なります。

* **カメラ**: 職場のアカウント、学校のアカウント、または Microsoft 以外のアカウントを追加する際に、QR コードをスキャンするために使用されます。
* **連絡先と電話番号**: 個人の Microsoft アカウントでサインインする際に、電話で使用されている既存のアカウントを検索することでプロセスを簡略化するために使用されます。
* **SMS**: 電話番号が、記録された番号と一致することを確認するために使用されます。 これは、個人の Microsoft アカウントで初めてサインインする際に使用されます。  アプリをダウンロードした電話に、6 ～ 8 桁の検証コードを含むテキスト メッセージが送信されます。 このコードを見つけてアプリに入力する代わりに、テキスト メッセージ内のテキストが自動的に検索されます。
* **他のアプリの上に描画**: 本人確認通知が、実行されている場合がある他のアプリの上にも表示されます。
* **インターネットからのデータの受信**: このアクセス許可は、通知を送信するために必要です。
* **電話がスリープしないようにする**: 所属する組織にデバイスを登録している場合は、組織が電話に関するこのポリシーを変更している可能性があります。
* **振動の制御**: 本人確認通知を受信したときに、振動させるかどうかを選択できます。
* **指紋ハードウェアの使用**: 一部の職場と学校のアカウントでは、本人確認を行うときに、追加の PIN が必要です。 プロセスを簡単にするために、PIN を入力する代わりに指紋を使用できます。
* **ネットワーク接続の表示**: アプリに Microsoft アカウントを追加するにはネットワーク/インターネット接続が必要です。
* **ストレージの内容の読み取り**: このアクセス許可は、アプリ設定で技術的な問題を報告するときにのみ使用されます。 問題を診断するために、ストレージから情報が収集されます。
* **完全なネットワーク アクセス**: このアクセス許可は、本人確認通知を送信するために必要です。
* **スタートアップ時に実行**: このアクセス許可は、電話機を再起動した場合でも、引き続き本人確認通知を受信できるようにします。

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Microsoft Authenticator アプリで、デバイスのロックを解除せずに要求を承認できるのはなぜですか。

本人を確認するために必要なことは、電話を所持していることだけなので、検証要求を承認するためにデバイスのロックを解除する必要はありません。 2 段階認証では、2 つのもの、つまりユーザーが知っているものとユーザーが持っているものを証明する必要があります。 知っているものは、パスワードです。 持っているものは、(Microsoft Authenticator アプリで設定され、MFA の確認手段として登録された) 電話です。したがって、電話を持っていて要求を承認することは、認証の 2 番目の要素に関する条件を満たします。

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>アカウント一覧のロック アイコンは何を意味していますか。

南京錠アイコンは、デバイスが Azure AD に登録され、アカウントに登録されていることを示しています。 iOS 向けのデバイス登録は、Microsoft Intune 登録時に行われます。

## <a name="next-steps"></a>次の手順

### <a name="contact-us"></a>お問い合わせ
疑問点への答えがここで見つからなかった場合は、ぜひお知らせください。 [Microsoft Authenticator アプリ フォーラム](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)に質問を投稿してコミュニティからのサポートを受けるか、このページにコメントを残してください。


### <a name="related-topics"></a>関連トピック
* Microsoft アカウントの [2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* 職場または学校のアカウントの [2 段階認証で問題が発生した場合](multi-factor-authentication-end-user-troubleshoot.md)
* [Microsoft Authenticator を使って携帯電話からサインインする](microsoft-authenticator-app-phone-signin-faq.md)
