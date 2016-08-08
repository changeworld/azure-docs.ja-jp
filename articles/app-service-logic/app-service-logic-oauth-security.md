<properties
	pageTitle="SaaS コネクタと API Apps での OAUTH セキュリティ | Azure"
	description="Azure App Service のコネクタと API Apps における OAUTH セキュリティのほか、マイクロサービス アーキテクチャ、SaaS について説明します。"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="mandia"/>


# SaaS コネクタでの OAUTH セキュリティについて

>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Facebook、Twitter、DropBox など、サービスとしてのソフトウェア (SaaS) の多くは、ユーザーが OAUTH プロトコルを使用して認証する必要があります。Logic Apps からこれらの SaaS コネクタを使用する場合、Logic Apps デザイナーで [承認] をクリックするだけという、シンプルなユーザー エクスペリエンスが用意されています。**承認**を実行する場合、まだサインインしていない場合はサインインし、SaaS のサービスへの接続に同意するよう求められます。同意して承認すると、Logic Apps からこれらの SaaS サービスにアクセスできます。

## 独自の SaaS アプリの作成
これらの SaaS サービスで既にアプリケーションを作成して登録しているために、このようなシンプルなエクスペリエンスが可能になります。場合によっては、独自のアプリケーションを登録し、使用することがあります。たとえば、カスタム アプリケーションで、これらの SaaS コネクタを使用する場合に必要となります。この例では DropBox コネクタを使用しますが、そのプロセスは、OAUTH に依存するすべてのコネクタで同じです。

Logic Apps の場合でも、既定のアプリケーションを使用する代わりに、独自のアプリケーションを使用できます。[承認] ボタンを使用して接続に失敗した場合、独自のアプリケーションを作成してみてください。Twitter コネクタの場合の手順を次に示します。

1. Azure プレビュー ポータルで、Twitter コネクタを開きます。**[参照]**、**[API Apps]** の順に移動します。[Twitter コネクタ] を選択します。![][1]

2. **[設定]**、**[認証]** の順に選択します。![][2]

3. **[リダイレクト URI]** の値をコピーします。![][3]

4. [[Twitter]](http://apps.twitter.com)、**[新しいアプリの作成]** の順に移動します。**[コールバック URL]** プロパティで、Twitter コネクタからコピーした **[リダイレクト URI]** の値を貼り付けます。![][4]
5. 独自の Twitter アプリを作成する場合は、**[キーとアクセス トークン]** を選択します。これらの値をコピーします。
6. Twitter コネクタの認証設定で、**[クライアント ID]** と **[クライアント シークレット]** プロパティにこれらの値を貼り付けます。![][5]
7. コネクタの設定を保存します。

これで、Logic Apps からコネクタを使用できるようになります。Logic Apps からこのコネクタを使用する場合、既定のアプリケーションではなく、独自のアプリケーションを使用します。

> [AZURE.NOTE] アプリを既に承認している場合は、アプリを再度承認しなければならない場合があります。


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=AcomDC_0727_2016-->