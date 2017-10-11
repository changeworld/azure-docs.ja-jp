---
title: "Azure MFA Server モバイル アプリ Web サービス | Microsoft Docs"
description: "Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。  これにより MFA Server で、ユーザーへのプッシュ通知を許可できます。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ認証を有効にする

Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。 Azure Multi-Factor Authentication では、ログイン時にユーザーに自動音声通話または SMS 送信を行うのではなく、ユーザーのスマートフォンまたはタブレットの Microsoft Authenticator アプリにプッシュ通知を行います。 ユーザーはアプリで **[認証]** をタップ (または PIN を入力し、[認証] をタップ) するだけでサインインを完了することができます。

電波の受信の信頼性に欠けるときには、2 段階認証にモバイル アプリを使用することをお勧めします。 アプリを OATH トークン ジェネレーターとして使用する場合、ネットワーク接続やインターネット接続は不要です。

環境に応じて、モバイル アプリ web サービスで Azure Multi-factor Authentication Server と同じサーバーまたは別のインターネットに接続するサーバーを展開します。

## <a name="requirements"></a>必要条件

Microsoft Authenticator アプリを使用するには、アプリがモバイル アプリ Web サービスと正常に通信できるように次の要件があります。

* Azure Multi-Factor Authentication Server v6.0 以降が必要です。
* Microsoft® [インターネット インフォメーション サービス (IIS) 7.x 以降](http://www.iis.net/)を実行している、インターネットに接続された Web サーバーに、モバイル アプリ Web サービスをインストールします。
* ASP.NET v4.0.30319 がインストールおよび登録され、[許可] に設定されている必要があります。
* 必要なロール サービスには、ASP.NET および IIS 6 メタベース互換が含まれます
* モバイル アプリ Web サービスには、パブリック URL を使用してアクセスできる必要があります。
* モバイル アプリ Web サービスは、SSL 証明書で保護されている必要があります。
* IIS での Azure Multi-factor Authentication Web サービス SDK のインストール 7.x 以降、 **Azure Multi-factor Authentication Server と同じサーバー**
* Azure Multi-Factor Authentication Web サービス SDK は、SSL 証明書で保護されている必要があります。
* モバイル アプリ Web サービスは、SSL 経由で Azure Multi-Factor Authentication Web サービス SDK に接続できる必要があります。
* モバイル アプリ Web サービスは、"PhoneFactor Admins" セキュリティ グループのメンバーであるサービス アカウントの資格情報を使用して、Azure MFA Web サービス SDK に対して認証できる必要があります。 Azure Multi-Factor Authentication Server がドメインに参加しているサーバーで実行されている場合、このサービス アカウントとグループは Active Directory に存在します。 このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加していない場合、Azure Multi-Factor Authentication Server のローカルに存在します。

## <a name="install-the-mobile-app-web-service"></a>モバイル アプリ Web サービスのインストール

モバイル アプリ Web サービスをインストールする前に、次の点に注意してください。

* "PhoneFactor Admins"の一部であるサービス アカウントが必要なグループです。 このアカウントは、ユーザー ポータルをインストールするために使用したものと同じであることができます。
* インターネットに接続された Web サーバーで Web ブラウザーを開き、web.config ファイルに入力された Web サービス SDK の URL に移動することをお勧めします。 ブラウザーが Web サービスを正常に取得できる場合、資格情報が求められます。 web.config ファイルに入力したユーザー名とパスワードを、ファイルに表示されているとおりに入力します。 証明書の警告およびエラーが表示されないことを確認してください。
* リバース プロキシまたはファイアウォールがモバイル アプリ Web サービス Web サーバーの前に配置され、SSL オフロードを実行している場合、モバイル アプリ Web サービスが https ではなく http を使用できるように、モバイル アプリ Web サービスの web.config ファイルを編集できます。 モバイル アプリからファイアウォール/リバース プロキシへの SSL は引き続き必要となります。 \<appSettings\> セクションに次のキーを追加します。

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Web サービス SDK のインストール

どちらのシナリオでは、Azure Multi-factor Authentication Web サービス SDK がある場合に**いない**Azure Multi-factor Authentication (MFA) Server に既にインストールされて、以下の手順を完了します。

1. Multi-factor Authentication Server コンソールを開きます。
2. 移動して、 **Web サービス SDK**選択**Web サービス SDK のインストール**です。
3. 何らかの理由を変更する必要がない場合、既定の設定を使用してインストールを完了します。
4. IIS 内のサイトに SSL 証明書をバインドします。

IIS サーバーで SSL 証明書の構成についての質問がある場合は、記事を参照して[IIS で SSL を設定する方法](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)です。

Web サービス SDK は、SSL 証明書で保護されている必要があります。 このために自己署名証明書を使用できます。 SSL 接続を開始するときにその証明書を信頼するように、ユーザー ポータル web サーバー上にローカル コンピューター アカウントの「信頼されたルート証明機関」ストアに証明書をインポートします。

![MFA Server configuration セットアップ Web サービス SDK](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>サービスのインストール

1. **MFA サーバーで**のインストール パスを参照します。
2. 移動、既定では、Azure MFA Server がインストールされているフォルダーに**C:\Program \azure Multi-factor Authentication**です。
3. インストール ファイルを見つけます**MultiFactorAuthenticationMobileAppWebServiceSetup64**です。 サーバーがある場合**いない**インターネットに接続する、インターネットに接続するサーバーにインストール ファイルをコピーします。
4. 場合は、MFA Server**いない**をインターネットに接続するスイッチ、**インターネットに接続するサーバー**です。
5. 実行、 **MultiFactorAuthenticationMobileAppWebServiceSetup64**管理者としてファイルをインストールする、必要に応じて、サイトを変更したい場合は、短い名前に、仮想ディレクトリを変更します。
6. インストールを完了したらを参照**C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (または、仮想ディレクトリ名に基づく適切なディレクトリ)、Web.Config ファイルを編集します。

   * キーを見つける**"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"**変更と**値 =""**に**値 ="domain \user"** "PhoneFactor Admins"の一部であるサービス アカウントを domain \user がここではグループ。
   * キーを見つける**"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"**を変更して**値 =""**に**値 ="Password"**パスワードがパスワードのサービス アカウントは、前の行に入力します。
   * 検索、 **pfMobile App Web Service_pfwssdk_PfWsSdk**設定と値から変更**http://localhost:4898/PfWsSdk.asmx** Web サービス SDK の URL に (例: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)。
   * Web.Config ファイルを保存してメモ帳を閉じます。

   > [!NOTE]
   > Web サービス SDK を参照する必要がありますので、この接続で SSL を使用すると、**完全修飾ドメイン名 (FQDN)**と**IP アドレスではなく**です。 SSL 証明書が発行された、FQDN と使用する URL が証明書の名前と一致する必要があります。

7. モバイル アプリ Web サービスがインストールされている web サイトが公開されている署名証明書に既にバインドされていない場合、サーバー証明書をインストール、IIS マネージャーを開いて、および web サイトに証明書をバインドします。
8. 任意のコンピューターから web ブラウザーを開き、モバイル アプリ Web サービスがインストールされている URL に移動し (例: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService)。 証明書の警告およびエラーが表示されないことを確認してください。

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ設定を構成する

モバイル アプリ Web サービスがインストールされたため、ポータルから Azure Multi-Factor Authentication Server を構成する必要があります。

1. Multi-factor Authentication Server コンソールで、ユーザー ポータル アイコンをクリックします。 ユーザーに認証方法の制御を許可する場合は、[設定] タブで **[ユーザーに認証方法の選択を許可する]** の **[モバイル アプリ]** チェック ボックスをオンにします。 この機能を有効にせずエンドユーザーがモバイル アプリのアクティブ化を完了するには、ヘルプ デスクに連絡する必要です。
2. **[ユーザーにモバイル アプリのアクティブ化を許可する]** チェック ボックスをオンにします。
3. **[ユーザー登録を許可する]** チェック ボックスをオンにします。
4. クリックして、**モバイル アプリの**アイコン。
5. MultiFactorAuthenticationMobileAppWebServiceSetup64 をインストールするときに作成された仮想ディレクトリで使用されている URL を入力してください (例: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) フィールドに**モバイル アプリ Web サービス URL:**です。
6. 追加、**アカウント名**フィールドにこのアカウントのモバイル アプリケーションで表示する会社または組織の名前。
   ![MFA サーバーの構成のモバイル アプリの設定](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ](multi-factor-authentication-advanced-vpn-configurations.md)。