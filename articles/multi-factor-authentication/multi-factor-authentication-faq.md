<properties 
	pageTitle="Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)" 
	description="Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに多層構造のセキュリティを確保することができます。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)


Azure Multi-factor Authentication に関する質問に回答しています。この FAQ には、課金モデルや使いやすさなど、サービスの使用に関する質問が含まれています。

## 全般

**Q: Azure Multi-factor Authentication に関するヘルプはどのようにして得られますか。**

[Microsoft サポート技術情報 (KB) の検索](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Microsoft Azure Multi-Factor Authentication Server (電話要素) のサポートに関する一般的な障害対応の技術的な解決策について、Microsoft サポート技術情報 (KB) を検索してください。

[Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- このコミュニティで技術的な質問と回答を検索して参照したり、[こちら](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)をクリックして独自の質問を行ったりできます。

[パスワード 発行](mailto:phonefactorsupport@microsoft.com)

- 従来の Phonefactor ユーザーで、パスワードのリセットについて不明な点がある場合や、パスワードのリセットのヘルプが必要な場合は、以下のリンクをクリックしてサポート ケースを開いてください。

[Microsoft Azure Multi-Factor Authentication Server (電話要素) のカスタマー サポート](https://support.microsoft.com/oas/default.aspx?prid=14947)

- このリンクを使用してマイクロソフトのサポート担当者にお問い合わせください。サポート オプションを判断するためいくつかの質問をお尋ねします。サポート オプションとしては、電子メール、オンラインでの送信、電話サポートがあります。 

[Microsoft Azure Multi-Factor Authentication Server (電話要素) に関する一般的な問い合わせ](http://azure.microsoft.com/services/multi-factor-authentication)

- Microsoft Azure Multi-factor Authentication Server (電話要素) について、または本製品の購入方法や利用できる各種サポート オプションについてご質問がある場合、[pfsales@microsoft.com](mailto:pfsales@microsoft.com) 宛に電子メールを送信してください。 



**Q: Azure Multi-Factor Authentication サーバーではどのようにユーザー データが処理されますか。**

オンプレミスで Multi-Factor Authentication (MFA) サーバーを使用すると、ユーザーのデータはオンプレミス サーバーに格納されます。永続的なユーザー データはクラウドに格納されません。ユーザーが 2 要素認証を実行すると、MFA サーバーから Azure MFA クラウド サービスにデータが送信され、認証が実行されます。これらの認証要求がクラウド サービスに送信されると、次のフィールドが要求で送信され、ログに記録されるので、ユーザーの認証/使用状況レポートで使用できます。一部のフィールドは省略可能なので、Multi-Factor Authentication サーバー内で有効または無効にすることができます。MFA サーバーから MFA クラウド サービスへの通信には、送信方向のポート 443 上で SSL/TLS が使用されます。以下のフィールドが対象です。

- 一意の ID - ユーザー名または内部 MFA サーバー ID
- 姓と名 - (省略可能)
- 電子メール アドレス - (省略可能)
- 電話番号 - 音声通話または SMS 認証を行う場合
- デバイス トークン - モバイル アプリ認証を行う場合
- 認証モード 
- 認証の結果 
- MFA サーバー名 
- MFA サーバー IP 
- クライアント IP - 使用可能な場合



以上のフィールドに加え、認証結果 (成功/拒否) と拒否の理由も認証データと共に格納され、認証/使用状況レポートで使用できます。




## 課金

**Q: ユーザーを認証するために使用する通話やテキスト メッセージに関して組織に課金されますか。**

すべての費用は、サービスのユーザーごとまたは組織ごとの費用に加算されます。Azure Multi-Factor Authentication を使用するときに、エンド ユーザーに対して行われる通話や送信されるテキスト メッセージに関して組織に対して課金されることはありません。電話の所有者は、通話またはテキスト メッセージの受信のために電話会社からローミング関連やその他の費用を請求される可能性があります。

**Q: 組織に対する Azure Multi-Factor Authentication 関連の請求はどのように行われますか。**

Microsoft Azure 管理ポータルで多要素認証プロバイダーを作成するときに「ユーザーごと」または「認証ごと」のどちらかの請求/使用法モデルを選択します。これは、使用量をベースとしたリソースで、仮想マシンや Web サイトと同様に、組織の Azure サブスクリプションに対して課金されます。

**Q: 「ユーザーごと」の請求モデルは、Multi-Factor Authentication が有効なユーザー数に基づきますか、それとも確認を実行するユーザー数に基づいて課金されるのでしょうか。**

請求は、Multi-Factor Authentication が有効なユーザーの数に基づいて行われます。

## 使いやすさ

**Q: 電話に応答が届かない場合、または電話を忘れた場合はどうすればよいでしょうか。**

予備の電話番号を構成している場合は、サインイン ページでプロンプトが表示されたらその電話番号を選択して再試行します。別の方法を構成していない場合には、管理者に連絡して、メインの電話番号 (携帯また職場) に割り当てられている電話番号を更新してもらってください。

**Q: 管理者ロールからユーザーを削除しましたが、多要素認証を無効にするのを忘れ、一覧に表示されなくなってしまいました。この機能をどのようにすれば削除できますか。**

- 使用しているポータルに応じて、左側のウィンドウで、[ユーザー] または [ユーザーとグループ] をクリックします。
- 編集するユーザーの隣にあるチェック ボックスをオンにして、[編集] または編集アイコンをクリックします。
- [設定] をクリックし、[ロールの割り当て] で [はい] を選択し、以前の管理者ロールにユーザーを追加します。
- 多要素認証ページに移動します。対象アカウントが、ページ上にある一覧の上部に表示されているはずです。 
- 前述の手順に従って、アカウントの多要素認証を無効にします。 
- この時点で、管理者ロールから対象アカウントを削除できます。


**Q: ユーザーから連絡を受けたときにアカウントがロックアウトされている場合、管理者はどうすればよいでしょうか。**

そのユーザーが登録プロセスを再度実行するように強制的にリセットできます。実行方法については、「[クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理](multi-factor-authentication-manage-users-and-devices.md)」を参照してください。

**Q: ユーザーがアプリ パスワードを紛失した、またはアプリ パスワードを使用しているデバイスが盗難に遭った場合、どうすればよいでしょうか。**

そのユーザーのすべてのアプリ パスワードを削除し、承認されていないアクセスを防ぐことできます。代わりのデバイスを入手したら、パスワードを再作成できます。実行方法については、「[クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理](multi-factor-authentication-manage-users-and-devices.md)」を参照してください。

**Q: ユーザーが非ブラウザー アプリにサインインできない場合、どうすればよいでしょうか。**

- 多要素認証が有効なユーザーが非ブラウザー アプリにサインインするためには、アプリ パスワードが必要になります。
- ユーザーはサインイン情報をクリア (削除) してから、アプリケーションを再起動し、ユーザー名とアプリ パスワードを使用してサインインしなければなりません。 

アプリ パスワードを作成する方法について、「[アプリ パスワードに関するヘルプ](multi-factor-authentication-end-user-app-passwords.md)」を参照してください。


>[AZURE.NOTE]Office 2013 クライアントのための最新の認証
>
> Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートするようになり、Multi-Factor Authentication をサポートするように有効化できます。つまり、Multi-Factor Authentication を有効にすると、Office 2013 クライアントでアプリ パスワードは不要になります。詳しくは、「[発表された Office 2013 の最新の認証のパブリック プレビュー](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」を参照してください。

**Q: テキスト メッセージを受信しない、または双方向のテキスト メッセージに返信しましたが確認がタイムアウトになる場合、どうすればよいでしょうか。**

Azure Multi-factor Authentication サービスは、SMS アグリゲーターを使用してテキスト メッセージを送信します。使用されるアグリゲーター、宛先の国、携帯電話の事業者、信号強度など、多くの要因がテキスト メッセージの配信と受信の信頼性に影響を及ぼす可能性があります。そのため、双方向の SMS を実行するときにテキスト メッセージの配信および SMS 応答の受信が保証されるわけではありません。可能な場合には双方向の SMS ではなく一方向の SMS の使用をお勧めします。信頼性が高いこと、および別の国から送信されたテキスト メッセージに応答して高額な国際 SMS 料金をユーザーが課金されるのを防ぐことができるためです。

またテキスト メッセージによる確認は、米国やカナダなどの一部の国ではより信頼性が高くなります。Azure Multi-factor Authentication を使用して信頼性の高いテキスト メッセージを受信することが難しいユーザーは、代わりにモバイル アプリまたは電話による方法を選択することをお勧めします。モバイル アプリによる通知は携帯電話でも Wi-Fi 接続でも受信可能で、デバイスがまったく応答信号を出さない場合でもモバイル アプリ パスコードが表示されるため、便利です。Azure Authenticator アプリは、[Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、[IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) で利用できます。


## エラー

**Q: モバイル アプリケーション通知を使用して認証を行うときに、「認証しようとしているアカウントはアクティブ化されていません」というエラーが表示された場合はどうすればでしょうか。**

- [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) に移動し、お客様の組織アカウントを使用してサインインします。
- 必要に応じて、[その他の確認オプション] をクリックし、別のオプションを選択して、アカウント確認を実行します。
- [追加のセキュリティ確認] をクリックします。
- モバイル アプリケーションから既存のアカウントを削除します。
- [構成] をクリックして、モバイル アプリケーションを再構成するための手順を実行します。




**Q: 非ブラウザー アプリケーションを使用してサインインするときに、0x800434D4L エラーが表示された場合はどうすればよいでしょうか。**

現時点では、追加のセキュリティ確認は、ブラウザーからアクセスできるアプリケーションまたはサービスでのみ使用できます。Windows Powershell など、ローカル コンピューターにインストールされている非ブラウザー アプリケーション (リッチ クライアント アプリケーションとも呼ばれます) は、追加のセキュリティ確認に必要なアカウントでは機能しません。この場合、アプリケーション生成エラー 0x800434D4L が表示されることがあります。

これを回避するには、管理関連の操作と管理以外の操作に関して異なるユーザー アカウントを使用します。後ほど、管理アカウントと非管理アカウント間のメールボックスでリンクを作成することによって、非管理アカウントを使用して outlook にサインインできます。詳細については、「[管理者がユーザーのメールボックスの内容を開いたり表示したりできるようにする](http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1)」 を参照してください。

<!----HONumber=Nov15_HO4-->