<properties 
	pageTitle="Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護" 
	description="Azure MFA および AD FS 2.0 を開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>
# Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護

お客様の組織が Azure Active Directory とフェデレーションされており、オンプレミスまたはクラウドにセキュリティ保護したいリソースがある場合、Azure Multi-Factor Authentication Sever を使用して、AD FS と連動するよう構成し、価値の高いエンド ポイントで多要素認証がトリガーされるようにすることによってセキュリティ保護を行えます。

このドキュメントでは、Azure Multi-Factor Authentication Server と AD FS 2.0 の使用について説明します。Azure Multi-Factor Authentication と Windows Serve 2012 R2 AD FS の使用の詳細については、「[Azure Multi-Factor Authentication Server と Windows Server 2012 R2 AD FS を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-w2k12.md)」を参照してください。


## AD FS 2.0 プロキシ
プロキシで AD FS 2.0 を保護するには、次の手順に従って ADFS プロキシ サーバーに Azure Multi-factor Authentication Server をインストールし、サーバーを構成します。

### プロキシで AD FS 2.0 を保護するには
<ol>
<li>Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。</li>
<li>[フォーム ベース] タブをクリックします。</li>
<li>[追加…] ボタンをクリックします。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>ユーザー名、パスワード、およびドメインの変数を自動的に検出するには、[フォーム ベースの Web サイトの自動構成] ダイアログ ボックス内でログイン URL (例: https://sso.contoso.com/adfs/ls) を入力し、[OK] をクリックします。</li>
<li>すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Azure Multi-Factor Authentication ユーザーの一致が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。</li>
<li>ページ変数を自動的に検出できない場合、[フォーム ベースの Web サイトの自動構成] ダイアログ ボックスの[手動で指定...] をクリックします。</li>
<li>[フォーム ベースの Web サイトの追加] ダイアログ ボックスの [送信 URL] フィールドに、ADFS ログイン ページへの URL (例: https://sso.contoso.com/adfs/ls) を入力し、アプリケーション名を入力します (省略可能)。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。[送信 URL] の詳細については、ヘルプ ファイルを参照してください。</li>
<li>[要求の形式] を “POST または GET” に設定します。</li>
<li>ユーザー名変数 (ctl00 $contentplaceholder1 $usernametextbox) とパスワード変数 (ctl00 $contentplaceholder1 $passwordtextbox) を入力します。フォーム ベースのログイン ページにドメイン テキスト ボックスが表示される場合、[ドメイン変数] も入力します。Web ブラウザーでログイン ページに移動し、ページを右クリックし、[ソースの表示] を選択し、ログイン ページ内の入力ボックスの名前を検索します。</li>
<li>すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Azure Multi-Factor Authentication ユーザーの一致が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>[詳細…] ボタンをクリックして、詳細設定 (カスタム拒否ページ ファイルを選択する機能を含む) を確認し、cookie を使用して Web サイトに対する成功認証を一定期間キャッシュに入れたり、1 次資格情報を認証する方法を選択したりします。</li>
<li>ADFS プロキシ サーバーはドメインに参加しない可能性があるため、ユーザー インポートや事前認証のためのドメイン コント ローラーへの接続に LDAP を使用する可能性があります。[拡張フォーム ベースの Web サイト] ダイアログ ボックスで、[1 次認証] タブをクリックし、事前認証の種類に “LDAP バインド” を選択します。</li>
<li>完了したら、[OK] ボタンをクリックして、[フォーム ベースの Web サイトの追加] ダイアログ ボックスに戻ります。詳細設定の詳細については、ヘルプ ファイルを参照してください。</li>
<li>[OK] ボタンをクリックし、ダイアログ ボックスを閉じます。</li>
<li>URL とページの変数が検出または入力されれば、Web サイトのデータがフォーム ベースのパネルに表示されます。</li>
<li>[ネイティブ モジュール] タブをクリックしてサーバー、ADFS プロキシが実行されている Web サイト (例:“既定の Web サイト”)、または ADFS プロキシ アプリケーション (例: “adfs” の下の “ls”) を選択し、希望のレベルで IIS プラグインを有効にします。</li>
<li>画面上部にある [IIS 認証を有効にする] ボックスをクリックします。</li>
<li>IIS 認証が有効になりました。ただし、LDAP を経由して Active Directory (AD) に事前認証を実行するには、ドメイン コントローラーへの LDAP 接続を構成する必要があります。これを行うには、[ディレクトリ統合] アイコンをクリックします。</li>
<li>[設定] タブで、[特定の LDAP 構成の使用] ラジオ ボタンを選択します。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>[編集…] ボタンをクリックします。</li>
<li>[LDAP 構成の編集] ダイアログ ボックスで、AD ドメイン コントローラーへの接続に必要な情報をフィールドに入力します。フィールドの説明を、次の表に示します。注: この情報は、Azure Multi-factor Authentication Server のヘルプ ファイルにも含まれています。</li>

フィールド| 説明
:------------- | :-------------
サーバー|LDAP ディレクトリを実行するサーバーのホスト名または IP アドレスを入力します。バックアップ サーバーは、セミコロンで区切って指定することも可能です。<br> **注:** バインドの種類が SSL の場合、通常、完全修飾ホスト名が必須であり、LDAP ディレクトリ サーバー上にインストールされる SSL 証明書の名前と一致する必要があります。 
ベース DN|すべてのディレクトリ クエリの開始元となるベース ディレクトリ オブジェクトの識別名を入力します。例えば、dc=contoso、dc=com です。
バインドの種類|LDAP ディレクトリの検索にバインドする際に使用する適切なバインドの種類を選択します。これは、インポート、同期、およびユーザー名の解決で使用します。<ul><li>* * 匿名 * *-匿名のバインドが実行されます。バインド DN とバインド パスワードは使用されません。これは、LDAP ディレクトリが匿名バインディングを許可し、かつアクセス許可が適切なレコードと属性のクエリを許可する場合にのみ機能します。</li><li>**シンプル** ‐ バインド DN とバインド パスワードは、LDAP ディレクトリにバインドするためのプレーン テキストとして渡されます。これは、サーバーに到達可能か、およびバインド アカウントに適切なアクセス権があるかを検証するテスト目的でのみ使用してください。適切な証明書がインストールされた後、代わりに SSL を使用することをお勧めします。</li><li>**SSL** ‐ バインド DN およびバインド パスワードは、SSL を使用して暗号化され、LDAP ディレクトリにバインドます。この場合、Azure Multi-Factor Authentication Server が信頼する LDAP ディレクトリ サーバーに証明書がインストールされている必要があります。</li><li>**Windows** ‐ バインド ユーザー名とバインド パスワードは、Active Directory ドメイン コントローラーまたは ADAM ディレクトリに安全に接続するために使用されます。バインド ユーザー名がブランクのままになっている場合、ログオンしたユーザーのアカウントがバインドに使用されます。</li></ul> 
バインド ユーザ名|LDAP ディレクトリへのバインド時に使用するアカウントのユーザー レコードの識別名を入力します。バインドの識別名は、バインドの種類がシンプルまたは SSL 場合にのみ使用します。    
バインド パスワード|LDAP ディレクトリへのバインドに使用されるバインド DN またはユーザー名のバインド パスワードを入力します。Multi-Factor Auth Server AdSync Service のパスワードを構成するには、同期を有効にし、ローカル コンピューターでサービスを実行しておく必要があります。Azure Multi-Factor Authentication Server AdSync Service を実行しているアカウントの下の [Windows 保管ユーザー名とパスワード] にパスワードが保存されます。パスワードは、Multi-Factor Auth Server ユーザー インターフェースを実行しているアカウント、およびAzure Multi-Factor Authentication Server Service を実行しているアカウントの下にも保存されます。注: パスワードはローカルサーバーの [Windows 保管ユーザー名とパスワード] だけに保管されるため、この手順は、パスワードへのアクセスが必要な各 Multi-Factor Auth Server で行う必要があります。 
クエリ サイズの上限|ディレクトリ検索で返されるユーザーの最大数のサイズ制限を指定します。この制限は、LDAP ディレクトリの構成と一致する必要があります。ページングがサポートされない大規模な検索では、インポートと同期は、バッチでユーザーの取得を試みます。ここで指定されるサイズ制限が、LDAP ディレクトリで構成された制限より大きい場合、一部のユーザーが欠落する可能性があります。 



<li>[テスト] ボタンをクリックして、LDAP 接続をテストします。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>LDAP 接続テストが成功した場合、[OK] ボタンをクリックします。</li>
<li>次に、[会社の設定] アイコンをクリックし、[ユーザー名の解決] タブを選択します。</li>
<li>[ユーザー名と一致させるために LDAP の一意の ID 属性を使用する] ラジオ ボタンを選択します。</li>
<li>ユーザーがユーザー名を ADFS プロキシログイン フォームに “domain\username” 形式で入力する場合、サーバーは、LDAP クエリの作成時にユーザー名からドメイン部分を除去できる必要があります。これはレジストリの設定から行えます。</li>
<li>レジストリ エディタを開き、64 ビット サーバーの HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor に移動します。32 ビット サーバーの場合、パスから “Wow6432Node” を取ってください。「Usernamecxz_stripprefixdomain」という名前の新しい DWORD レジストリ キーを作成し、値 1 に設定します。Azure Multi-factor Authentication は、ad FS プロキシをセキュリティ保護するようになりました。ユーザーが Active Directory からサーバーにインポートされたことを確認します。内部 IP アドレスをホワイトリストに登録し、それらの場所から Web サイトにログインする際に2 要素認証を不要にするには、以下の「信頼される IP」のセクションを参照してください。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## プロキシを持たない AD FS 2.0 Direct

AD FS プロキシが使用されないときに AD FS 2.0 を保護するには、次の手順に従って ADFS サーバーに Azure Multi-factor Authentication Server をインストールし、サーバーを構成します。

### プロキシなしで AD FS 2.0 を保護するには
<ol>
<li>Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。</li>
<li>[HTTP] タブをクリックします。</li>
<li>[追加…] ボタンをクリックします。</li>
<li>[基本 URL の追加] ダイアログ ボックスで、HTTP 認証が実行される ADFS Web サイトの URL (例: https://sso.domain.com/adfs/ls/auth/integrated) を [基本 URL] フィールドに入力し、アプリケーション名を入力します (省略可能)。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。</li>
<li>必要な場合、アイドル状態のタイムアウトと最大セッション時間を調整します。</li>
<li>すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Azure Multi-Factor Authentication ユーザーの一致が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。</li>
<li>必要な場合は、[クッキーのキャッシュ] ボックスをオンにします。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>[OK] ボタンをクリックします。</li>
<li>[ネイティブ モジュール] タブをクリックしてサーバー、ADFS が実行されている Web サイト、(例:“既定の Web サイト”) または ADFS アプリケーション (例: “adfs” の下の “ls”) を選択し、希望のレベルで IIS プラグインを有効にします。</li>
<li>画面上部にある [IIS 認証を有効にする] ボックスをクリックします。Azure Multi-factor Authentication は、ADFS をセキュリティ保護するようになりました。ユーザーが Active Directory からサーバーにインポートされたことを確認します。内部 IP アドレスをホワイトリストに登録し、それらの場所から Web サイトにログインする際に2 要素認証を不要にするには、以下の「信頼される IP」のセクションを参照してください。</li>

## 信頼される IP
信頼される IP は、特定の IP アドレスまたはサブネットから発生する Web サイト要求に関して、ユーザーが Azure Multi-Factor Authentication をバイパスできるようにします。たとえば、社内からのログイン中は、Azure Multi-Factor Authentication からユーザーを除外することもできます。その場合、社内のサブネットを信頼される IP エントリとして指定します。

### 信頼される IP を構成するには

<ol>
<li>[IIS 認証] セクションで、[信頼される IP] タブをクリックします。</li>
<li>[追加…] ボタンをクリックします。</li>
<li>[信頼される IP の追加] ダイアログ ボックスが表示された場合、[単一 IP]、[IP の範囲] または [サブネット] ラジオ ボタンのいずれかを選択します。</li>
<li>ホワイト リストに登録する IP アドレス、IP アドレスの範囲、またはサブネットを入力します。サブネットを入力する場合は、適切なネットマスクを選択し、[OK] ボタンをクリックします。これでホワイト リストが追加されました。</li>


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->