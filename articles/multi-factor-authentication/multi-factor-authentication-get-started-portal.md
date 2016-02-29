<properties 
	pageTitle="Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ" 
	description="Azure MFA およびユーザー ポータルを開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
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
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ

ユーザー ポータルにより、管理者は、Azure Multi-factor Authentication ユーザー ポータルをインストールおよび構成することができます。ユーザー ポータルは、ユーザーが Azure Multi-Factor Authentication に登録を行い、アカウントを保守するための IIS Web サイトです。ユーザーは、次のサインオン時に、電話番号の変更、PIN の変更、または Azure Multi-factor Authentication のバイパスを行うことができます。

ユーザーは、通常のユーザー名とパスワードを使用してユーザー ポータルにログインします。Azure Multi-Factor Authentication 呼び出しを完了するかセキュリティーに関する質問に答えて、その認証を完了します。ユーザー登録が許可されている場合、ユーザー ポータルに最初にログインする際にユーザーは電話番号と PIN を構成します。

ユーザー ポータルの管理者をセットアップして、新しいユーザーを追加する権限と既存のユーザーを更新する権限を付与することができます。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Azure Multi-Factor Authentication Server と同じサーバーでのユーザー ポータルのデプロイ

Azure Multi-factor Authentication Server と同じサーバーにユーザー ポータルをインストールするには、次の前提条件が必要です。

- asp.net および IIS 6 メタ ベース互換性を含め、IIS をインストールする必要があります(IIS 7 以降の場合)。 
- ログインしているユーザーに、コンピューターとドメイン (該当する場合) に対する管理者権限が必要です。このアカウントには Active Directory セキュリティ グループを作成するためのアクセス許可が必要だからです。

### Azure Multi-Factor Authentication Server のユーザー ポータルをデプロイするには

