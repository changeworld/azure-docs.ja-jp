<properties 
	pageTitle="既存の SaaS コネクタへのアプリの接続" 
	description="この記事では、既存の SaaS コネクタへの接続方法を示します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# 既存の SaaS コネクタへのアプリの接続

## 概要

このチュートリアルでは、Azure Marketplace の SaaS コネクタを使用する方法について説明します。 

以下の手順を実行します。
- Dropbox コネクタをプロビジョニングします。
- Dropbox API アプリを構成します。
- ゲートウェイを構成します。
- トークンを取得し、トークン ストアに格納します。 
- Dropbox の API を呼び出し、認証済みアクセスが機能することを確認します。

## Dropbox コネクタのプロビジョニング

1. プレビュー ポータルのホーム ページに移動し、[Marketplace] をクリックします。

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Marketplace ギャラリーで Dropbox を検索します。

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Dropbox コネクタをプロビジョニングするために Dropbox のアイコンをクリックします。[作成] をクリックして、Dropbox コネクタをプロビジョニングします。[作成] をクリックする前に、必ず名前とすべてのフィールドに必要な値を入力してください。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. [プレビュー ポータル] (https://portal.azure.com/) でリソース グループのブレードを表示します。Dropbox コネクタとゲートウェイを確認します。API アプリをプロビジョニングすると、API アプリはリソース グループの一部としてプロビジョニングされます。ポータルでリソース グループが作成されたときに、ゲートウェイも作成されました。ゲートウェイは、リソース グループ内の API アプリ宛てのすべての要求を処理する、専用の Web アプリです。

	Web アプリと他の API アプリを同じリソース グループに追加することができます。リソース グループ内の各 API アプリには、次の 3 種類のアクセス レベルのいずれかを設定することができます。

	* パブリック (匿名) - すべてのユーザーがログインせずにリソース グループの外部から API アプリを呼び出すことができます。
	* パブリック (認証済み) - 認証されたユーザーのみがリソース グループの外部から API アプリを呼び出すことができます。
	* 内部 - 同一リソース グループに属する他の API アプリまたは Web アプリのみが API アプリを呼び出すことができます。

## Dropbox API アプリの構成

最初に、認証された要求のみを受け入れるように Dropbox を構成します。このアクセス レベルは**パブリック (認証済み)** に設定します。さらに、Azure Active Directory、Google、Facebook などのプロバイダーの認証を必要とするようにゲートウェイを構成します。

1.	Azure の[プレビュー ポータル](https://portal.azure.com/)で、**[参照]、[API Apps]** の順にクリックし、保護する API アプリの名前をクリックします。

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	[API アプリ] ブレードで、[設定]、[アプリケーションの設定] の順にクリックします。
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	**[アプリケーションの設定]** ブレードで、**[アクセス レベル]** を **[パブリック (認証)]** に変更します。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	これで、認証されていないアクセスから Dropbox コネクタが保護されました。次に、Dropbox の認証を設定します。次に示す UI の *[クライアント ID]* および *[クライアント シークレット]* の詳細に入力します (*[クライアント ID]* および *[クライアント シークレット]* には、[Dropbox 開発者アカウント](https://www.dropbox.com/developers/apps)から接続できます)。

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

次に、ゲートウェイを構成して、使用する認証プロバイダーを指定する必要があります。

## ゲートウェイの構成

1. Dropbox の [API アプリ] ブレードに戻り、ゲートウェイへのリンクをクリックします。
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. 使用する ID プロバイダーを選択し、該当する記事の手順に従って、API アプリをそのプロバイダーで構成します。これらの記事はモバイル アプリ向けに書かれていますが、API アプリでも手順は同じです。一部の手順では、[管理ポータル](https://manage.windowsazure.com/)と[プレビュー ポータル](https://portal.azure.com/)の両方を使用する必要があります。
  
	- [Microsoft アカウント](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Facebook ログイン](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Twitter ログイン](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Google ログイン](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	構成方法については、手順をおって説明した「API アプリの保護: Azure Active Directory またはソーシャル プロバイダーの認証の追加」の記事を参照してください。 

## OAuth トークンを取得し、トークン ストアに格納します。

1.	ブラウザーで、次のログイン URL に移動します。 

	ゲートウェイの [Active Directory の設定] ブレードから URL を取得できます。
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	URL は次の形式になります。
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	たとえば、リソース グループに "MyResource" という名前を付け、Azure Active Directory 認証用にゲートウェイを構成している場合は、URL は次のようになります。

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	ブラウザーに URL を入力して Enter キーを押す前に、開発者ツールを起動していることを確認してください。ログインが完了したことを通知するメッセージを受け取ります。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	[ネットワーク] タブ (Chrome) から zumo 認証トークンと値を取得します。
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Chrome で Postman 拡張機能を呼び出します。ゲートウェイの同意に関する URL、Zumo 認証トークン (ヘッダー)、および値を入力し、POST 要求を行います。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	POST 要求から返されたリダイレクト URL を取得し、このリダイレクト URL が動作することを確認します。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	リダイレクト URL が動作した場合は、トークンが有効であることを意味します。SaaS コネクタに対するすべての呼び出しでは、確立と検証が実行済みのトークンを使用します。このトークンは、ゲートウェイを経由する外部のソースからのすべての要求に含まれている必要があります。ブラウザーで API にアクセスすると、通常、ブラウザーはトークンを cookie に格納し、後続のすべての呼び出しと共にこのトークンを API に送信します。

Safesforce、Facebook などすべての SaaS コネクタにこれと同じフローが適用できます。 

## Dropbox の API を呼び出し、認証済みアクセスが機能することを確認します。

1. API アプリの一覧に戻り、Dropbox コネクタを選択します。 

1. 最上部の URL をメモします。

2. Dropbox コネクタ (図を参照) をクリックして、サポートされるすべての API を表示します。

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	[API の定義] ブレードでは、API アプリでサポートされている操作を確認できます。厳密に型指定されたクライアントを生成するために、Visual Studio で使用できる swagger をダウンロードできます。また、Sienna、PowerApp studio で使用できる swaddle もダウンロードできます。 

2. ブラウザー ウィンドウで、ポータルからコピーした URL を入力し、サポートされている任意の API を追加して、Dropbox アカウントのファイルまたは他の詳細情報にアクセスします。 

	形式:`<URL>`/Operation

	次に例を示します。

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	既に適切な認証が確立されているため、この呼び出しは正常に実行され、写真フォルダーの詳細がブラウザーに表示されます。 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<ブラウザーの画像をコピー>-->

## 次のステップ

ここまでは、API アプリを保護し、トークン ストアを使用して SaaS コネクタにアクセスするためにゲートウェイを構成する方法について説明しました。詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。 

<!--HONumber=49-->