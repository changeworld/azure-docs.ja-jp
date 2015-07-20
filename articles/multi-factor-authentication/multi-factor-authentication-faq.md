<properties 
	pageTitle="Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)" 
	description="Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに多層構造のセキュリティを確保することができます。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadwha" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)

ここでは、Azure Multi-Factor Authentication に関してよく寄せられる質問について取り上げます。ヒントやトラブルシューティング情報が含まれています。右側のナビゲーション メニューを使用すると、対象の質問をすばやく検索できます。

## Azure Multi-factor Authentication に関するヘルプはどのようにして得られますか。
[Microsoft サポート技術情報 (KB) の検索](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Microsoft Azure Multi-Factor Authentication Server (電話要素) のサポートに関する一般的な障害対応の技術的な解決策について、Microsoft サポート技術情報 (KB) を検索してください。

[Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- このコミュニティーで技術的問題に関する質問や回答を検索して参照したり、[こちら](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)をクリックして独自の質問を行ったりできます。

[パスワード リセット](mailto:phonefactorsupport@microsoft.com)

- パスワードのリセット、またはパスワードのリセットに関するヘルプの取得法についての質問は、[phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) 宛に電子メールを送信してください。

[Microsoft Azure Multi-Factor Authentication Server (電話要素) のカスタマー サポート](https://support.microsoft.com/oas/default.aspx?prid=14947)

- このリンクを使用してマイクロソフトのサポート担当者にお問い合わせください。サポート オプションを判断するためいくつかの質問をお尋ねします。サポート オプションとしては、電子メール、オンラインでの送信、電話サポートがあります。 

[Microsoft Azure Multi-Factor Authentication Server (電話要素) に関する一般的な問い合わせ](http://azure.microsoft.com/services/multi-factor-authentication)

- Microsoft Azure Multi-factor Authentication Server (電話要素) について、または本製品の購入方法や利用できる各種サポート オプションについてご質問がある場合、[pfsales@microsoft.com](mailto:pfsales@microsoft.com) 宛に電子メールを送信してください。 


## モバイル アプリケーション通知を使用して認証を行うときに、「認証しようとしているアカウントはアクティブ化されていません」というエラーが表示された場合はどうすればでしょうか。

- [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) に移動し、お客様の組織アカウントを使用してサインインします。
- 必要に応じて、[その他の確認オプション] をクリックし、別のオプションを選択して、アカウント確認を実行します。
- [追加のセキュリティ確認] をクリックします。
- モバイル アプリケーションから既存のアカウントを削除します。
- [構成] をクリックして、モバイル アプリケーションを再構成するための手順を実行します。


## 電話に応答が届かない場合、または電話を忘れた場合はどうすればよいでしょうか。

予備の電話番号を構成している場合は、サインイン ページでプロンプトが表示されたらその電話番号を選択して再試行します。別の方法を構成していない場合には、管理者に連絡して、メインの電話番号 (携帯また職場) に割り当てられている電話番号を更新してもらってください。

## 非ブラウザー アプリケーションを使用してサインインするときに、0x800434D4L エラーが表示された場合はどうすればよいでしょうか。
現時点では、追加のセキュリティ確認は、ブラウザーからアクセスできるアプリケーションまたはサービスでのみ使用できます。Windows Powershell など、ローカル コンピューターにインストールされている非ブラウザー アプリケーション (リッチ クライアント アプリケーションとも呼ばれます) は、追加のセキュリティ確認に必要なアカウントでは機能しません。この場合、アプリケーション生成エラー 0x800434D4L が表示されることがあります。

これを回避するには、管理関連の操作と管理以外の操作に関して異なるユーザー アカウントを使用します。後ほど、管理アカウントと非管理アカウント間のメールボックスでリンクを作成することによって、非管理アカウントを使用して outlook にサインインできます。詳細については、「管理者がユーザーのメールボックスの内容を開いたり表示したりできるようにする」(http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1)) をご覧ください。




## 管理者ロールからユーザーを削除しましたが、多要素認証を無効にするのを忘れ、一覧に表示されなくなってしまいました。この機能をどのようにすれば削除できますか。

- 使用しているポータルに応じて、左側のウィンドウで、[ユーザー] または [ユーザーとグループ] をクリックします。
- 編集するユーザーの隣にあるチェック ボックスをオンにして、[編集] または編集アイコンをクリックします。
- [設定] をクリックし、[ロールの割り当て] で [はい] を選択し、以前の管理者ロールにユーザーを追加します。
- 多要素認証ページに移動します。対象アカウントが、ページ上にある一覧の上部に表示されているはずです。 
- 前述の手順に従って、アカウントの多要素認証を無効にします。 
- この時点で、管理者ロールから対象アカウントを削除できます。


## ユーザーから連絡を受けたときにアカウントがロックアウトされている場合、管理者はどうすればよいでしょうか。
そのユーザーが登録プロセスを再度実行するように強制的にリセットできます。実行方法については、「[クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理](multi-factor-authentication-manage-users-and-devices.md)」をご覧ください。

## ユーザーがアプリ パスワードを紛失した、またはアプリ パスワードを使用しているデバイスが盗難に遭った場合、どうすればよいでしょか。
そのユーザーのすべてのアプリ パスワードを削除し、承認されていないアクセスを防ぐことできます。代わりのデバイスを入手したら、パスワードを再作成できます。実行方法については、「[クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理](multi-factor-authentication-manage-users-and-devices.md)」をご覧ください。

## ユーザーが非ブラウザー アプリにサインインできない場合、どうすればよいでしょうか。

- 多要素認証が有効なユーザーが非ブラウザー アプリにサインインするためには、アプリ パスワードが必要になります。
- ユーザーはサインイン情報をクリア (削除) してから、アプリケーションを再起動し、ユーザー名とアプリ パスワードを使用してサインインしなければなりません。 

アプリ パスワードを作成する方法について、「[アプリ パスワードに関するヘルプ](multi-factor-authentication-end-user-app-passwords.md)」をご覧ください。


>[AZURE.NOTE]Office 2013 クライアントのための最新の認証
>
> Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートするようになり、Multi-Factor Authentication に対応できます。つまり、Multi-Factor Authentication を有効にすると、Office 2013 クライアントの使用に関してはアプリ パスワードは不要になります。詳しくは、「[発表された Office 2013 の最新の認証のパブリック プレビュー](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」をご覧ください。

## テキスト メッセージを受信しない、または双方向のテキスト メッセージに返信しましたが確認がタイムアウトになる場合、どうすればよいでしょか。
Azure Multi-factor Authentication サービスは、SMS アグリゲーターを使用してテキスト メッセージを送信します。使用されるアグリゲーター、宛先の国、携帯電話の事業者、信号強度など、多くの要因がテキスト メッセージの配信と受信の信頼性に影響を及ぼす可能性があります。そのため、双方向の SMS を実行するときにテキスト メッセージの配信および SMS 応答の受信が保証されるわけではありません。可能な場合には双方向の SMS ではなく一方向の SMS の使用をお勧めします。信頼性が高いこと、および別の国から送信されたテキスト メッセージに応答して高額な国際 SMS 料金をユーザーが課金されるのを防ぐことができるためです。

またテキスト メッセージによる確認は、米国やカナダなどの一部の国ではより信頼性が高くなります。Azure Multi-factor Authentication を使用して信頼性の高いテキスト メッセージを受信することが難しいユーザーは、代わりにモバイル アプリまたは電話による方法を選択することをお勧めします。モバイル アプリによる通知は携帯電話でも Wi-Fi 接続でも受信可能で、デバイスがまったく応答信号を出さない場合でもモバイル アプリ パスコードが表示されるため、便利です。Azure Authenticator アプリは、[Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、[IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) で利用できます。


## ユーザーを認証するために使用する通話やテキスト メッセージに関して組織に課金されますか。
すべての費用は、サービスのユーザーごとまたは組織ごとの費用に加算されます。Azure Multi-Factor Authentication を使用するときに、エンド ユーザーに対して行われる通話や送信されるテキスト メッセージに関して組織に対して課金されることはありません。電話の所有者は、通話またはテキスト メッセージの受信のために電話会社からローミング関連やその他の費用を請求される可能性があります。

## 組織に対する Azure Multi-Factor Authentication 関連の請求はどのように行われますか。
Microsoft Azure 管理ポータルで多要素認証プロバイダーを作成するときに「ユーザーごと」または「認証ごと」のどちらかの請求/使用法モデルを選択します。これは、使用量をベースとしたリソースで、仮想マシンや Web サイトと同様に、組織の Azure サブスクリプションに対して課金されます。

## 「ユーザーごと」の請求モデルは、Multi-Factor Authentication が有効なユーザー数に基づきますか、それとも確認を実行するユーザー数に基づいて課金されるのでしょうか。
請求は、Multi-Factor Authentication が有効なユーザーの数に基づいて行われます。

<!---HONumber=July15_HO2-->