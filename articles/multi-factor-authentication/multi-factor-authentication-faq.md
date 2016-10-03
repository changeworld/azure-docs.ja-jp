<properties
	pageTitle="Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)"
	description="Azure Multi-Factor Authentication に関してよく寄せられる質問の一覧を提供します。Azure Multi-Factor Authentication は、本人確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに多層構造のセキュリティを確保することができます。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)


この FAQ では、Azure Multi-Factor Authentication と Multi-Factor Authentication サービスの利用について、課金モデルや使いやすさに関する疑問を含め、よく寄せられる質問に答えます。

## 全般

**Q: Azure Multi-factor Authentication に関するヘルプはどのようにして得られますか。**

- [Microsoft サポート技術情報の検索](https://www.microsoft.com/ja-JP/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)

  サポート技術情報を検索して、一般的な技術上の問題の解決方法を探します。

- [Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

  このコミュニティで技術的な質問と回答を検索して参照したり、[Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)で独自の質問を投稿したりできます。

- [パスワード 発行](mailto:phonefactorsupport@microsoft.com)

  従来の PhoneFactor をご利用中で、パスワードのリセットについてご質問がある場合やサポートを必要とされている場合は、[パスワード リセット](mailto:phonefactorsupport@microsoft.com)のリンクを使用してサポート ケースを開いてください。

- [Azure Multi-Factor Authentication Server (PhoneFactor) のサポート](https://support.microsoft.com/oas/default.aspx?prid=14947)

  このリンクを使用してマイクロソフトのサポート担当者にお問い合わせください。電子メール、オンラインによる送信、電話でのサポートなど、サポート オプションを特定するためにいくつかの質問にお答えいただくことになります。



**Q: Azure Multi-Factor Authentication Server ではどのようにユーザー データが処理されますか。**

Multi-Factor Authentication Server を使用すると、ユーザーのデータはオンプレミス サーバーのみに格納されます。永続的なユーザー データはクラウドに格納されません。ユーザーが 2 要素認証を実行すると、Multi-Factor Authentication Server から Azure Multi-Factor Authentication クラウド サービスにデータが送信され、認証が要求されます。認証要求がクラウド サービスに送信されるとき、次のリストのフィールドのデータが要求で送信され、ログに記録されるので、ユーザーの認証および使用状況のレポートで使用できます。一部のフィールドは省略可能であり、Multi-Factor Authentication Server で構成することができます。Multi-Factor Authentication Server から Multi-Factor Authentication クラウド サービスへの通信には、送信方向のポート 443 経由で Secure Sockets Layer (SSL) またはトランスポート層セキュリティ (TLS) が使用されます。2 要素認証のログに含まれるデータ フィールドは次のとおりです。

- **一意の ID** (ユーザー名またはオンプレミスの Multi-Factor Authentication Server ID のいずれか)
- **姓と名** (省略可能)
- **電子メール アドレス** (省略可能)
- **電話番号** (音声通話または SMS 認証を使用する場合)
- **デバイス トークン** (モバイル アプリ認証を使用する場合)
- **認証モード**
- **認証の結果**
- **Multi-Factor Authentication のサーバー名**
- **Multi-Factor Authentication のサーバー IP**
- **クライアント IP** (使用可能な場合)



以上のフィールドに加えて、認証結果 (成功または拒否) と拒否の理由も認証データと共に格納され、認証レポートと使用状況レポートで使用できます。




## 課金

**Q: ユーザーを認証するために使用する通話やテキスト メッセージに関して組織に課金されますか。**

すべての費用は、サービスのユーザーごとまたは組織ごとの費用に加算されます。Azure Multi-Factor Authentication 経由でユーザーに対して行われる通話や送信されるテキスト メッセージに関して、組織に対して課金されることはありません。電話の所有者は、通話またはテキスト メッセージの受信のために電話サービス会社からローミング関連やその他の費用を請求される可能性があります。

**Q: 組織に対する Azure Multi-Factor Authentication 関連の請求はどのように行われますか。**

Azure Multi-Factor Authentication は、ユーザーごとおよび認証ごとの課金オプション付きスタンドアロン サービスとして、または Azure Active Directory Premium、Enterprise Mobility Suite、または Enterprise Cloud Suite とのセットで利用できます。スタンドアロン サービスは、使用量ベースで利用可能であり、組織の Azure 年額コミットメントに対して毎月請求されます。または、Microsoft エンタープライズ契約、Microsoft Open License プログラム、Microsoft Cloud Solution Providers プログラム (CSP)、または Direct を通じて入手できる、ユーザーごとの年間ランセンスでも利用できます。

>[AZURE.IMPORTANT]
オーストラリア リージョンをご利用いただけるのは、オーストラリアまたはニュージーランドに事業の活動場所を持つお客様のみです。

課金モデル | 料金
------------- | ------------- |
ユーザーごとの使用量 (Azure 年額コミットメント)| $1.40/月 (認証回数の制限なし)
認証ごとの使用量 (Azure 年額コミットメント)|$1.40/10 認証回数
ユーザーごとの年間ライセンス (Direct)|$1.40/月 (認証回数の制限なし)
ユーザーごとの年間ライセンス (ボリューム ライセンス)|[エンタープライズ契約の担当者](https://www.microsoft.com/ja-JP/licensing/licensing-programs/enterprise.aspx)にお問い合わせください。

**Q: 「ユーザーごと」の課金モデルは、Multi-Factor Authentication を使用できるように構成されているユーザー数に基づきますか、それとも確認を実行するユーザー数に基づいて課金されるのでしょうか。**

請求は、Multi-Factor Authentication を使用できるように構成されているユーザーの数に基づいて行われます。

**Q: Multi-Factor Authentication の請求はどのように行われますか。**

組織でユーザーごとまたは認証ごとの使用料課金と使用モデルを使用する場合、組織の管理者は Azure クラシック ポータルで Multi-Factor Authentication プロバイダーを作成するときに使用の種類を選択します。このリソースについては、仮想マシンや Web サイトなどと同様に、組織の Azure サブスクリプションに対して課金されます。ライセンス モデルを使用する場合は、Azure Multi-Factor Authentication ライセンスを購入してユーザーに割り当てます。これは、Office 365 や他のサブスクリプション製品と同様です。

**Q: 管理者用の Azure Multi-Factor Authentication の無料版はありますか。**

使用量ベースの Azure Multi-Factor Authentication プロバイダーが、Azure Active Directory の対応するインスタンスにリンクされていない場合は、Multi-Factor Authentication for Azure Administrators と呼ばれる Azure Multi-Factor Authentication 機能の一部が、Azure グローバル管理者グループのメンバーに無料で提供されます。Multi-Factor Authentication プロバイダーを使用すると、Multi-Factor Authentication を使用できるように構成されているディレクトリ内のすべての管理者とユーザーが、Azure Multi-Factor Authentication のフル バージョンにアップグレードされます。

**Q: Office 365 ユーザー用の Azure Multi-Factor Authentication の無料版はありますか。**

使用量ベースの Azure Multi-Factor Authentication プロバイダーが、Azure Active Directory の対応するインスタンスにリンクされていない場合は、Multi-Factor Authentication for Office 365 と呼ばれる Azure Multi-Factor Authentication 機能の一部が、Office 365 のライセンスが割り当てられているユーザーに無料で提供されます。Multi-Factor Authentication プロバイダーを使用すると、Multi-Factor Authentication を使用できるように構成されているディレクトリ内のすべての管理者とユーザーが、Azure Multi-Factor Authentication のフル バージョンにアップグレードされます。

**Q: ユーザーごとと認証ごとの使用量課金モデルは、組織でいつでも切り替えることができますか。**

組織でリソースを作成するときに課金モデルを選択します。リソースをプロビジョニングした後に課金モデルを変更することはできません。ただし、新しい Multi-Factor Authentication リソースを作成して元のリソースを置き換えることは可能です。ユーザー設定と構成オプションを新しいリソースに転送することはできません。

**Q: 使用量課金とライセンス モデルは、組織でいつでも切り替えることができますか。**

Azure Multi-Factor Authentication、Azure Active Directory Premium、Enterprise Mobility Suite、および Enterprise Cloud Suite ライセンスは、組織でいつでも購入できます。既にユーザーごとの Azure Multi-Factor Authentication プロバイダーを持っているディレクトリにライセンスが追加されると、所有ライセンス数分が使用量ベース課金から差し引かれます。Multi-Factor Authentication を使用できるように構成されているすべてのユーザーにライセンスが割り当てられている場合、管理者は Azure Multi-Factor Authentication プロバイダーを削除できます。組織内で、認証ごとの使用量課金とライセンス モデルを混在させることはできません。認証ごとの Multi-Factor Authentication プロバイダーがディレクトリにリンクされている場合、組織は所有しているライセンスに関係なく、すべての Multi-Factor Authentication 確認要求に対して課金されます。

**Q: 組織が Azure Multi-Factor Authentication を使用するには、ID の使用と同期が必要ですか。**

組織で使用量ベースの課金モデルを使用する場合、Azure Active Directory は必要ありません。ディレクトリへの Multi-Factor Authentication プロバイダーのリンクはオプションです。組織がディレクトリにリンクされていない場合は、オンプレミスの Azure Multi-Factor Authentication Server または Azure Multi-factor Authentication SDK をデプロイできます。ライセンス モデルでは Azure Active Directory が必要です。これは、ライセンスが購入されてディレクトリ内のユーザーに割り当てられると、ライセンスがディレクトリに追加されるためです。


## 使いやすさ

**Q: ユーザーの携帯電話に応答が届かない場合、またはユーザーが携帯電話を利用できない場合はどうずればよいですか。**

ユーザーが予備の電話番号を構成している場合は、サインイン ページでプロンプトが表示されたら再試行し、その電話番号を選択してください。ユーザーが別の方法を構成していない場合は、組織の管理者に連絡し、ユーザーのメインの電話 (携帯または職場の電話) に割り当てられている番号の更新を依頼してください。


**Q: アクセスできなくなったアカウントについてユーザーから問い合わせがあった場合、管理者はどうすればよいですか。**

管理者は、ユーザーに登録プロセスを再度実行するように依頼することで、ユーザーのアカウントをリセットできます。詳細については、[クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](multi-factor-authentication-manage-users-and-devices.md)に関するページを参照してください。

**Q: アプリ パスワードが使用されている電話をユーザーが紛失した場合や盗難された場合は、管理者はどうすればよいでしょうか。**

管理者は、そのユーザーのすべてのアプリ パスワードを削除して、承認されていないアクセスを防ぐことできます。ユーザーは、代わりのデバイスを入手したら、パスワードを再作成できます。詳細については、[クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](multi-factor-authentication-manage-users-and-devices.md)に関するページを参照してください。

**Q: ユーザーが非ブラウザー アプリにサインインできない場合はどうすればよいでしょうか。**

- Multi-Factor Authentication を使用できるように構成されているユーザーが一部の非ブラウザー アプリにサインインするには、アプリ パスワードが必要になります。
- ユーザーは、サインイン情報のクリア (削除)、アプリの再起動、ユーザー名とアプリ パスワードを使用したサインインを行う必要があります。

アプリ パスワードの作成や[アプリ パスワードに関するヘルプ](multi-factor-authentication-end-user-app-passwords.md)についての詳細を確認してください。


>[AZURE.NOTE] Office 2013 クライアントのための最新の認証
>
> Office 2013 クライアント (Outlook など) では、新しい認証プロトコルがサポートされます。Multi-Factor Authentication をサポートするように Office 2013 を構成することができます。Office 2013 を構成すると、Office 2013 クライアントのアプリ パスワードが不要になります。詳しくは、「[Office 2013 modern authentication public preview announcement (Office 2013 の最新の認証のパブリック プレビューに関する発表)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」を参照してください。

**Q: テキスト メッセージを受信しない場合、または双方向のテキスト メッセージに返信したが確認がタイムアウトになる場合は、どうすればよいでしょうか。**

Azure Multi-factor Authentication サービスは、SMS アグリゲーターを使用してテキスト メッセージを送信します。使用されるアグリゲーター、宛先の国、ユーザーの携帯電話の事業者、信号強度など、多くの要因がテキスト メッセージの配信と受信の信頼性に影響を及ぼす可能性があります。そのため、双方向の SMS でテキスト メッセージの配信および SMS 応答の受信が保証されるわけではありません。可能な場合は、双方向の SMS ではなく、一方向の SMS を使用することをお勧めします。一方向の SMS は、信頼性が高く、別の国から送信されたテキスト メッセージに応答して高額な国際 SMS 料金をユーザーが課金されるのを防ぐことができます。

またテキスト メッセージによる確認は、米国やカナダなどの一部の国または地域ではより信頼性が高くなります。Azure Multi-Factor Authentication を使用して信頼性の高いテキスト メッセージを受信することが難しいユーザーは、代わりにモバイル アプリまたは電話による方法を選択することをお勧めします。モバイル アプリによる認証方法が優れているのは、ユーザーが携帯電話と Wi-fi 接続経由の両方でモバイル アプリの通知を受け取ることができるためです。さらに、デバイスがまったく信号を出さない場合でも、モバイル アプリ パスコードが表示されます。Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

**Azure Multi-Factor Authentication Server でハードウェア トークンを使用できますか。**

Azure Multi-Factor Authentication Server を使用している場合、サード パーティ製オープン認証 (OATH) の時間ベースのワンタイム パスワード (TOTP) トークをインポートし、Multi-Factor Authentication に使用します。Gemalto で独自のトークン用に生成できる古い Portable Symmetric Key Container (PSKC) 形式のサードパーティ製 OATH TOTP トークンのインポートがサポートされています。また、CSV 形式でのトークンのインポートもサポートされています。CSV 形式でトークンをインポートする場合は、CSV ファイルにシリアル番号、Base32 形式のシークレット キー、および時間間隔 (通常は 30 秒) を含める必要があります。

Azure Multi-Factor Authentication Server にインポートできる CSV ファイルにシークレット キー ファイルを配置できる場合は、OATH TOTP トークンである ActiveIdentity トークンを使用できます。OATH トークンと Active Directory フェデレーション サービス (ADFS)、リモート認証ダイヤルイン ユーザー サービス (RADIUS) (クライアント システムがアクセス チャレンジ応答を処理できる場合)、インターネット インフォメーション サーバー (IIS) のフォーム ベース認証を使用できます。

**Q: Azure Multi-Factor Authentication Server を使用してターミナル サービスをセキュリティで保護することはできますか。**

はい。ただし、Windows Server 2012 R2 以降のバージョンを使用している場合は、リモート デスクトップ ゲートウェイ (RD ゲートウェイ) を使用するときにだけ可能です。

Windows Server 2012 R2 におけるセキュリティの変更により、Azure Multi-Factor Authentication Server から、Windows Server 2012 以前のバージョンのローカル セキュリティ機関 (LSA) のセキュリティ パッケージに接続する方法が変更されました。Windows 2012 以前のターミナル サービスのバージョンでは、単に [Windows 認証でアプリケーションをセキュリティで保護する](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)ことだけが可能です。Windows Server 2012 R2 を使用している場合は、RD ゲートウェイが必要になります。

**Q: 発信者 ID を設定した後に、ユーザーが Multi-Factor Authentication 呼び出しを匿名の発信者から受けるのはなぜですか。**

公衆電話網経由で Multi-Factor Authentication の呼び出しが行われた場合、発信者 ID をサポートしていない通信事業者を通じて呼び出しがルーティングされることがあります。このため、Multi-Factor Authentication システムが常に発信者 ID を送信していても、発信者 ID は保証されません。


## エラー

**Q: モバイル アプリの通知を使用して認証を行うときに、"認証しようとしているアカウントはアクティブ化されていません" というエラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

次の手順に従います。

1. [Azure ポータル プロファイル](https://account.activedirectory.windowsazure.com/profile/)に移動して、組織のアカウントでサインインします。
2. 必要に応じて、**[Other verification options (その他の確認オプション)]** をクリックし、アカウント確認に使用する別のオプションをクリックします。
3. **[追加のセキュリティ確認]** をクリックします。
4. モバイル アプリから既存のアカウントを削除します。
5. **[構成]** をクリックし、モバイル アプリを再構成するための手順を実行します。




**Q:非ブラウザー アプリケーションを使用してサインインしようとしたときに、0x800434D4L エラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

現時点で、ユーザーは、ブラウザーからアクセスできるアプリケーションとサービスでのみ、追加のセキュリティ確認を使用することができます。Windows PowerShell など、ローカル コンピューターにインストールされている非ブラウザー アプリケーション ("リッチ クライアント アプリケーション" とも呼ばれます) は、追加のセキュリティ確認を必要とするアカウントでは機能しません。この場合、アプリケーションで 0x800434D4L エラーがユーザーに表示される可能性があります。

これを回避するには、管理関連の操作用と管理以外の操作用に異なるユーザー アカウントを使用します。非管理アカウントを使用して Outlook にサインインできるように、後ほど、管理アカウントと非管理アカウントのメールボックスにリンクを作成することができます。詳細については、「[管理者がユーザーのメールボックスの内容を開いたり表示したりできるようにする](http://help.outlook.com/141/gg709759.aspx?sl=1)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->