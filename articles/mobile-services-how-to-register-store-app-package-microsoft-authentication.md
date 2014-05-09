<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="共有アクセス署名、パート 1" pageTitle="Windows ストア アプリケーション パッケージを Microsoft 認証に登録する" metaKeywords="" description="Azure のモバイル サービス アプリケーションで Microsoft 認証に Windows ストア アプリケーションを登録する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="Windows ストア アプリケーション パッケージを Microsoft 認証に登録する" authors="glenga" solutions="" manager="" editor="" />

# Windows ストア アプリケーション パッケージを Microsoft 認証に登録する

Azure のモバイル サービスでは、クライアント主導とサーバー主導の両方の認証方法がサポートされています。サーバー主導の認証では、Microsoft アカウントなどの ID プロバイダーを使用します。サーバー主導の認証で、アプリケーションをモバイル サービスに登録せずに Microsoft アカウントを使用すると、認証が必要になるたびに、ユーザーは資格情報を入力するように求められます。アプリケーションを登録すると、Microsoft アカウントのログイン資格情報がキャッシュされて認証に使われるようになるので、ユーザーが再度入力を求められることがありません。このトピックでは、認証に Azure のモバイル サービスを使用するときの Microsoft アカウントのログイン エクスペリエンスを向上させるために Windows ストア アプリケーション パッケージを登録する方法について説明します。

Windows デバイスで Live Connect を使用したシングル サインオン エクスペリエンスを提供するために、クライアント主導の認証を使用することができます。Live Connect API を使用する場合は、このトピックの手順を行う必要はありません。詳細については、「[Live Connect シングル サインオンによる Windows ストア アプリケーションの認証]」を参照してください。

アプリケーションを登録すると、アプリケーションにプッシュ通知を送信することもできます。アプリケーションのためにチュートリアル「[プッシュ通知の使用]」を既に実行した場合、このトピックの手順は既に終わっています。

<div class="dev-callout"><b>注</b>
	<p><em>Visual Studio 2013 Preview</em> には、Windows ストア アプリ パッケージをモバイル サービスに簡単に登録できる新機能が含まれています。詳細については、Windows デベロッパー センターで<a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">モバイル サービスのプッシュ通知の追加のクイック スタート</a>に関するページを参照してください。</p>
</div>

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

アプリケーション パッケージの登録後、<a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出すときに、<em>useSingleSignOn</em> に値 <strong>true</strong> を指定してください。そうすることで、Microsoft アカウントを使用する場合に、向上したログイン エクスペリエンスをユーザーに提供できます。

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[Live Connect シングル サインオンによる Windows ストア アプリケーションの認証]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[ユーザー認証の使用 C#]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[ユーザー認証の使用 JavaScript]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/

