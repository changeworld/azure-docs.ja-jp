<properties pageTitle="認証の使用 (Appcelerator) | モバイル デベロッパー センター" metaKeywords="" description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# モバイル サービスでの認証の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、アプリケーションから Windows Azure Mobile Services のユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、Mobile Services を構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを完了するには、Appcelerator Titanium Studio 3.2.1 またはそれ以降、iOS 7.0 またはそれ以降、さらに Android 4.3 またはそれ以降が必要です。

##<a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	Appcelerator Titanium Studio で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4.	[実行] をクリックしてプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。
    
    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、TodoItem テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

1.	プロジェクト ファイル index.js を開き、テーブル イベント Lister メソッドで、`case 2:` を探します。

    アプリケーションでは、利用可能な ID プロバイダーをユーザーに要求するか、任意の ID プロバイダーを自動的に提供できます。

2.	利用可能な ID プロバイダーをすべて提供するには、次のコードを使用します。

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.	特定の ID プロバイダーを提供するには、次のコードを使用します。

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

>[WACOM.NOTE] Google 以外の ID プロバイダーを使用している場合は、**authorizeClient** に渡される値を *microsoftaccount*、*facebook*、*twitter*、*windowsazureactivedirectory* のいずれかにします。

4.	[実行] をクリックして iPhone シミュレーターまたは Android エミュレーターでアプリケーションを開始し、[ログインするプロバイダー] オプションをクリックして ID プロバイダーでログオンします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。


<!-- Anchors. -->

[アプリケーションを認証に登録し、Mobile Services を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[Mobile Services の使用]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started

<!--HONumber=35.1-->
