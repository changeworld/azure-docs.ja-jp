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
	ms.date="02/11/2015" 
	ms.author="sdanie"/>

# Azure API Management の使用

このガイドでは、API Management の基本的な使用方法を簡単に説明し、実際に API を呼び出してみます。

## このトピックの内容

-   [API Management インスタンスの作成][API Management インスタンスの作成]
-   [API の作成][API の作成]
-   [操作の追加][操作の追加]
-   [成果物に新しい API を追加する][成果物に新しい API を追加する]
-   [API を含む成果物のサブスクライブ][API を含む成果物のサブスクライブ]
-   [開発者ポータルから操作を呼び出す][開発者ポータルから操作を呼び出す]
-   [分析結果の表示][分析結果の表示]
-   [次のステップ][次のステップ]

## <a name="create-service-instance"> </a>API Management インスタンスの作成

> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

API Management を使用するにあたって最初に行うことは、サービス インスタンスの作成です。[管理ポータル][管理ポータル]にログインし、**[新規]**、**[アプリ サービス]**、**[API Management]**、**[作成]** をクリックします。

![API Management new instance][API Management new instance]

**[URL]** に、サービス URL に使用する一意のサブドメイン名を指定します。

サービス インスタンスの **[料金レベル]**、**[サブスクリプション]**、**[リージョン]** を選択します。このチュートリアルは、いずれの料金レベルでもかまいません。それらを選択したら、次に進むボタンをクリックします。

![New API Management service][New API Management service]

**[会社名]** に「**Contoso Ltd.**」と入力し、管理者の電子メール フィールドに電子メール アドレスを入力します。

> API Management システムからの通知には、この電子メール アドレスが使用されます。詳細については、「[通知の構成][通知の構成]」を参照してください。

チェック ボックスをクリックすると、サービス インスタンスが作成されます。

![New API Management service][1]

![New API Management service][2]

サービス インスタンスが作成されたら、API を作成します。

## <a name="create-api"> </a>API の作成

API は、クライアント アプリケーションから呼び出すことのできる一連の操作で構成されます。API の操作は、既存の Web サービスに引き渡されます。

それぞれの API Management サービス インスタンスには、サンプルの Echo API があらかじめ構成されています。インスタンスに送信された入力をそのまま返すものです。このサンプルを使用するには、何らかの HTTP 動詞を呼び出します。すると、自分が送ったヘッダーと本文がそのまま戻り値として得られます。

このチュートリアルでは、<http://echoapi.cloudapp.net/api> の Web サービスを使用して、**My Echo Service** という新しい API を API Management に作成します。

API の作成と構成は、Azure の管理ポータルから API Management コンソールにアクセスして行います。API Management コンソールには、API Management サービスの Azure ポータルの **[管理コンソール]** をクリックしてアクセスします。

![New API Management console][New API Management console]

**My Echo API** を作成するには、左側の **[API Management]** メニューで **[API]** をクリックし、**[API の追加]** をクリックします。

![Create API][Create API]

![Add new API][Add new API]

新しい API を構成するには、次の 3 つのフィールドを使用します。

-   **[Web API タイトル]** ボックスに「**My Echo API**」と入力します。**[Web API タイトル]** は、API に対する一意のわかりやすい名前です。開発者ポータルと管理ポータルには、この名前が表示されます。
-   **[Web サービスの URL]** に「**<http://echoapi.cloudapp.net/api>**」と入力します。**[Web サービスの URL]** は、API が実装されている HTTP サービスの URL です。要求は、API Management によってこのアドレスに転送されます。
-   **[Web API URL サフィックス]** に「**myecho**」と入力します。**[Web API URL サフィックス]** は、API Management サービスのベース URL に付加されます。ご利用の API は、共通のベース URL を持ち、その後ろに付加された一意のサフィックスによって識別されます。

**[保存]** をクリックすると API が作成されます。新しい API が作成されると、その API の概要ページが管理ポータルに表示されます。

![API summary][API summary]

API セクションには 4 つのタブがあります。**[概要]** タブには、API に関する基本的なメトリックと情報が表示されます。**[設定]** タブは、バックエンド サービスの認証資格情報など、API の構成を表示したり編集したりするときに使用します。**[操作]** タブは、API の操作を管理するときに使用します (この後の手順で使用します)。**[問題]** タブは、API を使用する開発者によって報告された問題を確認するときに使用します。

> サンプルの Echo API に認証は使用されていません。認証の構成の詳細については、「[Configure API settings (API 設定の構成)][Configure API settings (API 設定の構成)]」を参照してください。

API を作成して必要な設定を行ったら、API に操作を追加します。操作の定義は、受け取った要求の検証とドキュメントの自動生成に使用されます。

## <a name="add-operation"> </a>操作の追加

**[操作]** をクリックして、API の [操作] ウィンドウを表示します。まだ操作を追加していないため、ここには何も表示されません。

![Operations][Operations]

新しい操作を追加するには、**[操作の追加]** をクリックします。**[新しい操作]** ウィンドウが表示され、**[署名]** タブが既定で選択されます。

