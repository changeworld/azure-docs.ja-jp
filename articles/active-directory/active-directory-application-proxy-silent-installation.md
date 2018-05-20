---
title: Azure AD アプリケーション プロキシ コネクタのサイレント インストール | Microsoft Docs
description: Azure AD アプリケーション プロキシ コネクタの無人インストールを実行して、オンプレミスのアプリへの安全なリモート アクセスを実現する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: b9d8535be51601f2ca438f2d22c18656c5ff0dc0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタ用の無人インストール スクリプトを作成します。

このトピックでは、Azure AD アプリケーション プロキシ コネクタの無人インストールと登録を可能にする Windows PowerShell スクリプトの作成方法について説明します。

この機能は次の場合に便利です。

* ユーザー インターフェイスが有効でない、または Remote Desktop でアクセスできない Windows サーバーにコネクタをインストールする。
* 一度に多数のコネクタをインストールし、登録する。
* コネクタのインストールと登録を別の手順の一部として統合する。
* コネクタのビットを含むが未登録の標準的なサーバー イメージを作成する。

[アプリケーション プロキシ コネクタ](application-proxy-understand-connectors.md)を機能させるには、グローバル管理者のアカウントとパスワードを使用して、Azure AD ディレクトリにコネクタを登録する必要があります。 通常、この情報は、コネクタのインストール時にポップアップ ダイアログ ボックスに入力しますが、このプロセスを PowerShell を使用して自動化できます。

無人インストールには 2 つの手順があります。 最初に、コネクタをインストールします。 次に、コネクタを Azure AD に登録します。 

## <a name="install-the-connector"></a>コネクタをインストールする
次の手順に従って、コネクタを登録なしでインストールします。

1. コマンド プロンプトを開きます。
2. 次のコマンドを実行します。/q はサイレント インストールを意味します。 サイレント インストールでは、使用許諾契約書への同意を求めるメッセージは表示されません。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>コネクタを Azure AD に登録する
コネクタを登録するために使用できる方法は 2 つあります。

* Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
* オフラインで作成したトークンを使用してコネクタを登録する

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
1. 管理者のユーザー名とディレクトリのパスワードを格納する Windows PowerShell 資格情報オブジェクト `$cred` を作成します。 *\<username\>* と *\<password\>* を置き換えて、次のコマンドを実行します。
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、作成済みの `$cred` オブジェクトを使用してスクリプトを実行します。
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>オフラインで作成したトークンを使用してコネクタを登録する
1. 次のコード スニペットの値を使用して、AuthenticationContext クラスを使用するオフライン トークンを作成します。

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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


2. トークンを作成したら、そのトークンを使用して SecureString を作成します。

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. \<tenant GUID\> をご使用のディレクトリ ID で置き換えて、次の Windows PowerShell コマンドを実行します。

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>次の手順 
* [独自のドメイン名でアプリケーションを発行する](manage-apps/application-proxy-configure-custom-domain.md)
* [シングル サインオンを有効にする](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)


