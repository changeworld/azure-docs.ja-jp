<properties
	pageTitle="Azure Multi-Factor Authentication Server と Windows Server 2012 R2 AD FS を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護 | Microsoft Azure"
	description="この記事では、Azure Multi-Factor Authentication と Windows Server 2012 R2 の AD FS の使用を開始する方法について説明します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="billmath"/>


# Azure Multi-Factor Authentication Server と Windows Server 2012 R2 の AD FS を使用したクラウドとオンプレミスのリソースのセキュリティ保護

所属する組織で Active Directory フェデレーション サービス (AD FS) が使用されており、クラウドまたはオンプレミスのリソースをセキュリティ保護したい場合は、Azure Multi-Factor Authentication Server をデプロイして構成し、AD FS と連携させることができます。この構成を行うことで、重要なエンドポイントの多要素認証がトリガーされます。

この記事では、Azure Multi-Factor Authentication Server と Windows Server 2012 R2 の AD FS の使用について説明します。詳細については、「[Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-adfs2.md)」を参照してください。

## Windows Server 2012 R2 AD FS を Azure Multi-Factor Authentication Server でセキュリティ保護する

Azure Multi-Factor Authentication Server をインストールする際、次のオプションがあります。

- AD FS と同じサーバーに Azure Multi-Factor Authentication Server をローカル インストールする
- AD FS サーバーに Azure Multi-Factor Authentication アダプターをローカル インストールし、Multi-Factor Authentication Server を別のコンピューターにインストールする

開始する前に、次の情報に注意してください。

- Azure Multi-Factor Authentication Server を AD FS サーバーにインストールする必要はありません。ただし、AD FS 用の Multi-Factor Authentication アダプターを、AD FS が実行されている Windows Server 2012 R2 にインストールする必要があります。サポートされているバージョンの場合、サーバーを別のコンピューターにインストールできるため、AD FS アダプターを AD FS フェデレーション サーバーに別個にインストールします。アダプターを別個にインストールする方法については、次の手順を参照してください。
- Multi-Factor Authentication Server の AD FS アダプターが設計された時点では、AD FS によって証明書利用者の名前をアダプターに渡し、それをアプリケーション名として使用することができると想定されていました。しかし、結果的にはそうでないことが明らかになりました。所属する組織がテキスト メッセージやモバイル アプリの認証方法を使用している場合、[会社の設定] に定義された文字列にはプレースホルダー <$*application\_name*$> が含まれます。このプレースホルダーは、AD FS アダプターを使用する際に自動的に置き換えられません。AD FS をセキュリティ保護するときに、適切な文字列からプレースホルダーを削除することをお勧めします。

- サインインに使用するアカウントには、Active Directory サービスでセキュリティ グループを作成するためのユーザー権限が付与されている必要があります。

- Multi-Factor Authentication AD FS アダプターのインストール ウィザードでは、Active Directory のインスタンスに PhoneFactor Admins というセキュリティ グループが作成されます。その後、フェデレーション サービスの AD FS サービス アカウントがこのグループに追加されます。PhoneFactor Admins グループが実際に作成されていることと、AD FS サービス アカウントがこのグループのメンバーであることを、ドメイン コントローラーで確認することをお勧めします。必要に応じて、ドメイン コントローラーで AD FS サービス アカウントを PhoneFactor Admins グループに手動で追加します。
- ユーザー ポータルを使用した Web サービス SDK のインストールについては、「[Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ](multi-factor-authentication-get-started-portal.md)」を参照してください。


### Azure Multi-Factor Authentication Server を AD FS サーバーにローカル インストールする

