<properties 
	pageTitle="IIS 認証と Azure Multi-Factor Authentication Server" 
	description="IIS 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="billmath"/>

# IIS 認証

Azure Multi-Factor Authentication Server の [IIS 認証] セクションを使用して、IIS 認証を有効にし、Microsoft IIS Web アプリケーションと統合するように構成できます。Azure Multi-Factor Authentication Server は、Azure Multi-Factor Authentication に追加するために、IIS Web サーバーに対して行われる要求をフィルター処理できるプラグインをインストールします。IIS プラグインは、フォームベースの認証と統合 Windows HTTP 認証のサポートを提供します。さらに、信頼できる IP を構成して、内部 IP アドレスを 2 要素認証から除外できます。


![IIS 認証](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Azure Multi-Factor Authentication Server でフォームベースの IIS 認証を使用する

フォームベースの認証を使用する IIS Web アプリケーションを保護するには、IIS Web サーバーに Azure Multi-Factor Authentication Server をインストールし、次の手順で Server を構成します。

1. Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. [フォーム ベース] タブをクリックします。
3. [追加…] ボタンをクリックします。
4. ユーザー名変数、パスワード変数、およびドメイン変数を自動的に検出するには、ログイン URL (例: https://localhost/contoso/auth/login.aspx) を [フォームベースの Web サイトの自動構成] ダイアログ ボックスに入力し、[OK] をクリックします。
5. すべてのユーザーが Server にインポート済みである、またはインポート予定であり、多要素認証の対象となる場合は、[Multi-Factor Authentication のユーザー照合が必要] ボックスをオンにします。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。
6. ページ変数を自動的に検出できない場合、[フォーム ベースの Web サイトの自動構成] ダイアログ ボックスの[手動で指定...] をクリックします。
7. [フォームベースの Web サイトの追加] ダイアログ ボックスで、ログイン ページの URL を [送信 URL] フィールドに入力し、アプリケーション名 (省略可能) を入力します。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。[送信 URL] の詳細については、ヘルプ ファイルを参照してください。 
8. 適切な要求の形式を選択します。これは、ほとんどの Web アプリケーションでは、"POST または GET" に設定されます。
9. ユーザー名変数、パスワード変数、およびドメイン変数を入力します ([ログイン] ページに表示される場合)。場合によっては、Web ブラウザーでログイン ページに移動し、ページを右クリックし、[ソースの表示] を選択して、ページ内の入力ボックスの名前を確認する必要があります。
10. すべてのユーザーがサーバーにインポートされている、またはインポートされ、多要素認証を受ける場合、[Multi-Factor Authentication のユーザー照合が必要] ボックスにチェック マークを付けます。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。
11.  [詳細…] ボタンをクリックして詳細設定を確認します。詳細設定では、カスタム拒否ページ ファイルの選択、Web サイトに対して成功した認証の Cookie を使用した一定期間のキャッシュ、プライマリ資格情報の認証対象 (Windows ドメイン、LDAP ディレクトリ、または RADIUS サーバー) の選択などを実行できます。完了したら、[OK] ボタンをクリックして、[フォームベースの Web サイトの追加] ダイアログ ボックスに戻ります。詳細設定の詳細については、ヘルプ ファイルを参照してください。
12. [OK] ボタンをクリックします。
13. URL とページの変数が検出または入力されれば、Web サイトのデータがフォーム ベースのパネルに表示されます。
14. この後の「Azure Multi-Factor Authentication Server の IIS プラグインを有効にする」の項を参照して、IIS 認証の構成を完了してください。 

## 統合 Windows 認証と Azure Multi-Factor Authentication Server を使用する

統合 Windows HTTP 認証を使用する IIS Web アプリケーションを保護するには、IIS Web サーバーに Azure Multi-Factor Authentication Server をインストールし、次の手順で Server を構成します。

1. Azure Multi-factor Authentication Server 内で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. [HTTP] タブをクリックします。[フォーム ベース] タブをクリックします。
3. [追加…] ボタンをクリックします。
4. [基本 URL の追加] ダイアログ ボックスで、HTTP 認証が実行される Web サイトの URL (例: http://localhost/owa) を [基本 URL] フィールドに入力し、アプリケーション名 (省略可能) を入力します。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
5. 既定値では不十分な場合は、アイドル状態のタイムアウトと最大セッション時間を調整します。
6. すべてのユーザーが Server にインポート済みである、またはインポート予定であり、多要素認証の対象となる場合は、[Multi-Factor Authentication のユーザー照合が必要] ボックスをオンにします。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。 
7. 必要な場合は、[クッキーのキャッシュ] ボックスをオンにします。
8. [OK] ボタンをクリックします。
9. この後の「[Azure Multi-Factor Authentication Server の IIS プラグインを有効にする](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server)」の項を参照して、IIS 認証の構成を完了してください。 


## Azure Multi-Factor Authentication Server の IIS プラグインを有効にする

フォームベースまたは HTTP 認証 URL と設定を構成した後、Azure Multi-Factor Authentication の IIS プラグインを読み込んで IIS で有効になる場所を選択する必要があります。次の手順に従います。

1. IIS 6 で実行する場合は、[ISAPI] タブをクリックし、Web アプリケーションが実行される Web サイト (例: 既定の Web サイト) を選択し、そのサイトに対して Azure Multi-Factor Authentication の ISAPI フィルター プラグインを有効にします。
2. IIS 7 以降で実行する場合は、[ネイティブ モジュール] タブをクリックし、サーバー、Web サイト、またはアプリケーションを選択して、必要なレベルで IIS プラグインを有効にします。
3. 画面上部にある [IIS 認証を有効にする] ボックスをクリックします。これで、Azure Multi-Factor Authentication で、選択された IIS アプリケーションが保護されるようになりました。ユーザーが Server にインポートされていることを確認してください。内部 IP アドレスをホワイトリストに登録し、それらの場所から Web サイトにログインする際に2 要素認証を不要にするには、以下の「信頼される IP」のセクションを参照してください。 


## 信頼される IP

信頼される IP は、特定の IP アドレスまたはサブネットから発生する Web サイト要求に関して、ユーザーが Azure Multi-Factor Authentication をバイパスできるようにします。たとえば、社内からのログイン中は、Azure Multi-Factor Authentication からユーザーを除外することもできます。その場合、社内のサブネットを信頼される IP エントリとして指定します。信頼できる IP を構成するには、次の手順に従います。

1. [IIS 認証] セクションで、[信頼される IP] タブをクリックします。 
2. [追加…] ボタンをクリックします。
3. [信頼される IP の追加] ダイアログ ボックスが表示された場合、[単一 IP]、[IP の範囲] または [サブネット] ラジオ ボタンのいずれかを選択します。
4. ホワイト リストに登録する IP アドレス、IP アドレスまたはサブネットの範囲を入力します。サブネットを入力する場合は、適切なネットマスクを選択し、[OK] ボタンをクリックします。これでホワイト リストが追加されました。

<!---HONumber=August15_HO6-->