![Operation signature][Operation signature]

この例では、エコー サービスに GET 操作を指定します。**[署名]** タブのフィールドに次の値を入力します。

-   **[HTTP 動詞]** ボックスに「**GET**」と入力します。入力と同時に http 動詞の一覧が絞り込まれ **[GET]** を選択することができます。
-   **[URL テンプレート]** ボックスに「**/resource**」と入力します。
-   **[表示名]** ボックスに「**GET resource**」と入力します。
-   **[説明]** ボックスに「**A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).**」と入力します。この説明は、開発者がこの API を使用するとき、この操作のドキュメントを生成する用途に使用されます。

この操作のクエリ文字列パラメーターを構成するには、**[パラメーター]** をクリックします。この例には、2 つのクエリ文字列パラメーターがあります。クエリ パラメーターを追加するには、**[クエリ パラメーターの追加]** をクリックし、次の値を指定します。

1 つ目のクエリ パラメーターには、次の値を構成します。

-   **[名前]** ボックスに「**param1**」と入力します。
-   **[説明]** ボックスに「**A sample parameter that is required.**」と入力します。
-   **[型]** フィールドをクリックし、一覧から **[string]** を選択します。サポートされる型は、**string**、**number**、**boolean**、**dateTime** です。
-   **[値]** フィールドをクリックしてテキスト ボックスに「**sample**」と入力し、正符号をクリックして、既定値のテキストをパラメーターに追加します。既定のテキストを追加したら、**[値]** フィールドの外側をどこかクリックして、値の追加ウィンドウを閉じます。
-   **[必須]** チェック ボックスをオンにします。

2 つ目のクエリ パラメーターには、次の値を入力します。

-   **名前**: **param2**
-   **説明**: **Another sample parameter, set to not required.**
-   **型**: **number**

操作の結果として考えられるすべてのステータス コードの応答例を提供するようお勧めします。たとえば、ステータス コードごとに、対応する応答本文の例を複数 (サポートされるコンテンツ タイプごとに 1 つ) 設けます。このチュートリアルでは、**200 OK** という応答コードを追加します。

応答セクションの **[追加]** をクリックし、テキスト ボックスに「**200**」と入力してドロップダウン リストの項目を絞り込み、**[200 OK]** を選択します。

![Add response][Add response]

**[200 OK]** を選択すると、新しい応答コードが操作に追加され、応答ウィンドウが表示されます。**[説明]** ボックスに「**Returned in all cases.**」と入力します。

![Add response][3]

> さまざまな表現形式の応答を構成するには、**[表現の追加]** を使用します。詳細については、「[Responses (応答)][Responses (応答)]」を参照してください。

**[保存]** をクリックして、新しく構成した操作を API に追加します。

## <a name="add-api-to-product"> </a>成果物に新しい API を追加する

API 呼び出しを行うにあたって開発者はまず、成果物をサブスクライブする必要があります。API に対するアクセスは成果物によって提供され、成果物には、使用量クォータやレート制限などのアクセス制限が含まれている場合があります。このチュートリアル ステップでは、My Echo API を既存の成果物に追加します。

この API インスタンスで利用できる成果物を表示したり構成したりするには、左側の **[API Management]** メニューにある **[成果物]** をクリックします。

![Products][Products]

すべての API Management インスタンスは、2 つのサンプル成果物を既定で備えています。

-   **スターター**
-   **無制限**

このチュートリアルでは、**スターター**成果物を使用します。**[スターター]** をクリックすると、スターター成果物に関連付けられている API を含め、その設定が表示されます。

![Add API][Add API]

**[成果物への API の追加]** をクリックします。

![Add API][4]

**[My Echo API]** のチェック ボックスをオンにし、**[保存]** をクリックします。

![API added][API added]

以上で成果物への **My Echo API** の関連付けは終了です。開発者は成果物をサブスクライブして API を使い始めることができます。

> このチュートリアル ステップでは、事前構成され使用する準備が整っているスターター成果物を使用します。新しい成果物の作成と発行に関する具体的な手順については、「[How create and publish a product (成果物を作成して発行する方法)][How create and publish a product (成果物を作成して発行する方法)]」を参照してください。

## <a name="subscribe"> </a>API を含む成果物のサブスクライブ

開発者が API を呼び出すためには、その API へのアクセスを提供する成果物を事前にサブスクライブする必要があります。開発者は、開発者ポータルで成果物をサブスクライブすることができます。また、成果物に対する開発者のサブスクリプションを管理者が管理コンソールから追加することもできます。このチュートリアルで先ほど API Management インスタンスを作成しました。既定では、API Management インスタンスを作成したユーザーが管理者になります。**スターター**成果物のサブスクリプションを特定のアカウントに追加することが可能です。

このサービス インスタンスで開発者を表示したり構成したりするには、左側の **[API Management]** メニューにある **[開発者]** をクリックします。

![Developers][Developers]

サブスクリプションをはじめとするユーザーの設定を行うには、開発者の名前をクリックします。