1. AD FS フェデレーション サーバーに Azure Multi-Factor Authentication Server をダウンロードしてインストールします。インストール情報については、「[Azure Multi-Factor Authentication Server の概要](multi-factor-authentication-get-started-server.md)」を参照してください。
2. Azure Multi-Factor Authentication Server 管理コンソールで、**AD FS** アイコンをクリックし、**[ユーザー登録を許可する]** と **[ユーザーに認証方法の選択を許可する]** のオプションを選択します。
3. 対象の組織について指定するオプションが他にあれば、それを選択します。
4. **[AD FS アダプターのインストール]** をクリックします。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. コンピューターがドメインに参加しており、AD FS アダプターと Multi-Factor Authentication サービスの間の通信をセキュリティ保護するための Active Directory 構成が完了していない場合、**[Active Directory]** ウィンドウが表示されます。**[次へ]** をクリックしてこの構成を自動的に完了するか、**[Active Directory の自動構成をスキップして手動で設定を構成する]** チェック ボックスをオンにし、**[次へ]** をクリックします。
6. コンピューターがドメインに参加しておらず、AD FS アダプターと Multi-Factor Authentication サービスの間の通信をセキュリティ保護するためのローカル グループ構成が完了していない場合、**[ローカル グループ]** ウィンドウが表示されます。**[次へ]** をクリックしてこの構成を自動的に完了するか、**[ローカル グループの自動構成をスキップして手動で設定を構成する]** チェック ボックスをオンにし、**[次へ]** をクリックします。
7. インストール ウィザードで **[次へ]** をクリックします。Azure Multi-Factor Authentication Server によって PhoneFactor Admins グループが作成され、AD FS サービス アカウントがその PhoneFactor Admins グループに追加されます。
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. **[インストーラーの起動]** ページで **[次へ]** をクリックします。
9. Multi-Factor Authentication AD FS アダプター インストーラーで、**[次へ]** をクリックします。
10. インストールが完了したら、**[閉じる]** をクリックします。
11. アダプターのインストールが完了したら、AD FS に登録する必要があります。Windows PowerShell を開き、次のコマンドを実行します。<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. 新しく登録されたアダプターを使用するよう AD FS のグローバル認証ポリシーを編集します。AD FS 管理コンソールで、**認証ポリシー** ノードに移動します。**[Multi-Factor Authentication]** セクションで、**[グローバル設定]** セクションの横にある **[編集]** リンクをクリックします。**[Edit Global Authentication Policy (グローバル認証ポリシーの編集)]** ウィンドウで、**Multi-Factor Authentication** を追加の認証方式として選択し、**[OK]** をクリックします。アダプターが WindowsAzureMultiFactorAuthentication として登録されます。登録を有効にするには、AD FS サービスを再起動する必要があります。

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

この時点で、Multi-Factor Authentication Server は、AD FS と共に使用する追加の認証プロバイダーとして設定されます。

## Web サービス SDK を使用して AD FS アダプターのスタンドアロン インスタンスをインストールする
1. Multi-Factor Authentication Server を実行しているサーバーに Web サービス SDK をインストールします。
2. \\Program Files\\Multi-Factor Authentication Server ディレクトリから、AD FS アダプターのインストール先となる予定のサーバーに次のファイルをコピーします。
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. MultiFactorAuthenticationAdfsAdapterSetup64.msi インストール ファイルを実行します。
4. Multi-Factor Authentication AD FS アダプター インストーラーで **[次へ]** をクリックしてインストールを実行します。
5. インストールが完了したら、**[閉じる]** をクリックします。
6. 次の操作を行って、MultiFactorAuthenticationAdfsAdapter.config ファイルを編集します。

