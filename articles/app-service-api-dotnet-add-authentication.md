<properties 
	pageTitle="Azure API アプリの保護" 
	description="Visual Studio を使用して Azure API アプリを保護する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# API アプリの保護: Azure Active Directory またはソーシャル プロバイダーの認証の追加

## 概要

[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルでは、**すべてのユーザーが利用できる**アクセス レベルで API アプリをデプロイしました。このチュートリアルでは、認証されたユーザーのみがアクセスできるように API アプリを保護する方法を示します。

以下の手順を実行します。

- API アプリを呼び出して、動作していることを確認します。
- API アプリに認証ルールを適用します。
- API アプリをもう一度呼び出して、認証されていない要求が拒否されることを確認します。
- 構成したプロバイダーにログインします。
- API アプリをもう一度呼び出して、認証されたアクセスが機能することを確認します。

## 前提条件

このチュートリアルでは、[API アプリの作成](app-service-dotnet-create-api-app.md)に関するページで作成し、[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するページでデプロイした API アプリを使用します。

## ブラウザーを使用した API アプリの呼び出し 

API アプリにパブリックにアクセスできることを確認する最も簡単な方法は、ブラウザーから呼び出すことです。

1. ブラウザーで、[Azure ポータル]に移動します。

3. ホーム ページで **[参照]、[API Apps]** の順にクリックし、次に、保護する API アプリの名前をクリックします。

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. **[API アプリ]** ブレードで、API アプリを呼び出すブラウザーのウィンドウを開くための **URL** をクリックします。

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. ブラウザーのアドレス バーに表示されている URL に `/api/contacts/get/` を追加します。

	たとえば、次の URL が API アプリの URL だとします。

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	最終的な URL は次のようになります。

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	API 呼び出しの処理方法は、ブラウザーによって異なります。次の図は、Chrome ブラウザーからの呼び出しが成功したようすを示しています。

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. 使用した URL は、このチュートリアルの後半でもう一度使用するため、保存してください。

## API アプリの保護

API アプリをデプロイしたときに、リソース グループにデプロイしました。Web アプリと他の API アプリを同じリソース グループに追加することができます。リソース グループ内の各 API アプリには、次の 3 種類のアクセス レベルのいずれかを設定することができます。
<!--todo: さまざまなアクセス設定を示す図-->

- **パブリック (匿名)** - すべてのユーザーがログインせずにリソース グループの外部から API アプリを呼び出すことができます。
- **パブリック (認証済み)** - 認証されたユーザーのみがリソース グループの外部から API アプリを呼び出すことができます。
- **内部** - 同一リソース グループに属する他の API アプリまたは Web アプリのみが API アプリを呼び出すことができます。

Visual Studio でリソース グループが作成されたときに、*ゲートウェイ*も作成されました。ゲートウェイは、リソース グループ内の API アプリ宛てのすべての要求を処理する、専用の Web アプリです。

[Azure ポータル]でリソース グループのブレードに移動すると、図の API アプリとゲートウェイを確認できます。

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

認証された要求のみを受け入れるように API アプリを構成するには、API アプリのアクセス レベルを**パブリック (認証済み)** に設定します。さらに、Azure Active Directory、Google、Facebook などのプロバイダーからの認証を必要とするようにゲートウェイを構成します。

1. 保護する API アプリの **[API アプリ]** ブレードに戻ります。

2. **[API アプリ]** ブレードで、**[設定]**、**[アプリケーションの設定]** の順にクリックします。

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. **[アプリケーションの設定]** ブレードで、**[アクセス レベル]** を **[パブリック (認証済み)]** に変更し、**[保存]** をクリックします。

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	これで、認証されていないアクセスから API アプリが保護されます。次に、ゲートウェイを構成して、使用する認証プロバイダーを指定する必要があります。

4. スクロールして左側の **[API アプリ]** ブレードに戻り、ゲートウェイへのリンクをクリックします。

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. **[ゲートウェイ]** ブレードで、**[設定]**、**[ID]** の順にクリックします。

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	**[ID]** ブレードからは、Azure Active Directory や他のさまざまなプロバイダーを使用して認証を構成する別のブレードに移動することができます。

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. 使用する ID プロバイダーを選択し、該当する記事の手順に従って、API アプリをそのプロバイダーで構成します。これらの記事はモバイル アプリ向けに書かれていますが、API アプリでも手順は同じです。一部の手順では、[以前のポータル]を使用する必要があります。 

 - [Microsoft アカウント](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Facebook ログイン](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Twitter ログイン](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Google ログイン](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

たとえば、次のスクリーン ショットは、Azure Active Directory の認証を設定した後の[以前のポータル] ページと [[Azure ポータル]] ブレードを示しています。

Azure ポータルでは、**[Azure Active Directory]** ブレードに、以前のポータルの [Azure Active Directory] タブで作成したアプリケーションの**クライアント ID** が表示されます。さらに、**[許可されたテナント]** に、Azure Active Directory テナント ("contoso.onmicrosoft.com" など) が表示されます。

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

以前のポータルでは、**[Azure Active Directory]** タブで作成したアプリケーションの **[構成]** タブに、Azure ポータルの **[Azure Active Directory]** ブレードの **[サインオン URL]**、**[アプリケーション ID/URI]**、および **[応答 URL]** が表示されます。

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

(この図の応答 URL には、同じ URL が 2 つ表示されています。1 つには `http:`、もう 1 つには `https:` が付いています)。

## 認証の動作の確認 

1. ブラウザーのウィンドウを開き、アドレス バーに、API アプリの `Get` メソッドを呼び出す URL を、先ほどと同様に入力します。

	今回は、API アプリへのアクセスを試みると、エラー メッセージが表示されます。

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. ブラウザーで、次のログイン URL に移動します。 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	たとえば、リソース グループに myfirstrg という名前を付け、Azure Active Directory 認証用にゲートウェイを構成している場合は、次のような URL になります。

    	http://myfirstrggateway.azurewebsites.net/login/aad

	先ほどの URL とは異なり、今回は API アプリの名前が含まれていないことに注目してください。ゲートウェイは API アプリではなく、ユーザーを認証しています。ゲートウェイは、リソース グループ内のすべての API アプリの認証を処理します。

	(サインインで問題が発生した場合は、プライベートまたは匿名でウィンドウを開いてみてください。)

3. ブラウザーにログイン ページが表示されたら資格情報を入力します。 
 
	Azure Active Directory のログインを構成している場合は、[以前のポータル]の [Azure Active Directory] タブで作成したアプリケーションの **[ユーザー]** タブに一覧表示されているユーザー名から、admin@contoso.onmicrosoft.com などいずれかを選んで使用します。

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. "ログインが完了しました" というメッセージが表示されたら、API アプリの Get メソッドの URL をもう一度入力します。

	今回は認証済みであるため、呼び出しは成功します。ゲートウェイは、ユーザーが認証されたユーザーであると認識し、ユーザーの要求を API アプリに渡します。

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:架空の名前が示されている画像に置き換える-->

## Postman を使用した Post 要求の送信

ゲートウェイにログインすると、ゲートウェイは認証トークンを返信します。このトークンは、ゲートウェイを経由する外部のソースからのすべての要求に含まれている必要があります。ブラウザーで API にアクセスすると、通常、ブラウザーはトークンを cookie に格納し、後続のすべての呼び出しと共にこのトークンを API に送信します。

このため、バックグラウンドで行われている処理を確認することができます。このセクションでは、ブラウザー ツールを使用して Post 要求を作成および送信し、cookie から認証トークンを取得して HTTP ヘッダーに含めます。

以下の手順では、Chrome ブラウザーで Postman ツールを使用する方法を示していますが、任意の REST クライアント ツールとブラウザー開発者ツールでもこれと同じ操作を実行できます。

1. Chrome ブラウザーのウィンドウで、前のセクションで示した認証の手順を実行してから、開発者ツールを開きます (F12)。

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Chrome 開発者ツールの **[リソース]** タブで、ゲートウェイの cookie を検索します。**x-zumo-auth** cookie のすべての値をトリプルクリックして選択します。

	**注:** cookie のすべての値を必ず取得してください。ダブルクリックでは、最初の部分しか取得されません。

5. **x-zumo-auth** cookie の **[値]** を右クリックし、**[コピー]** をクリックします。

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Postman 拡張機能をまだ Chrome ブラウザーにインストールしていない場合は、インストールします。

6. Postman 拡張機能を開きます。

7. 要求 URL のフィールドに、先ほど使用した、API アプリの Get メソッドの URL を入力しますが、末尾の `get/` は省略します。
 
		http://[apiappurl]/api/contacts
    
8. **[ヘッダー]** をクリックし、*x-zumo-auth* ヘッダーを追加します。クリップボードからトークンの値を **[値]** フィールドに貼り付けます。

9. *Content-Type* ヘッダーに値 *application/json* を追加します。

10. **form-data** をクリックし、*contact* キーを追加して、次の値を指定します。

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. [送信] をクリックします。

	API アプリは、応答 *201 Created* を返します。

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. この要求が認証トークンなしでは実行されないことを確認するために、認証ヘッダーを削除してから、もう一度 [送信] をクリックします。

	応答 *403 Forbidden* が返されます。

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## 次のステップ

ここまでは、Azure Active Directory またはソーシャル プロバイダーの認証を必要とすることによって Azure API アプリを保護する方法について説明しました。詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。 

[以前のポータル]: https://manage.windowsazure.com/
[Azure ポータル]: https://portal.azure.com/


<!--HONumber=49-->