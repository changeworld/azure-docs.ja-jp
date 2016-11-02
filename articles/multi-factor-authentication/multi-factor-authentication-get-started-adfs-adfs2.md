<properties
	pageTitle="Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護"
	description="Azure MFA および AD FS 2.0 を開始する方法について説明する Azure Multi-Factor Authentication のページです。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>
# Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護

お客様の組織が Azure Active Directory とフェデレーションされており、オンプレミスまたはクラウドにセキュリティ保護したいリソースがある場合、Azure Multi-Factor Authentication Sever を使用して、AD FS と連動するよう構成し、価値の高いエンド ポイントで多要素認証がトリガーされるようにすることによってセキュリティ保護を行えます。

このドキュメントでは、Azure Multi-Factor Authentication Server と AD FS 2.0 の使用について説明します。Azure Multi-Factor Authentication と Windows Serve 2012 R2 AD FS の使用の詳細については、「[Azure Multi-Factor Authentication Server と Windows Server 2012 R2 AD FS を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-w2k12.md)」を参照してください。


## AD FS 2.0 プロキシ
プロキシで AD FS 2.0 を保護するには、次の手順に従って ADFS プロキシ サーバーに Azure Multi-factor Authentication Server をインストールし、サーバーを構成します。

### プロキシで AD FS 2.0 を保護するには

1. Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. [フォーム ベース] タブをクリックします。
3. [追加…] ボタンをクリックします。
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. ユーザー名変数、パスワード変数、およびドメイン変数を自動的に検出するには、ログイン URL (例: https://sso.contoso.com/adfs/ls) を [フォームベースの Web サイトの自動構成] ダイアログ ボックスに入力し、[OK] をクリックします。
5. すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Multi-Factor Authentication のユーザー照合が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。
6. ページ変数を自動的に検出できない場合、[フォーム ベースの Web サイトの自動構成] ダイアログ ボックスの[手動で指定...] をクリックします。
7. [フォームベースの Web サイトの追加] ダイアログ ボックスで、ADFS ログイン ページの URL を [送信 URL] フィールドに入力し (例: https://sso.contoso.com/adfs/ls)、(省略可能) アプリケーション名を入力します。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。[送信 URL] の詳細については、ヘルプ ファイルを参照してください。
8. [要求の形式] を “POST または GET” に設定します。
9. ユーザー名変数 (ctl00 $contentplaceholder1 $usernametextbox) とパスワード変数 (ctl00 $contentplaceholder1 $passwordtextbox) を入力します。フォーム ベースのログイン ページにドメイン テキスト ボックスが表示される場合、[ドメイン変数] も入力します。Web ブラウザーでログイン ページに移動し、ページを右クリックし、[ソースの表示] を選択し、ログイン ページ内の入力ボックスの名前を検索します。
10. すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Multi-Factor Authentication のユーザー照合が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. [詳細…] ボタンをクリックして、詳細設定 (カスタム拒否ページ ファイルを選択する機能を含む) を確認し、cookie を使用して Web サイトに対する成功認証を一定期間キャッシュに入れたり、1 次資格情報を認証する方法を選択したりします。
12. ADFS プロキシ サーバーはドメインに参加しない可能性があるため、ユーザー インポートや事前認証のためのドメイン コント ローラーへの接続に LDAP を使用する可能性があります。[拡張フォーム ベースの Web サイト] ダイアログ ボックスで、[1 次認証] タブをクリックし、事前認証の種類に “LDAP バインド” を選択します。
13. 完了したら、[OK] ボタンをクリックして、[フォーム ベースの Web サイトの追加] ダイアログ ボックスに戻ります。詳細設定の詳細については、ヘルプ ファイルを参照してください。
14. [OK] ボタンをクリックし、ダイアログ ボックスを閉じます。
15. URL とページの変数が検出または入力されれば、Web サイトのデータがフォーム ベースのパネルに表示されます。
16. [ネイティブ モジュール] タブをクリックし、サーバー、ADFS プロキシが実行されている Web サイト (例: "既定の Web サイト")、または ADFS プロキシ アプリケーション (例: "adfs" の下の "ls") を選択して、必要なレベルで IIS プラグインを有効にします。
17. 画面上部にある [IIS 認証を有効にする] ボックスをクリックします。
18. IIS 認証が有効になりました。ただし、LDAP を経由して Active Directory (AD) に事前認証を実行するには、ドメイン コントローラーへの LDAP 接続を構成する必要があります。これを行うには、[ディレクトリ統合] アイコンをクリックします。
19. [設定] タブで、[特定の LDAP 構成の使用] ラジオ ボタンを選択します。
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. [編集…] ボタンをクリックします。
21. [LDAP 構成の編集] ダイアログ ボックスで、AD ドメイン コントローラーへの接続に必要な情報をフィールドに入力します。フィールドの説明を、次の表に示します。注: この情報は、Azure Multi-factor Authentication Server のヘルプ ファイルにも含まれています。
22. [テスト] ボタンをクリックして、LDAP 接続をテストします。
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. LDAP 接続テストが成功した場合、[OK] ボタンをクリックします。
24. 次に、[会社の設定] アイコンをクリックし、[ユーザー名の解決] タブを選択します。
25. [ユーザー名と一致させるために LDAP の一意の ID 属性を使用する] ラジオ ボタンを選択します。
26. ユーザーがユーザー名を ADFS プロキシログイン フォームに “domain\\username” 形式で入力する場合、サーバーは、LDAP クエリの作成時にユーザー名からドメイン部分を除去できる必要があります。これはレジストリの設定から行えます。
27. レジストリ エディタを開き、64 ビット サーバーの HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor に移動します。32 ビット サーバーの場合、パスから “Wow6432Node” を取ってください。「Usernamecxz\_stripprefixdomain」という名前の新しい DWORD レジストリ キーを作成し、値 1 に設定します。Azure Multi-factor Authentication は、ad FS プロキシをセキュリティ保護するようになりました。ユーザーが Active Directory からサーバーにインポートされたことを確認します。内部 IP アドレスをホワイトリストに登録し、それらの場所から Web サイトにログインする際に2 要素認証を不要にするには、以下の「信頼される IP」のセクションを参照してください。

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## プロキシを持たない AD FS 2.0 Direct

AD FS プロキシが使用されないときに AD FS 2.0 を保護するには、次の手順に従って ADFS サーバーに Azure Multi-factor Authentication Server をインストールし、サーバーを構成します。

### プロキシなしで AD FS 2.0 を保護するには
1. Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. [HTTP] タブをクリックします。
3. [追加…] ボタンをクリックします。
4. [基本 URL の追加] ダイアログ ボックスで、HTTP 認証が実行される ADFS Web サイトの URL (例: https://sso.domain.com/adfs/ls/auth/integrated) を [基本 URL] フィールドに入力し、(省略可能) アプリケーション名を入力します。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
5. 必要な場合、アイドル状態のタイムアウトと最大セッション時間を調整します。
6. すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Multi-Factor Authentication のユーザー照合が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。
7. 必要な場合は、[クッキーのキャッシュ] ボックスをオンにします。
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. [OK] ボタンをクリックします。
9. [ネイティブ モジュール] タブをクリックし、サーバー、ADFS が実行されている Web サイト (例: "既定の Web サイト")、または ADFS アプリケーション (例: "adfs" の下の "ls") を選択して、必要なレベルで IIS プラグインを有効にします。
10. 画面上部にある [IIS 認証を有効にする] ボックスをクリックします。Azure Multi-factor Authentication は、ADFS をセキュリティ保護するようになりました。ユーザーが Active Directory からサーバーにインポートされたことを確認します。内部 IP アドレスをホワイトリストに登録し、それらの場所から Web サイトにログインする際に2 要素認証を不要にするには、以下の「信頼される IP」のセクションを参照してください。


## 信頼できる IP
信頼される IP は、特定の IP アドレスまたはサブネットから発生する Web サイト要求に関して、ユーザーが Azure Multi-Factor Authentication をバイパスできるようにします。たとえば、社内からのログイン中は、Azure Multi-Factor Authentication からユーザーを除外することもできます。その場合、社内のサブネットを信頼される IP エントリとして指定します。

### 信頼される IP を構成するには


1. [IIS 認証] セクションで、[信頼される IP] タブをクリックします。
1. [追加…] ボタンをクリックします。
1. [信頼される IP の追加] ダイアログ ボックスが表示された場合、[単一 IP]、[IP の範囲] または [サブネット] ラジオ ボタンのいずれかを選択します。
1. ホワイト リストに登録する IP アドレス、IP アドレスの範囲、またはサブネットを入力します。サブネットを入力する場合は、適切なネットマスクを選択し、[OK] ボタンをクリックします。信頼される IP が追加されました。


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=AcomDC_0921_2016-->