|MultiFactorAuthenticationAdfsAdapter.config の手順| サブ手順|
|:------------- | :------------- |
|**UseWebServiceSdk** ノードを **true** に設定します。||
|**WebServiceSdkUrl** の値を、Multi-Factor Authentication Web サービス SDK の URL に設定します。</br></br>例: **https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**</br></br>この certificatename は実際の証明書の名前です。 ||
|Web サービス SDK を構成します。<br><br>"*オプション 1*": ユーザー名とパスワードの使用|<ol type="a"><li>**WebServiceSdkUsername** の値を、PhoneFactor Admins セキュリティ グループのメンバーであるアカウントに設定します。&lt;domain&gt;&#92;&lt;user name&gt; の形式を使用します。<li>**WebServiceSdkPassword** の値を適切なアカウント パスワードに設定します。</li></ol>
|Web サービス SDK を構成します ("*続き*")。<br><br>"*オプション 2*": クライアント証明書の使用|<ol type="a"><li>Web サービス SDK を実行しているサーバーの証明機関からクライアント証明書を取得します。[クライアント証明書を取得する](https://technet.microsoft.com/library/cc770328.aspx)方法を確認してください。</li><li>クライアント証明書を、Web サービス SDK を実行しているサーバー上のローカル コンピューターの個人用証明書ストアにインポートします。注: 証明機関の公開証明書が、信頼されたルート証明書の証明書ストアにあることを確認します。</li><li>クライアント証明書の公開キーと秘密キーを .pfx ファイルにエクスポートします。</li><li>Base64 形式の公開キーを .cer ファイルにエクスポートします。</li><li>サーバー マネージャーで、Web Server (IIS)\\Web Server\\Security\\IIS Client 証明書マッピング認証機能がインストールされていることを確認します。インストールされていない場合は、**[役割と機能の追加]** を選択してこの機能を追加します。</li><li>IIS マネージャーで、Web サービス SDK 仮想ディレクトリが含まれている Web サイトの **[構成エディター]** をダブルクリックします。注: 仮想ディレクトリ レベルではなく、Web サイト レベルでこの操作を行うことが非常に重要です。</li><li>**system.webServer/security/authentication/iisClientCertificateMappingAuthentication** セクションに移動します。</li><li>**[有効]** を **true** に設定します。</li><li>**[OneToOneCertificateMappingsEnabled]** を **true** に設定します。</li><li>**[oneToOneMappings]** の横にある **[...]** ボタンをクリックし、**[追加]** リンクをクリックします。</li><li>前にエクスポートした Base64.cer ファイルを開きます。*-----BEGIN CERTIFICATE-----*、*-----END CERTIFICATE-----*、およびすべての改行を削除します。結果の文字列をコピーします。</li><li>**証明書**を、前の手順でコピーした文字列に設定します。</li><li>**[有効]** を **true** に設定します。</li><li>**userName** を、PhoneFactor Admins セキュリティ グループのメンバーであるアカウントに設定します。&lt;domain&gt;&#92;&lt;user name&gt; 形式を使用します。</li><li>パスワードを適切なアカウントのパスワードに設定します。その後、構成エディターを閉じます。</li><li>**[適用]** リンクをクリックします。</li><li>Web サービス SDK の仮想ディレクトリで、**[認証]** をダブルクリックします。</li><li>**[ASP.NET 偽装]** と **[基本認証]** が **[有効]** になっており、他のアイテムがすべて **[無効]** になっていることを確認します。</li><li>Web サービス SDK の仮想ディレクトリで、**[SSL 設定]** をダブルクリックします。</li><li>**[クライアント証明書]** を **[受け入れる]** に設定し、**[適用]** をクリックします。</li><li>前にエクスポートした .pfx ファイルを、AD FS アダプターを実行しているサーバーにコピーします。</li><li>.pfx ファイルを、ローカル コンピューターの個人証明書ストアにインポートします。</li><li>**[秘密キーの管理]** を右クリックして選択し、AD FS サービスへのサインインに使用したアカウントに対して読み取りアクセス権を付与します。</li><li>クライアント証明書を開き、**[詳細]** タブから拇印をコピーします。</li><li>MultiFactorAuthenticationAdfsAdapter.config ファイルで、**WebServiceSdkCertificateThumbprint** を前の手順でコピーした文字列に設定します。</li></ol>
| Register-MultiFactorAuthenticationAdfsAdapter.ps1 スクリプトを編集します。具体的には、*-ConfigurationFilePath &lt;path&gt;* を `Register-AdfsAuthenticationProvider` コマンドの末尾に追加します。このときの *&lt;path&gt;* は、MultiFactorAuthenticationAdfsAdapter.config ファイルへの完全なパスです。||

アダプターを登録するために、\\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 スクリプトを PowerShell で実行します。アダプターが WindowsAzureMultiFactorAuthentication として登録されます。登録を有効にするには、AD FS サービスを再起動する必要があります。

<!---HONumber=AcomDC_0831_2016-->