---
title: Azure AD アプリケーション プロキシ コネクタをサイレント インストールする方法 | Microsoft Docs
description: Azure AD アプリケーション プロキシ コネクタのサイレント インストールを実行して、オンプレミス アプリへの安全なリモート アクセスを実現する方法について説明します。
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban

---
# Azure AD アプリケーション プロキシ コネクタをサイレント インストールする方法
複数の Windows サーバーまたはユーザー インターフェイスが有効になっていない Windows サーバーにインストール スクリプトを送信できます。このトピックでは、無人インストールを有効にし、Azure AD アプリケーション プロキシ コネクタをインストールして登録する Windows PowerShell スクリプトを作成する方法について説明します。

## アクセスの実現
アプリケーション プロキシは、社内ネットワークに "コネクタ" と呼ばれる軽量の Windows Server サービスをインストールすることによって機能します。アプリケーション プロキシ コネクタを機能させるには、グローバル管理者のアカウントとパスワードを使用して、Azure AD ディレクトリにコネクタを登録する必要があります。通常、これは、コネクタのインストール時にポップアップ ダイアログ ボックスに入力します。または、Windows PowerShell を使用して資格情報オブジェクトを作成することで登録情報を入力することも、独自のトークンを作成して、登録情報の入力に使用することもできます。

## 手順 1: 登録せずにコネクタをインストールする
次のように、コネクタを登録せずにコネクタ MSI をインストールします。

1. コマンド プロンプトを開きます。
2. 次のコマンドを実行します。/q はサイレント インストールを意味します。つまり、インストールで使用許諾契約書への同意を求めるメッセージは表示されません。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## 手順 2: コネクタを Azure Active Directory に登録する
次の方法のいずれかを使用してこれを行うこともできます。

* Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
* オフラインで作成したトークンを使用してコネクタを登録する

### Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
1. 次のスクリプトを実行して、Windows PowerShell 資格情報オブジェクトを作成します。"<username>" と "<password>" をディレクトリのユーザー名とパスワードに置き換えます。
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. **C:\\Program Files\\Microsoft AAD App Proxy Connector** に移動し、作成済みの PowerShell 資格情報オブジェクトを使用してスクリプトを実行します。ここで、$cred は作成済みの PowerShell 資格情報オブジェクトの名前です。
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### オフラインで作成したトークンを使用してコネクタを登録する
1. コード スニペットの値を使用する AuthenticationContext クラスを使用して、オフライン トークンを作成します。

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





1. トークンを作成したら、そのトークンを使用して SecureString を作成します。<br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. 次の Windows PowerShell コマンドを実行します。SecureToken は上記で作成したトークンの名前です。tenantID はテナントの GUID です。<br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## 関連項目
* [Azure Active Directory のアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)
* [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

最新のニュースと更新情報については、[アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)をご覧ください。

<!---HONumber=AcomDC_0622_2016-->