<properties
	pageTitle="Azure AD iOS の概要 | Microsoft Azure"
	description="サインインと、OAuth を用いた Azure AD の保護 API の呼び出しのために Azure AD と統合する、iOS アプリケーションの構築方法を説明します。"
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Azure AD の iOS アプリへの統合

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

デスクトップ アプリケーションを開発する場合、Azure AD を使用すると、Active Directory アカウントを使用してユーザーの認証処理を容易に行うことができます。また、Office 365 API や Azure API などの Azure AD によって保護された任意の Web API をアプリケーションで安全に使用することもできます。

保護されたリソースにアクセスする必要がある iOS クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。それがどれほど簡単であるかを示すために、ここで、次のような、Objective-C の To-Do List アプリを構築します。

-	[OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して Azure AD Graph API を呼び出すためのアクセス トークンを取得します。
-	指定されたエイリアスを持つユーザーをディレクトリで検索します。
-	ユーザーのサインアウト処理を行います。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

2. Azure AD にアプリケーションを登録する
3. ADAL をインストールおよび構成する
5. ADAL を使用して、Azure AD からトークンを取得する

最初に、[アプリのスケルトンをダウンロードするか](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。また、ユーザーを作成し、アプリケーションを登録することを可能にするための Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## 手順 1. .Net または Node.js REST API の TODO サンプル サーバーをダウンロードして実行する

このサンプルは、Microsoft Azure Active Directory の単一のテナント ToDo REST API の作成用の既存のサンプルを扱うために、特別に作成されました。これは、クイック スタートの前提条件です。

この設定方法については、次の既存のサンプルを参照してください。

* [Node.js 用の Microsoft Azure Active Directory のサンプル REST API サービス](active-directory-devquickstarts-webapi-nodejs.md)

## 手順 2. Web API を Microsoft Azure AD テナントに登録する

**実行する事柄**

*Microsoft Active Directory は、2 種類のアプリケーションの追加をサポートします。サービスをユーザーに提供する Web API と、それらの Web API にアクセスする (Web 上またはデバイス上のいずれかで実行する) アプリケーションです。この手順では、このサンプルをテストするために、ローカルで実行している Web API を登録します。この Web API は、通常、アプリがアクセスする機能を提供する REST サービスです。Microsoft Azure Active Directory は、あらゆるエンドポイントを保護できます。*

*ここでは前述の TODO REST API を登録することを想定していますが、この方法は、Azure Active Directory で保護するすべての Web API に有効です。*

Microsoft Azure AD を使用して Web API を登録する手順

1. [Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで [Active Directory] をクリックします。
3. サンプル アプリケーションを登録するディレクトリ テナントをクリックします。
4. [アプリケーション] タブをクリックします。
5. ドロアーで、[追加] をクリックします。
6. [組織で開発中のアプリケーションを追加] をクリックします。
7. たとえば "TodoListService" など、アプリケーションのわかりやすい名前を入力し、[Web アプリケーションや Web API] を選択して、[次へ] をクリックします。
8. サインオン URL には、サンプルのベース URL を入力します。既定では `https://localhost:8080` となります。
9. アプリ ID URI には、「`https://<your_tenant_name>/TodoListService`」と入力します。これは `<your_tenant_name>` を Azure AD テナントの名前に置き換えます。[OK] クリックして登録を完了します。
10. 引き続き Azure ポータルで、アプリケーションの [構成] タブをクリックします。
11. **クライアント ID 値を見つけて、コピーして保持しておきます**。これは後でアプリケーションを構成するときに必要になります。

## 手順 3. サンプルの iOS ネイティブ クライアント アプリケーションを登録する

最初の手順として、Web アプリケーションを登録します。次に、アプリケーションについて Azure Active Directory に通知する必要があります。これにより、アプリケーションは登録した Web API と通信できるようになります。

**実行する事柄**

*前述のとおり、Microsoft Azure Active Directory は、2 種類のアプリケーションの追加をサポートします。サービスをユーザーに提供する Web API と、それらの Web API にアクセスする (Web 上またはデバイス上のいずれかで実行する) アプリケーションです。この手順では、このサンプルのアプリケーションを登録します。このアプリケーションが、登録した Web API へのアクセスを要求できるようにするために、この手順は必要です。Azure Active Directory は、登録されていないアプリケーションに対して、サインインを要求することさえも拒否します。 モデル化されたセキュリティの一環としてそのようになります。*

*ここでは上述のサンプル アプリケーションを登録することを想定していますが、手順は開発するすべてのアプリケーションに有効です。*

**アプリケーションと Web API の両方を 1 テナントに配置する理由**

*ご想像どおり、別のテナントから Azure Active Directory に登録されている外部 API にアクセスするアプリを構築することができます。そのようにすると、お客様に対してアプリケーションでの API の使用に同意が求められることになります。このようにする利点として、iOS 向け Active Directory 認証ライブラリがユーザーに代わって同意に対応することになります。 より高度な機能を利用するようになるにつれて、これが、Azure、Office、および他のすべてのサービス プロバイダーから一連の Microsoft API にアクセスするために必要な処理の重要な部分であることがわかるようになります。この時点では、同じテナントに Web API とアプリケーションの両方を登録しているので、同意のプロンプトは表示されません。これは、通常、自社のみで使用するアプリケーションを開発している場合に当てはまります。*

1. [Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで [Active Directory] をクリックします。
3. サンプル アプリケーションを登録するディレクトリ テナントをクリックします。
4. [アプリケーション] タブをクリックします。
5. ドロアーで、[追加] をクリックします。
6. [組織で開発中のアプリケーションを追加] をクリックします。
7. たとえば "TodoListClient-iOS" など、アプリケーションのわかりやすい名前を入力し、[ネイティブ クライアント アプリケーション] を選択して、[次へ] をクリックします。
8. リダイレクト URI には、「`http://TodoListClient`」を入力します。[完了] をクリックします。
9. アプリケーションの [構成] タブをクリックします。
10. クライアント ID 値を見つけて、コピーして保持しておきます。これは後でアプリケーションを構成するときに必要になります。
11. [その他のアプリケーションに対するアクセス許可] で、[アプリケーションの追加] をクリックします。 [表示] ドロップダウンで [その他] を選択し、上のチェック マークをクリックします。TodoListService を見つけてクリックし、下のチェック マークをクリックしてアプリケーションを追加します。[委任されたアクセス許可] ドロップダウンで [TodoListService へのアクセス (Access TodoListService)] を選択し、構成を保存します。


## 手順 4. iOS ネイティブ クライアントのサンプル コードをダウンロードする

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## 手順 5. iOS 向け ADAL をダウンロードして XCode ワークスペースに追加する

#### iOS 向け ADAL のダウンロード

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### ワークスペースのライブラリへのインポート

XCode で、プロジェクト ディレクトリを右クリックして、[ファイルを iOS サンプルに追加 (Add files to iOS Sample)] をクリックします。

プロンプトが表示されたら、iOS 向け ADAL の複製先ディレクトリを選択します。

#### リンク フレームワークとライブラリに、libADALiOS.a を追加します。

[リンク フレームワークとライブラリ (Linked Frameworks and Libraries)] の下の [追加] ボタンをクリックして、インポートされたフレームワークからライブラリ ファイルを追加します。

プロジェクトを構築して、すべてが正しくコンパイルされていることを確認します。


## 手順 6. settings.plist ファイルを Web API の情報で構成する

settings.plist ファイルは、"Supporting Files" の下にあります。これには以下の情報が含まれています。

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

.plist ファイル内の情報を Web API の設定に置き換えます。

##### 注

現在の既定は、[Node.js 用の Azure Active Directory のサンプル REST API サービス](https://github.com/AzureADSamples/WebAPI-Nodejs)で動作するように設定されています。ただし、Web API のクライアント ID を指定する必要があります。独自の API を実行している場合は、必要に応じてエンドポイントを更新する必要があります。

## 手順 7. アプリケーションを構築して実行する

REST API エンドポイントに接続することができ、Azure Active Directory アカウントからの資格情報の入力を要求するプロンプトが表示される必要があります。

その他のリソースについては、[GitHub の AzureAD サンプル](https://github.com/AzureAdSamples)、および [Azure.com](http://azure.microsoft.com/documentation/services/active-directory/) にある Azure AD に関するドキュメントを参照してください。

<!---HONumber=58-->