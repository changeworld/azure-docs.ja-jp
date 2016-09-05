<properties
	pageTitle="方法: Azure AD のパスワードをリセットするための登録 | Microsoft Azure"
	description="Azure Active Directory でパスワードをリセットするために登録する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="asteen"/>

# アカウントへのアクセス権を失わないでください

> [AZURE.IMPORTANT]
**このページが表示される理由:** リンクをたどってこのページにアクセスした場合、管理者によってパスワードのリセット登録が要求されている可能性があります。アプリにアクセスするためには、パスワードのリセット登録を行う必要があります。電話または電子メールの情報を求められたり、セキュリティの質問を設定するよう求められたりすることがあります。その情報がスパム目的に使用されることはないのでご安心ください。いただいた情報は、お客様のアカウントのセキュリティを強化する目的でのみ使用されます。必要な作業は、ここに記載した手順に従って実行できます。

パスワードのリセット登録を最速で行うには、[http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) にアクセスしてください。

 1. [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) に移動します。
 2. ユーザー名とパスワードを入力します。
 3. **[今すぐセットアップ]** をクリックして登録するオプションを選択します。この場合は、**[認証用電話]** で登録する例を示します。

    ![][011]

 4. ドロップダウン リストからご自分の国コードを選択し、**[完全な電話番号 + 市外局番]** を入力します。

    ![][012] ![][013]

 5. **[テキスト メッセージを送信する]** または **[電話で確認コードを受け取る]** オプションのいずれかを選択します。この場合は電話に 6 桁のコードが送信される **[テキスト メッセージを送信する]** を選択します。電話にコードが送信されるまで待機します。

    ![][014]

 6. コードが送信されたらそれを入力ボックスに入力し、[確認] をクリックします。
 7. **[ご利用ありがとうございました]** と表示されたら終わりです。 これで [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) に移動し、登録したものを使用していつでもパスワードをリセットできます。

    ![][015]

 >[AZURE.IMPORTANT] 管理者が 1 つ以上のオプションの登録を許可している場合、携帯電話を紛失した場合や電子メールにアクセスできない場合のために、バックアップのオプションを登録することを強くお勧めします。

## 自分のパスワードを更新する他の方法
パスワードの変更、パスワードのリセットなどの一般的な手順を実行する方法については、次を参照してください。

* [**Office365 の自分のパスワードを変更する**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-o365)
* [**アクセス パネルからパスワードを変更する**](active-directory-passwords-update-methods.md#how-to-change-your-password-from-the-access-panel)
* [**パスワードをリセットする**](active-directory-passwords-update-methods.md#how-to-reset-your-password)
* [**自分のアカウントのロックを解除する**](active-directory-passwords-update-methods.md#how-to-unlock-your-account)
* [**一般的な問題とその解決方法**](active-directory-passwords-update-methods.md#common-problems-and-their-solutions)

## パスワードのリセットに関するドキュメントへのリンク
Azure AD のパスワード管理に関する他のすべてのドキュメント ページへのリンクを以下に示します。

* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) -ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。



[001]: ./media/active-directory-passwords-update-your-own-password/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-update-your-own-password/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-update-your-own-password/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-update-your-own-password/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-update-your-own-password/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-update-your-own-password/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-update-your-own-password/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-update-your-own-password/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-update-your-own-password/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-update-your-own-password/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-update-your-own-password/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-update-your-own-password/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-update-your-own-password/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-update-your-own-password/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-update-your-own-password/015.jpg "Image_015.jpg"

<!---HONumber=AcomDC_0824_2016-->