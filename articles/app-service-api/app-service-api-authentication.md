<properties
	pageTitle="Azure App Service での API Apps の認証と承認 | Microsoft Azure"
	description="Azure App Service が API Apps 向けに提供している認証サービスと承認サービスについて説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Azure App Service での API Apps の認証と承認

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要 

この記事では、Azure App Service の API Apps における認証処理と承認処理で利用できる選択肢について説明します。

次の図は、App Service の認証の鍵となるいくつかの特性を示しています。

* 受信した API 要求の前処理を行います。認証処理をどこまで独自のコードで行うかに関して、ある程度選択の自由がここで生まれます。 
* 認証プロバイダーとして、Azure Active Directory、Facebook、Google、Twitter、Microsoft Account の 5 つがサポートされます。
* エンド ユーザーとサービス プリンシパルの両方の認証に対応します。 
* API Apps、Web Apps、Mobile Apps のいずれについても同じ処理が行われます。

![](./media/app-service-api-authentication/api-apps-overview.png)

## 受信要求の前処理

App Service は、API アプリに対する匿名 HTTP 要求の到達を禁止するか、またはトークンを持った HTTP 要求を認証したうえで API アプリへの到達を許可することができます。API アプリに対する構成としては、次の 3 つの選択肢があります。

1. 認証済みの要求のみ API アプリへの到達を許可する。

	ブラウザーから匿名の要求を受信した場合、その要求は、App Service によってログオン ページにリダイレクトされます。

	この方法がうまく機能するためには、使用する認証プロバイダー (Google、Twitter など) があらかじめ決まっていることが必要です。その場合、ログオン処理を App Service で行うように構成することができます。または、独自の URL を指定しておき、そこに匿名の要求を App Service からリダイレクトさせる方法もあります。そのうえで、認証プロバイダーをユーザーが選択できるようにします。

	この選択肢をアプリで採用した場合、認証コードを自分で記述する必要は一切ありません。また、最も重要な要求は HTTP ヘッダー内に指定されているため承認処理も単純です。

2. すべての要求に API アプリへの到達を許可したうえで、認証済みの要求を検証し、HTTP ヘッダー内の認証情報を受け渡しする。

	この選択肢では、匿名の要求を柔軟に処理することができ、ごく一般的な要求にアクセスするコードが記述しやすくなります。1 つ目の選択肢とは異なり、匿名ユーザーに API の利用を禁止する場合は、独自にコードを記述する必要があります。

3. すべての要求に API への到達を許可し、要求に含まれる認証情報に対して何も実行しない。

	この選択肢を採用した場合、認証と承認に伴う一切の処理をアプリケーション コードに委ねることになります。

どの選択肢を採用するかは、[Azure ポータル](https://portal.azure.com/)の **[認証/承認]** ブレードで選びます。

![](./media/app-service-api-authentication/authblade.png)

1 つ目と 2 つ目の選択肢については、**[App Service 認証]** をオンにし、**[要求が認証されなかったときに実行する処理]** ボックスの一覧の **[ログイン]** または **[要求を許可 (何もしない)]** を選択します。**[ログイン]** を選択した場合は、認証プロバイダーを選んで、その構成を行う必要があります。

![](./media/app-service-api-authentication/actiontotake.png)
 
## 言語を選ばない

App Service 認証処理は、要求が API アプリに到達する前の時点で行われます。つまり、認証機能を利用する側の API アプリは、どのような言語、どのようなフレームワークで作成されていてもかまいません。API の開発には、ASP.NET、Java、Node.js のほか、App Service でサポートされるあらゆるフレームワークを使用できます。

App Service は、HTTP 要求の Authorization ヘッダーで JWT トークンを受け渡しします。コードは、その作成に使用された言語やフレームワークに関係なく、必要な情報をトークンから取得することができます。また、App Service では、いくつかの特殊なヘッダーを設定することによって、ごく一般的に使用される要求にも簡単にアクセスすることができます。その例を次に示します。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
.NET API では `Authorize` 属性を使用できます。要求の情報は .NET のクラスで自動的に設定されるため、要求に基づくコードをすぐに記述して、きめ細かな承認処理を行うことができます。

## サービス プリンシパルの認証

App Service 認証は、内部的なシナリオ (API アプリから別の API アプリを呼び出すなど) で利用することもできます。このシナリオで認証に使用するのは、エンド ユーザーの資格情報ではなくサービス アカウントの資格情報です。サービス アカウントは*サービス プリンシパル*とも呼ばれ、このようなアカウントでの認証は、サービス間シナリオとも呼ばれます。

内部的なシナリオでは、呼び出し先の API アプリを Azure Active Directory で保護し、その API アプリを呼び出すときに AAD サービス プリンシパル承認トークンを渡すことができます。そのトークンは、クライアント ID とクライアント シークレットを AAD アプリケーションから渡すことによって要求することができます。Mobile Services Zumo トークンの処理で使用されていたような Azure 専用の特殊なコードは不要です。このシナリオについて、ASP.NET API アプリを使った例が、[API Apps のサービス プリンシパル認証](app-service-api-dotnet-service-principal-auth.md)に関するチュートリアルで紹介されています。

App Service 認証を使わずにサービス間の認証を行う場合、クライアント証明書または基本認証を利用することができます。Azure のクライアント証明書の詳細については、「[Web Apps の TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

App Service ロジック アプリから API アプリへのサービス プリンシパル認証は特殊なケースであり、「[App Service でホストされたカスタム API のロジック アプリでの使用](../app-service-logic/app-service-logic-custom-hosted-api.md)」で説明されています。

## 詳細情報

Azure App Service での認証および承認サービスの詳細については、「[App Service 認証/承認の展開](/blog/announcing-app-service-authentication-authorization/)」を参照してください。

## 次のステップ

この記事では、App Service の API アプリにおける認証と承認の機能について説明しました。

引き続き ASP.NET と API Apps を使った一連のチュートリアルをご覧になる場合は、[App Service API Apps でのユーザー認証](app-service-api-dotnet-user-principal-auth.md)に関するページを参照してください。

Azure App Service における Node と Java の使用について詳しくは、[Node.js デベロッパー センター](/develop/nodejs/)と [Java デベロッパー センター](/develop/java/)を参照してください。

<!---HONumber=AcomDC_1210_2015-->