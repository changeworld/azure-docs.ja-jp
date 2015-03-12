<properties 
	pageTitle="Azure API Management の使用" 
	description="API とその操作を作成する方法のほか、API Management の基本操作について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API Management の使用

このガイドでは、API Management の基本的な使用方法を簡単に説明し、実際に API を呼び出してみます。

> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][]を参照してください。

API Management を使用するにあたって最初に行うことは、サービス インスタンスの作成です。[管理ポータル][]にログインし、**[新規]**、**[アプリ サービス]**、**[API Management]**、**[作成]** をクリックします。

![API Management new instance][api-management-create-instance-menu]

**[URL]** に、サービス URL に使用する一意のサブドメイン名を指定します。

サービス インスタンスの **[サブスクリプション]** と **[リージョン]** を選択します。それらを選択したら、次に進むボタンをクリックします。

![New API Management service][api-management-create-instance-step1]

**[会社名]** に「**Contoso Ltd.**」と入力し、管理者の電子メール フィールドに電子メール アドレスを入力します。

>API Management システムからの通知には、この電子メール アドレスが使用されます。詳細については、「[通知の構成][]」を参照してください。

![New API Management service][api-management-create-instance-step2]

API Management サービス インスタンスは、2 つのレベルで利用できます (Developer と Standard)。既定では、新しい API Management サービス インスタンスは、Developer レベルを使用して作成されます。Standard レベルを選択するには、**[詳細設定]** チェックボックスをオンにし、次の画面で Standard レベルを選択します。

>Microsoft Azure は、API Management サービスを実行できる 2 つのレベルを提供します (Developer と Standard)。Developer レベル は、高可用性が重要ではない、開発、テスト、パイロット API プログラム用です。Standard レベルでより多くのトラフィックを処理するには、予約ユニット数を拡張できます。Standard レベルでは、最も高い処理能力とパフォーマンスを備えた、API Management サービスが提供されます。このチュートリアルは、いずれかのレベルを使用して実行できます。API Management レベルの詳細については、「[API Management の料金][]」を参照してください。

チェック ボックスをクリックすると、サービス インスタンスが作成されます。

![New API Management service][api-management-instance-created]

サービス インスタンスが作成されたら、API を作成します。

## <a name="create-api"> </a>API の作成

API は、クライアント アプリケーションから呼び出すことのできる一連の操作で構成されます。API の操作は、既存の Web サービスに引き渡されます。

それぞれの API Management サービス インスタンスには、サンプルの Echo API があらかじめ構成されています。これを使用して任意の HTTP 動詞を呼び出すと、自分が送ったヘッダーと本文がそのまま戻り値として得られます。このチュートリアルでは、Echo API のバックエンド Web サービスを使用して、**My Echo Service** という新しい API を API Management に作成します。

API の作成と構成は、Azure の管理ポータルから API Management コンソールにアクセスして行います。API Management コンソールには、API Management サービスの Azure ポータルの **[管理コンソール]** をクリックしてアクセスします。

![New API Management console][api-management-management-console]

**My Echo API** を作成するには、左側の **[API Management]** メニューの **[API]** をクリックし、**[API の追加]** をクリックします。

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

新しい API を構成するには、次の 3 つのフィールドを使用します。

-	**[Web API タイトル]** ボックスに「**My Echo API**」と入力します。**[Web API タイトル]** は、API に対する一意のわかりやすい名前です。開発者ポータルと管理ポータルには、この名前が表示されます。
-	**[Web サービスの URL]** に「**http://echoapi.cloudapp.net/api**」と入力します。**[Web サービスの URL]** は、API が実装されている HTTP サービスの URL です。要求は、API Management によってこのアドレスに転送されます。
-	**[Web API URL サフィックス]** に「**myecho**」と入力します。**[Web API URL サフィックス]** は、API Management サービスのベース URL に付加されます。ご利用の API は、共通のベース URL を持ち、その後ろに付加された一意のサフィックスによって識別されます。
-	**Web API の URL スキーム**により、API へのアクセスに使用できるプロトコルが決まります。既定では、HTTP が指定されます。

**[保存]** をクリックすると API が作成されます。新しい API が作成されると、その API の概要ページが管理ポータルに表示されます。

![API summary][api-management-new-api-summary]


>サンプルの Echo API に認証は使用されていません。認証の構成の詳細については、「[Configure API settings (API 設定の構成)][]」を参照してください。


## <a name="add-operation"> </a>操作の追加

**[操作]** をクリックして、API の [操作] ウィンドウを表示します。操作の定義は、受け取った要求の検証とドキュメントの自動生成に使用されます。まだ操作を追加していないため、ここには何も表示されません。

![Operations][api-management-myecho-operations]

新しい操作を追加するには、**[操作の追加]** をクリックします。**[新しい操作]** ウィンドウが表示され、**[署名]** タブが既定で選択されます。

![Operation signature][api-management-operation-signature]

この例では、エコー サービスに GET 操作を指定します。**[署名]** タブのフィールドに次の値を入力します。

