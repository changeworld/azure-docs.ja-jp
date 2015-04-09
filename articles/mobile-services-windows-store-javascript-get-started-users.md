<properties 
	pageTitle="認証の使用 (JavaScript) | モバイル デベロッパー センター" 
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア JavaScript アプリのユーザーを認証する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証と承認されると、ユーザー ID 値が表示されます。  

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、Mobile Services を構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]
4. [クライアント側で認証トークンを保存する]

このチュートリアルは、Mobile Services のクイック スタートに基づいています。先にチュートリアル「[Mobile Services の使用]」を完了している必要があります。 

>[AZURE.NOTE]このチュートリアルでは、さまざまな ID プロバイダーを使用した、モバイル サービスによって管理される認証フローについて説明します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証による Live Connect を使用して、Windows Phone アプリでシングル サインオンできるようにするには、トピック [Live Connect を使用した Windows Phone アプリへのシングル サインオン] を参照してください。クライアントによって管理される認証を使用することにより、アプリは ID プロバイダーが保持する追加のユーザー データにアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出すことにより、同じユーザー データをモバイル サービスで取得できます。詳細については、[この記事](http://go.microsoft.com/fwlink/p/?LinkId=506605) を参照してください。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(省略可能)「<a href="/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Windows ストア アプリ パッケージを Microsoft 認証に登録する</a>」の手順を実行します。</p>

    
	<p>この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。</p>
</li>
</ol>
これで、Mobile Services とアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Visual Studio 2012 Express for Windows 8 で、チュートリアル「<a href="/develop/mobile/tutorials/get-started/">Mobile Services の使用</a>」を実行したときに作成したプロジェクトを開きます。</p></li> 
<li><p>F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションが Mobile Services にアクセスしようとしても、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>クライアント側で認証トークンを保存する

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>次のステップ

次のチュートリアル「[モバイル サービス ユーザーのサービス側の承認][スクリプトを使用したユーザーの承認]」では、認証されたユーザーに基づいて Mobile Services によって提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。 


<!-- Anchors. -->
[アプリケーションを認証に登録し、Mobile Services を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[クライアント側で認証トークンを保存する]: #tokens
[次のステップ]:#next-steps


<!-- URLs. -->
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: mobile-services-windows-store-javascript-single-sign-on.md
[Mobile Services の使用]: mobile-services-windows-store-get-started.md
[データの使用]: mobile-services-windows-store-javascript-get-started-data.md
[認証の使用]: mobile-services-windows-store-javascript-get-started-users.md
[プッシュ通知の使用]: mobile-services-windows-store-javascript-get-started-push.md
[スクリプトを使用したユーザーの承認]: mobile-services-windows-store-javascript-authorize-users-in-scripts.md

[Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /develop/mobile/how-to-guides/register-windows-store-app-package

<!--HONumber=49-->