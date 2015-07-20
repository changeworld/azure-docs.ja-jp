<properties 
	pageTitle="Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ" 
	description="Azure MFA およびユーザー ポータルを開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
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

<ol>
<li>Azure Multi-Factor Authentication Server 内で: 左側のメニューの [ユーザー ポータル] アイコンをクリックし、[ユーザー ポータルのインストール] ボタンをクリックします。<li>[次へ] をクリックします。<li>[次へ] をクリックします。<li>コンピューターがドメインに参加しており、ユーザー ポータルと Azure Multi-Factor Authentication サービスの間の通信をセキュア保護するための Active Directory 構成が完了していない場合、Active Directory の手順が表示されます。[次へ] ボタンをクリックし、この構成を自動的に完了します。<li>[次へ] をクリックします。<li>[次へ] をクリックします。<li>[閉じる] をクリックします。<li>任意のコンピューターから web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (例: https://www.publicwebsite.com/MultiFactorAuth)。証明書の警告およびエラーが表示されないことを確認してください。</li>

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

<ol>




<li>Azure Multi-Factor Authentication Server サーバーで Windows エクスプ ローラーを開き、Azure Multi-Factor Authentication Server がインストールされているフォルダーに移動します (C:\Program Files\Multi-Factor Authentication Server など)。ユーザー ポータルがインストールされるサーバーに応じて、MultiFactorAuthenticationUserPortalSetup インストール ファイルの 32 ビットまたは 64 ビット バージョンを選択します。インターネットに接続されたサーバーにインストール ファイルをコピーします。</li>

<li>インターネットに接続された Web サーバーで、セットアップ ファイルを管理者権限で実行する必要があります。これを最も簡単に行うには、管理者としてコマンド プロンプトを開き、インストール ファイルがコピーされた場所に移動します。</li>

<li>MultiFactorAuthenticationUserPortalSetup64 のインストール ファイルを実行し、必要に応じて、サイトと仮想ディレクトリの名前を変更します。</li>

<li>ユーザー ポータルのインストールを完了したら、C:\inetpub\wwwroot\MultiFactorAuth (または仮想ディレクトリ名に基づく適切なディレクトリ) に移動し、web.config ファイルを編集します。</li>

<li>USE_WEB_SERVICE_SDK キーを見つけて、値を false から true に変更します。WEB_SERVICE_SDK_AUTHENTICATION_USERNAME および WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD キーを見つけ、PhoneFactor Admins セキュリティ グループのメンバーであるサービス アカウントのユーザー名およびパスワードに値を設定します (上記の「要件」セクションを参照)。行末尾 (value=””/>) の引用符の間にユーザー名およびパスワードを入力するようにしてください。修飾されたユーザー名 (domain\username または machine\username など) を使用することをお勧めします。</li>

<li>pfup_pfwssdk_PfWsSdk 設定を見つけ、値を「http://localhost:4898/PfWsSdk.asmx」から Azure Multi-Factor Authentication Server で実行している Web サービス SDK の URL に変更します (https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx など)。この接続では SSL が使用されているため、IP アドレスではなくサーバー名で Web サービス SDK を参照する必要があります。これは、SSL 証明書がサーバー名に対して発行され、使用される URL が証明書の名前に一致する必要があるためです。サーバー名がインターネットに接続されているサーバーで IP アドレスに解決されない場合、そのサーバーの hosts ファイルにエントリを追加し、Azure Multi-Factor Authentication Server の名前を IP アドレスにマッピングします。変更を行ったら、web.config ファイルを保存します。</li>

<li>既定の Web サイトの直下などにユーザー ポータルがインストールされた Web サイトがまだ公的署名証明書にバインドされていない場合、証明書がまだインストールされていないならサーバーにインストールし、IIS マネージャーを開き証明書を Web サイトにバインドします。</li>

<li>任意のコンピューターで Web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (https://www.publicwebsite.com/MultiFactorAuth など)。証明書の警告およびエラーが表示されないことを確認してください。</li>

## Azure Multi-Factor Authentication Server のユーザー ポータル設定の構成
ポータルがインストールされたため、Azure Multi-Factor Authentication Server を構成してポータルと連携させる必要があります。

### Azure Multi-Factor Authentication Server のユーザー ポータル設定を構成するには




1. Azure Multi-Factor Authentication Server で [ユーザー ポータル] アイコンをクリックします。[設定] タブで、ユーザー ポータルへの URL を [ユーザー ポータルの URL] テキスト ボックスに入力します。電子メール機能が有効な場合、この URL は、Azure Multi-Factor Authentication Server にインポートされるときにユーザーに送信される電子メールに挿入されます。
2. ユーザー ポータルで使用する設定を選択します。たとえば、ユーザーによる認証方法の制御を許可する場合は、ユーザーが選択できる方法と [ユーザーに認証方法の選択を許可する] にチェックマークを付けます。
3. 表示される設定について理解するには、ヘルプの右上隅の [ヘルプ] リンクをクリックします。

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->