-	**[HTTP 動詞]** ボックスに「**GET**」と入力します。入力と同時に http 動詞の一覧が絞り込まれ **[GET]** を選択することができます。
-	**[URL テンプレート]** ボックスに「**/resource**」と入力します。
-	**[表示名]** ボックスに「**GET resource**」と入力します。
-	「**A demonstration of a GET call on a sample resource.It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).**」と **[説明]** ボックスに入力します。この説明は、開発者がこの API を使用するとき、この操作のドキュメントを生成する用途に使用されます。

この操作のクエリ文字列パラメーターを構成するには、**[パラメーター]** をクリックします。クエリ パラメーターを追加するには、**[クエリ パラメーターの追加]** をクリックし、次の値を指定します。

-	**[名前]** ボックスに「**param1**」と入力します。
-	**[説明]** ボックスに「**A sample parameter that is required.**」と入力します。
-	**[型]** フィールドをクリックし、一覧から **[string]** を選択します。サポートされる型は、**string**、**number**、**boolean**、**dateTime** です。
-	**[値]** フィールドをクリックしてテキスト ボックスに「**sample**」と入力し、正符号をクリックして、既定値のテキストをパラメーターに追加します。既定のテキストを追加したら、**[値]** フィールドの外側をどこかクリックして、値の追加ウィンドウを閉じます。
-	**[必須]** チェック ボックスをオンにします。

**[保存]** をクリックして、新しく構成した操作を API に追加します。


## <a name="add-api-to-product"> </a>成果物に新しい API を追加する

API 呼び出しを行うにあたって開発者はまず、成果物をサブスクライブする必要があります。API に対するアクセスは成果物によって提供され、成果物には、使用量クォータやレート制限などのアクセス制限が含まれている場合があります。このチュートリアル ステップでは、My Echo API を既存の成果物に追加します。

この API インスタンスで利用できる成果物を表示したり構成したりするには、左側の **[API Management]** メニューにある **[成果物]** をクリックします。

![Products][api-management-list-products]

すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

-	**スターター**
-	**無制限**

このチュートリアルでは、**スターター**成果物を使用します。**[スターター]** をクリックすると、スターター成果物に関連付けられている API を含め、その設定が表示されます。

![Add API][api-management-add-api-to-product]

**[成果物への API の追加]** をクリックします。

![Add API][api-management-add-myechoapi-to-product]

**[My Echo API]** のチェック ボックスをオンにし、**[保存]** をクリックします。

![API added][api-management-api-added-to-product]

以上で成果物への **My Echo API** の関連付けは終了です。開発者は成果物をサブスクライブして API を使い始めることができます。

>このチュートリアル ステップでは、事前構成され使用する準備が整っている**スターター**成果物を使用しています。新しい成果物の作成と発行に関する具体的な手順については、「[成果物を作成して発行する方法][]」を参照してください。

Administrator ユーザーには、すべての成果物に対するサブスクリプションが自動的に設定されます。Administrator ユーザーは、サブスクリプションによってアクセスが提供される API にアクセスできます。したがって、呼び出しを実行する前に、作成されたばかりの成果物を手動でサブスクライブする必要はありません。

## <a name="call-operation"> </a>開発者ポータルから操作を呼び出す

開発者ポータルには、API の操作を見てテストするための便利が環境が用意されており、操作を直接呼び出すことができます。このチュートリアル ステップでは、**My Echo API** に追加した Get メソッドを呼び出します。管理ポータルの右上にあるメニューから **[開発者ポータル]** をクリックします。

![Developer portal][api-management-developer-portal-menu]

上部のメニューで **[API]** をクリックし、**[My Echo API]** をクリックして、利用できる操作を表示します。

![Developer portal][api-management-developer-portal-myecho-api]

操作を作成したときに追加した説明とパラメーターが、操作を使用する開発者のためのドキュメントとして表示されている点に注目してください。

**[GET Resource]** をクリックし、**[コンソールを開く]** をクリックします。 

![Operation console][api-management-developer-portal-myecho-api-console]

パラメーターの値を入力し、開発者キーを指定して、**[HTTP Get]** をクリックします。

€

操作を呼び出すと、バックエンド サービスの**要求された URL**、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>次のステップ

-   ポリシーの構成
-   開発者ポータルのカスタマイズ
-   API Inspector を使用した呼び出しのトレース

[Azure 無料評価版]: http://azure.microsoft.com/pricing/free-trial/

[API Management インスタンスの作成]: #create-service-instance
[API の作成]: #create-api
[操作の追加]: #add-operation
[成果物に新しい API を追加する]: #add-api-to-product
[API を含む成果物のサブスクライブ]: #subscribe
[開発者ポータルから操作を呼び出す]: #call-operation
[分析結果の表示]: #view-analytics
[次のステップ]: #next-steps

[API 設定の構成]: ../api-management-howto-create-apis/#configure-api-settings
[構成の通知]: ../api-management-howto-configure-notifications
[応答]: ../api-management-howto-add-operations/#responses
[成果物を作成して発行する方法]: ../api-management-howto-add-products
[API の詳細な構成について]: ../api-management-get-started-advanced
[API Management の料金]: http://azure.microsoft.com/pricing/details/api-management/
[管理ポータル]: https://manage.windowsazure.com/

[ポリシーの構成]: ../api-management-howto-policies
[開発者ポータルのカスタマイズ]: ../api-management-customize-portal
[API Inspector を使用した呼び出しのトレース]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
