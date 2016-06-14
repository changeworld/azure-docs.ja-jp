<properties
	pageTitle="Azure AD アプリケーション プロキシを有効にする | Microsoft Azure"
	description="Azure クラシック ポータルでアプリケーション プロキシを有効にして、リバース プロキシ用のコネクタをインストールします。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="kgremban"/>

# Azure ポータルでアプリケーション プロキシを有効にする

この記事では、Microsoft Azure AD アプリケーション プロキシを Azure AD のクラウド ディレクトリに対して有効にする手順について説明します。この手順には、お使いのプライベート ネットワークに対してアプリケーション プロキシ コネクタをインストールする作業も含まれます。これにより、ネットワークからプロキシ サービスへの接続が維持されます。また、Microsoft Azure AD テナント サブスクリプションへのコネクタの登録も行います。アプリケーション プロキシが何に役立つのかよくわからないという場合は、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」で詳細を確認してください。

この Azure AD アプリケーション プロキシを有効にするチュートリアルを完了すると、リモート アクセス用にオンプレミスのアプリを発行する準備が整います。

> [AZURE.NOTE] アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## アプリケーション プロキシの前提条件
アプリケーション プロキシ サービスを有効にして使用するためには、次の条件を満たしておく必要があります。

- [Microsoft Azure AD の Basic または Premium サブスクリプション](active-directory-editions.md)に加え、自分が全体管理者となっている Azure AD ディレクトリ。
- アプリケーション プロキシ コネクタをインストールできる Windows Server 2012 R2 または Windows 8.1 以降が実行されているサーバー。このサーバーは、クラウド内のアプリケーション プロキシ サービスに HTTPS 要求を送信します。また、発行予定のアプリケーションに HTTPS で接続できることが必要です。
- 経路上にファイアウォールが存在する場合、コネクタからアプリケーション プロキシに HTTPS (TCP) 要求を行うことができるように、ファイアウォールを開放する必要があります。コネクタは、上位ドメイン *msappproxy.net* および *servicebus.windows.net* に属しているサブドメインと併せて、以下のポートを使用します。**送信**トラフィックに対しては、以下のポートを**すべて**開放してください。

	| ポート番号 | 説明 |
	| --- | --- |
	| 80 | セキュリティ検証用の送信 HTTP トラフィックに使用されます。 |
	| 443 | Azure AD に対するユーザー認証に使用されます (コネクタ登録プロセスでのみ必要)。 |
	| 10100 ～ 10120 | プロキシに送り返される LOB HTTP 応答に使用されます。 |
	| 9352、5671 | コネクタと Azure サービス間で受信要求の通信に使用されます。 |
	| 9350 | 省略可能。受信要求のパフォーマンス向上に使用されます。 |
	| 8080 | コネクタのブートストラップ シーケンスのほか、コネクタの自動更新に使用されます。 |
	| 9090 | コネクタの登録に使用されます (コネクタ登録プロセスでのみ必要)。 |
	| 9091 | コネクタの信頼証明書の自動更新に使用されます。 |

ご利用のファイアウォールが送信元ユーザーに応じてトラフィックを監視している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。また、NT Authority\\System に対しては必ずポート 8080 を有効にしてください。


## 手順 1: Azure AD でのアプリケーション プロキシの有効化
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に管理者としてサインインします。
2. Active Directory に移動し、アプリケーション プロキシを有効にするディレクトリを選択します。

	![Active Directory - icon](./media/active-directory-application-proxy-enable/ad_icon.png)

3. ディレクトリ ページから **[構成]** を選択し、下にスクロールして **[アプリケーション プロキシ]** まで移動します。
4. **[このディレクトリに対してアプリケーション プロキシ サービスを有効にする]** を **[有効]** に切り替えます。

	![アプリケーション プロキシを有効にする](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. **[今すぐダウンロード]** を選択します。これにより、**Azure AD アプリケーション プロキシ コネクタのダウンロード** ページが表示されます。ライセンス条項を読んで同意し、**[ダウンロード]** をクリックして、アプリケーション プロキシ コネクタの Windows インストーラー ファイル (.exe) を保存します。

## 手順 2: コネクタのインストールと登録
1. 前述した前提条件に従い、用意したサーバーで *AADApplicationProxyConnectorInstaller.exe* を実行します。
2. ウィザードの指示に従ってインストールします。
3. インストール時に、Azure AD テナントのアプリケーション プロキシにコネクタを登録するように求められます。

  - Azure AD グローバル管理者の資格情報を指定します。グローバル管理者のテナントは、Microsoft Azure の資格情報とは異なる場合があります。
  - コネクタを登録する管理者が、アプリケーション プロキシ サービスを有効にしたディレクトリに存在することを確認します。たとえば、テナント ドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他のいずれかのエイリアスであることが必要です。
  - Azure AD コネクタをインストールするサーバーで **[IE セキュリティ強化の構成]** を **[オン]** に設定している場合、登録画面がブロックされることがあります。その場合は、エラー メッセージに示された指示に従って、アクセスを許可します。Internet Explorer セキュリティ強化の構成が無効になっていることを確認します。
  - コネクタの登録が成功しない場合は、「[アプリケーション プロキシのトラブルシューティング](active-directory-application-proxy-troubleshoot.md)」をご覧ください。  

4. インストールが完了したら、次に示す 2 つの新しいサービスがサーバーに追加されます。

 	- **Microsoft AAD アプリケーション プロキシ コネクタ**: 接続を有効にします。
	- **Microsoft AAD アプリケーション プロキシ コネクタ アップデーター**: 定期的にコネクタの新しいバージョンをチェックし、必要に応じてコネクタを更新する自動更新サービスです。

	![App Proxy Connector services - screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. インストール ウィンドウで、**[完了]** をクリックしてインストールを終了します。

[アプリケーション プロキシを使用してアプリケーションを発行](active-directory-application-proxy-publish.md)する準備がこれで整いました。

高可用性を確保するには、追加のコネクタを少なくとも 1 つデプロイする必要があります。追加のコネクタをデプロイするには、上記の手順 2. と手順 3. を繰り返します。各コネクタは個別に登録する必要があります。

コネクタをアンインストールする場合は、コネクタ サービスと更新サービスを両方ともアンインストールしてから、コンピューターを再起動してサービスを完全に削除するようにしてください。


## 次のステップ

- [アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)
- [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

最新のニュースと更新情報については、[アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)をご覧ください。

<!---HONumber=AcomDC_0608_2016-->