1. Azure Multi-Factor Authentication Server 内で: 左側のメニューの [ユーザー ポータル] アイコンをクリックし、[ユーザー ポータルのインストール] ボタンをクリックします。 
1. [次へ] をクリックします。
1. [次へ] をクリックします。
1. コンピューターがドメインに参加しており、ユーザー ポータルと Azure Multi-Factor Authentication サービスの間の通信をセキュア保護するための Active Directory 構成が完了していない場合、Active Directory の手順が表示されます。[次へ] ボタンをクリックし、この構成を自動的に完了します。
1. [次へ] をクリックします。
1. [次へ] をクリックします。
1. [閉じる] をクリックします。
1. 任意のコンピューターから Web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (例: https://www.publicwebsite.com/MultiFactorAuth)。証明書の警告およびエラーが表示されないことを確認してください。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## 別のサーバーへの Azure Multi-Factor Authentication Server ユーザー ポータルのデプロイ

Azure Multi-Factor Authentication アプリを使用するには、アプリがユーザー ポータルと正常に通信できるよう以下のことが必要です。

ハードウェアおよびソフトウェアの要件については、「ハードウェアとソフトウェアの要件」を参照してください。

- Azure Multi-Factor Authentication Server の V6.0 以降を使用している必要があります。
- ユーザー ポータルが、Microsoft® Internet Information Services (IIS) 6.x、IIS 7.x 以降を実行している、インターネットに接続された Web サーバーにインストールされている必要があります。
- IIS 6.x を使用している場合は、ASP.NET v2.0.50727 がインストールおよび登録され、[許可] に設定します。
- IIS 7.x 以降を使用するときに必要な役割サービスには、ASP.NET および IIS 6 メタベース互換が含まれます。
- ユーザー ポータルは、SSL 証明書で保護する必要があります。
- Azure Multi-Factor Authentication Web サービス SDK が、Azure Multi-Factor Authentication Server がインストールされているサーバー上の IIS 6.x または IIS 7.x 以降にインストールされている必要があります
- Azure Multi-Factor Authentication Web サービス SDK は、SSL 証明書で保護されている必要があります。
- ユーザー ポータルは SSL 経由で Azure Multi-Factor Authentication Web サービス SDK に接続できる必要があります
- ユーザー ポータルは、「PhoneFactor Admins」と呼ばれるセキュリティ グループのメンバーであるサービス アカウントの資格情報を使用して Azure Multi-Factor Authentication Web サービス SDK で認証できる必要があります。このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加しているサーバーで実行されている場合は、Active Directory に存在します。このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加していない場合、Azure Multi-Factor Authentication Server のローカルに存在します。

Azure Multi-Factor Authentication Server 以外のサーバーでユーザー ポータルをインストールするには、次の 3 つの手順が必要です。

1. Web サービス SDK のインストール
2. ユーザー ポータルのインストール
3. Azure Multi-Factor Authentication Server のユーザー ポータル設定の構成


### Web サービス SDK のインストール

Azure Multi-Factor Authentication Server に Azure Multi-Factor Authentication Web サービス SDK がまだインストールされていない場合、そのサーバーに移動し、Azure Multi-Factor Authentication Server を開きます。[Web サービス SDK] アイコン、[Web サービス SDK のインストール...] ボタンを順にクリックし、表示された指示に従います。Web サービス SDK は、SSL 証明書で保護されている必要があります。この目的で自己署名証明書を使用できますが、ユーザー ポータル Web サーバー上のローカル コンピューター アカウントの「信頼されたルート証明機関」ストアにインポートすることで、SSL 接続を開始するときにこの証明書が信頼されます。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### ユーザー ポータルのインストール

別のサーバーにユーザー ポータルをインストールする前に、以下の点に留意してください。

- インターネットに接続された Web サーバーで Web ブラウザーを開き、web.config ファイルに入力された Web サービス SDK の URL に移動することをお勧めします。ブラウザーが Web サービスを正常に取得できる場合、資格情報が求められます。web.config ファイルに入力したユーザー名とパスワードを、ファイルに表示されているとおりに入力します。証明書の警告およびエラーが表示されないことを確認してください。
- リバース プロキシまたはファイアウォールがユーザー ポータル Web サーバーの前面に配置され SSL オフロードを実行している場合、ユーザー ポータルの web.config ファイルを編集し、以下のキーを <appSettings> セクションに追加することで、ユーザー ポータルで https ではなく http を使用できるようにします。<add key="SSL_REQUIRED" value="false"/>

#### ユーザー ポータルをインストールするには

1. Azure Multi-Factor Authentication Server サーバーで Windows エクスプ ローラーを開き、Azure Multi-Factor Authentication Server がインストールされているフォルダーに移動します (C:\\Program Files\\Multi-Factor Authentication Server など)。ユーザー ポータルがインストールされるサーバーに応じて、MultiFactorAuthenticationUserPortalSetup インストール ファイルの 32 ビットまたは 64 ビット バージョンを選択します。インターネットに接続されたサーバーにインストール ファイルをコピーします。
2. インターネットに接続された Web サーバーで、セットアップ ファイルを管理者権限で実行する必要があります。これを最も簡単に行うには、管理者としてコマンド プロンプトを開き、インストール ファイルがコピーされた場所に移動します。
3. MultiFactorAuthenticationUserPortalSetup64 のインストール ファイルを実行し、必要に応じて、サイトと仮想ディレクトリの名前を変更します。
4. ユーザー ポータルのインストールを完了したら、C:\\inetpub\\wwwroot\\MultiFactorAuth (または仮想ディレクトリ名に基づく適切なディレクトリ) に移動し、web.config ファイルを編集します。
5. USE\_WEB\_SERVICE\_SDK キーを見つけて、値を false から true に変更します。WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME および WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD キーを見つけ、PhoneFactor Admins セキュリティ グループのメンバーであるサービス アカウントのユーザー名およびパスワードに値を設定します (上記の「要件」セクションを参照)。行末尾 (value=””/>) の引用符の間にユーザー名およびパスワードを入力するようにしてください。修飾されたユーザー名 (domain\\username や machine\\username など) を使用することをお勧めします。
6. pfup\_pfwssdk\_PfWsSdk 設定を見つけ、値を "http://localhost:4898/PfWsSdk.asmx" から Azure Multi-Factor Authentication Server で実行されている Web Service SDK の URL に変更します (例: https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx))。この接続では SSL が使用されているため、IP アドレスではなくサーバー名で Web サービス SDK を参照する必要があります。これは、SSL 証明書がサーバー名に対して発行され、使用される URL が証明書の名前に一致する必要があるためです。サーバー名がインターネットに接続されているサーバーで IP アドレスに解決されない場合、そのサーバーの hosts ファイルにエントリを追加し、Azure Multi-Factor Authentication Server の名前を IP アドレスにマッピングします。変更を行ったら、web.config ファイルを保存します。
7. 既定の Web サイトの直下などにユーザー ポータルがインストールされた Web サイトがまだ公的署名証明書にバインドされていない場合、証明書がまだインストールされていないならサーバーにインストールし、IIS マネージャーを開き証明書を Web サイトにバインドします。
8. 任意のコンピューターから Web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (例: https://www.publicwebsite.com/MultiFactorAuth)。証明書の警告およびエラーが表示されないことを確認してください。



## Azure Multi-Factor Authentication Server のユーザー ポータル設定の構成
ポータルがインストールされたため、Azure Multi-Factor Authentication Server を構成してポータルと連携させる必要があります。

Azure Multi-Factor Authentication Server にはユーザー ポータル用のさまざまなオプションがあります。次の表に、これらのオプションとそれぞれの使用目的を示します。

ユーザー ポータル設定|説明|
:------------- | :------------- | 
ユーザー ポータル URL| ポータルがホストされる場所の URL を入力できます。
プライマリ認証| ポータルにサインインするときに使用する認証の種類を指定できます。Windows 認証、Radius 認証または LDAP 認証のいずれかです。
ユーザーにログインを許可する|ユーザーが、ユーザー ポータルのサインイン ページにユーザー名とパスワードを入力できるようにします。これが選択されていない場合、ボックスは淡色表示されます。
ユーザー登録を許可する|ユーザーを電話番号などの追加情報の入力を求める設定画面に移動して、多要素認証に登録できるようにします。予備の電話番号には、補助的な電話番号を指定できます。サード パーティの OATH トークンには、サード パーティ の OATH トークンを指定できます。
ワンタイム バイパスの開始をユーザーに許可する| ワンタイム バイパスの開始をユーザーに許可できます。ユーザーがこの設定を有効にした場合は、ユーザーの次回のサインインで有効になります。プロンプトのバイパス秒を指定するボックスが表示され、ユーザーは既定値である 300 秒を変更できます。変更しなかった場合、ワンタイム バイパスは 300 秒間のみ有効です。
ユーザーに認証方法の選択を許可する| ユーザーが主要な連絡方法を指定できるようにします。電話、テキスト メッセージ、モバイル アプリ、または OATH トークンを指定できます。
ユーザーに言語の選択を許可する| 電話、テキスト メッセージ、モバイル アプリ、または OATH トークンで使用される言語をユーザーが変更できるようにします。
ユーザーにモバイル アプリのアクティブ化を許可する| サーバーで使用されるモバイル アプリのアクティブ化プロセスを完了するためのアクティブ化コードをユーザーが生成できるようにします。アクティブ化できるデバイスの数を設定することもできます (1 ～ 10 の範囲)。
代替認証にセキュリティの質問を使用する|多要素認証が失敗した場合に、セキュリティの質問を使用できます。正解する必要があるセキュリティの質問の数を指定できます。
ユーザーにサード パーティ製の OATH トークンの関連付けを許可する| ユーザーがサード パーティの OATH トークンを指定できるようにします。
代替認証に OATH トークンを使用する|多要素認証が失敗した場合に、OATH トークンを使用できます。セッションのタイムアウトを分単位で指定することもできます。
ログの有効化|ユーザー ポータルでログを有効にします。ログ ファイルは C:\\Program Files\\Multi-Factor Authentication Server\\Logs に配置されます。

これらの設定の大部分は、有効になったユーザーがユーザー ポータルにサインインするときに表示されます。

![ユーザー ポータル設定](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Azure Multi-Factor Authentication Server のユーザー ポータル設定を構成するには




1. Azure Multi-Factor Authentication Server で [ユーザー ポータル] アイコンをクリックします。[設定] タブで、ユーザー ポータルへの URL を [ユーザー ポータルの URL] テキスト ボックスに入力します。電子メール機能が有効な場合、この URL は、Azure Multi-Factor Authentication Server にインポートされるときにユーザーに送信される電子メールに挿入されます。
2. ユーザー ポータルで使用する設定を選択します。たとえば、ユーザーによる認証方法の制御を許可する場合は、ユーザーが選択できる方法と [ユーザーに認証方法の選択を許可する] にチェックマークを付けます。
3. 表示される設定について理解するには、ヘルプの右上隅の [ヘルプ] リンクをクリックします。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## [管理者] タブ
このタブでは、管理者特権を持つユーザーを追加できます。管理者を追加するときに、管理者が受け取るアクセス許可を微調整できます。これにより、管理者に対して必要な権限のみを確実に付与できます。[追加] ボタンをクリックした後、ユーザーとアクセス許可を選択し、[追加] をクリックします。

![ユーザー ポータル管理者](./media/multi-factor-authentication-get-started-portal/admin.png)


## セキュリティの質問
このタブでは、[代替認証にセキュリティの質問を使用する] オプションが選択されている場合にユーザーが回答する必要があるセキュリティの質問を指定できます。Azure Multi-Factor Authenticaton Server には、既定の質問が用意されています。質問の順序を変更したり、独自の質問を追加したりできます。独自の質問を追加する場合は、質問を表示するために使用する言語も指定できます。

![ユーザー ポータルのセキュリティの質問](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## 渡されたセッション

## SAML
SAML を使用する ID プロバイダーからの要求を受け入れるようにユーザー ポータルを設定できます。タイムアウト セッション、検証証明書、およびログアウト リダイレクト URL を指定できます。

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## 信頼される IP
このタブでは、1 つの IP アドレスまたは IP アドレス範囲を指定し、指定したいずれかの IP アドレスからユーザーがサインインした場合に多要素認証がバイパスされるように設定できます。

![ユーザー ポータルの信頼できる IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## セルフサービス ユーザー登録
ユーザーにサインインと登録を許可するには、[ユーザーにログインを許可する] オプションと [ユーザー登録を許可する] オプションを選択する必要があります。選択した設定は、ユーザーのサインイン エクスペリエンスに影響することに注意してください。

たとえば、ユーザーがユーザー ポータルにログインし、[ログイン] ボタンをクリックすると、[Multi-Factor Authentication ユーザー設定] ページが表示されます。Azure Multi-factor Authentication の構成方法によっては、ユーザーは自分の認証方法を選択できる場合があります。

ユーザーが認証方法として音声通話を選択した場合、またはその方法を使用するように事前に構成されている場合は、ページに代表電話番号と内線番号 (該当する場合) を入力します。予備の電話番号を入力できる場合もあります。

![ユーザー ポータルの信頼できる IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。電話番号と PIN (該当する場合) を入力した後、ユーザーは、[今すぐ電話で認証] ボタンをクリックします。Azure Multi-factor Authentication が、ユーザーの代表電話番号に対して電話による認証を実行します。ユーザーは、自己登録プロセスの次の手順に進むには、電話に応答し、PINを入力する (該当する場合) 必要があります。

ユーザーが認証方法として SMS テキストを選択した場合、またはその方法を使用するように事前に構成されている場合は、ページに自分の携帯電話の番号を入力します。ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。電話番号と PIN (該当する場合) を入力した後、ユーザーは、[今すぐテキスト メッセージで認証] ボタンをクリックします。Azure Multi-factor Authentication が、ユーザーの携帯電話に対して SMS 認証を実行します。ユーザーは、自己登録プロセスの次の手順に進むには、ワンタイム パスコード (OTP) を含む SMS を受け取り、その OTP と PIN (該当する場合) を使用してメッセージに応答する必要があります。

![ユーザー ポータルの SMS](./media/multi-factor-authentication-get-started-portal/text.png)

ユーザーが認証方法としてモバイル アプリを選択した場合、またはその方法を使用するように事前に構成されている場合は、ユーザーのデバイスに Azure Multi-Factor Authentication アプリのインストールとアクティブ化コードの生成を求めるメッセージがページに表示されます。ユーザーは、Multi-Factor Authentication アプリをインストールした後、[アクティブ化コードの生成] ボタンをクリックします。

>[AZURE.NOTE]Azure Multi-Factor Authentication アプリを使用するためには、ユーザーは各自のデバイスのプッシュ通知を有効にする必要があります。

アクティブ化コード、URL、およびバーコード画像がページに表示されます。ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。ユーザーは、Azure Multi-Factor Authentication アプリにアクティブ化コードと URL を入力するか、バーコード スキャナーを使用してバーコード画像をスキャンした後、[アクティブにする] ボタンをクリックします。

アクティブ化が完了したら、[今すぐ認証] ボタンをクリックします。Azure Multi-factor Authentication がユーザーのモバイル アプリに対して認証を実行します。ユーザーは、自己登録プロセスの次の手順に進むには、モバイル アプリに PINを入力し (該当する場合)、[認証] ボタンを押す必要があります。


管理者がセキュリティの質問と回答を収集するように Azure Multi-factor Authentication Server を構成している場合は [セキュリティの質問] ページが表示されます。ユーザーは、4 つのセキュリティの質問を選択し、選択した質問への回答を入力する必要があります。

![ユーザー ポータルのセキュリティの質問](./media/multi-factor-authentication-get-started-portal/secq.png)

これで、ユーザーの自己登録が完了し、ユーザーはユーザー ポータルにログインします。管理者によって許可されている場合、ユーザーは、今後いつでもユーザー ポータルに戻って、電話番号、PIN、認証方法、およびセキュリティの質問を変更できます。

 

<!---HONumber=AcomDC_0218_2016-->