> この例では、Clayton Gragg という名前の開発者にサブスクリプションを追加します。開発者アカウントが未作成である場合は、管理者アカウントでサブスクライブしてもかまいません。開発者アカウントの作成については、「[How to manage developer accounts in Azure API Management (Azure API Management で開発者アカウントを管理する方法)][How to manage developer accounts in Azure API Management (Azure API Management で開発者アカウントを管理する方法)]」を参照してください。

![Add subscription][Add subscription]

**[サブスクリプションの追加]** をクリックします。

![Add subscription][5]

**[スターター]** のチェック ボックスをオンにし、**[サブスクライブ]** をクリックします。

![Subscription added][Subscription added]

開発者アカウントのサブスクライブ後、成果物の API を呼び出すことができます。

## <a name="call-operation"> </a>開発者ポータルから操作を呼び出す

開発者ポータルには、API の操作を見てテストするための便利が環境が用意されており、操作を直接呼び出すことができます。このチュートリアル ステップでは、**My Echo API** に追加した Get メソッドを呼び出します。管理ポータルの右上にあるメニューから **[開発者ポータル]** をクリックします。

![Developer portal][Developer portal]

上部のメニューで **[API]** をクリックし、**[My Echo API]** をクリックして、利用できる操作を表示します。

![Developer portal][6]

操作を作成したときに追加した説明とパラメーターが、操作を使用する開発者のためのドキュメントとして表示されている点に注目してください。

**[GET Resource]** をクリックし、**[コンソールを開く]** をクリックします。

![Operation console][Operation console]

パラメーターの値を入力し、開発者キーを指定して、**[HTTP Get]** をクリックします。

![HTTP Get][HTTP Get]

操作を呼び出すと、バックエンド サービスの**要求された URL**、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

![Response][Response]

## <a name="view-analytics"> </a>分析結果の表示

**My Echo API** の分析結果を表示するには、開発者ポータルの右上にあるユーザー メニューから **[管理]** を選択して管理ポータルに戻ります。

![Manage][Manage]

管理ポータルの既定のビューはダッシュボードで、API Management インスタンスの概要が表示されます。

![Dashboard][Dashboard]

My Echo API のグラフの上にマウス ポインターを合わせると、特定の期間における API の使用量について具体的なメトリックが表示されます。

> グラフに線が表示されない場合は、開発者ポータルに戻って何か API を呼び出し、少し経ってから、再度ダッシュボードに切り替えてください。

![Analytics][Analytics]

**[詳細の表示]** をクリックすると、表示されたメトリックの拡大版を含む、API の概要ページが表示されます。

![Summary][Summary]

詳細なメトリックとレポートについては、左側の **[API Management]** メニューの **[分析]** をクリックします。

![Overview][Overview]

**[分析]** セクションには、次の 4 つのタブがあります。

-   **[概略]** には、全体的な使用量と正常性のメトリックのほか、開発者、成果物、API、操作のぞれぞれのトップが表示されます。
-   **[使用量]** には、API 呼び出しと帯域幅の詳しい状況 (地理的情報を含む) が表示されます。
-   **[正常性]** には、ステータス コード、キャッシュの成功率、応答時間、API とサービスの応答時間が表示されます。
-   **[アクティビティ]** には、開発者、成果物、API、操作ごとの特定のアクティビティを詳しく分析したレポートが表示されます。

## <a name="next-steps"> </a>次のステップ

-   「[Azure API Management の詳細な構成について][Azure API Management の詳細な構成について]」チュートリアルにあるその他のトピックもチェックしてください。

  [API Management インスタンスの作成]: #create-service-instance
  [API の作成]: #create-api
  [操作の追加]: #add-operation
  [成果物に新しい API を追加する]: #add-api-to-product
  [API を含む成果物のサブスクライブ]: #subscribe
  [開発者ポータルから操作を呼び出す]: #call-operation
  [分析結果の表示]: #view-analytics
  [次のステップ]: #next-steps
  [Azure の無料評価版サイト]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [管理ポータル]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [通知の構成]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Configure API settings (API 設定の構成)]: ../api-management-howto-create-apis/#configure-api-settings
  [Operations]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Add response]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [Responses (応答)]: ../api-management-howto-add-operations/#responses
  [Products]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [How create and publish a product (成果物を作成して発行する方法)]: ../api-management-howto-add-products
  [Developers]: ./media/api-management-get-started/api-management-developers.png
  [How to manage developer accounts in Azure API Management (Azure API Management で開発者アカウントを管理する方法)]: ../api-management-howto-create-or-invite-developers/
  [Add subscription]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Subscription added]: ./media/api-management-get-started/api-management-subscription-added.png
  [Developer portal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Manage]: ./media/api-management-get-started/api-management-manage-menu.png
  [Dashboard]: ./media/api-management-get-started/api-management-dashboard.png
  [Analytics]: ./media/api-management-get-started/api-management-mouse-over.png
  [Summary]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Overview]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Azure API Management の詳細な構成について]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 
