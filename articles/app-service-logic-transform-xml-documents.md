<properties 
	pageTitle="XML ドキュメントを変換する" 
	description="あるスキーマから別のスキーマへ XML ドキュメントを変換する方法について説明します。" 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2015"
	ms.author="anuragdalmia"/>

#XML ドキュメントを変換する


## BizTalk 変換 API アプリの概要
簡単に言うと、BizTalk 変換 API アプリは、1 つの形式から別の形式へデータを変換します。たとえば、注文書から出荷先住所と請求先住所を取得して、請求書ドキュメントに挿入する場合などです。または、受信メッセージに *YearMonthDay* 形式で現在の日付が含まれる場合、*MonthDayYear* 形式に日付の書式設定を変更する必要があります。

Microsoft Azure App Service の変換 API プリを使用してこれを実行できます。変換またはマップは、送信元 XML スキーマ (入力) と送信先 XML スキーマ (出力) で構成されます。さまざまな組み込み関数を使用すると、文字列操作、条件付き割り当て、算術式、日時の書式設定や、ループ構造の操作などで、データを操作および制御できます。

マップは、[Microsoft Azure BizTalk Services SDK](http://www.microsoft.com/download/details.aspx?id=39087) を使用して Visual Studio 内で作成します。マップの作成とテストが終了したら、マップ (.trfm) を BizTalk 変換 API アプリへアップロードします。

他には次の機能があります。

- マップで作成される変換は、あるドキュメントから別のドキュメントに名前と住所をコピーするなど、単純な内容になります。または、設定不要なマップ操作を使用して、より複雑な変換を作成することができます。
- 文字列、日時の関数などの複合的なマップ操作や関数をすぐに使用できます。
- スキーマ間でデータを直接コピーできます。BizTalk マッパーを使用すれば、この操作は送信元スキーマ内の要素を送信先スキーマの対応する部分につなぐ線を描画するくらい簡単です。
- マップを作成しているとき、作成したすべてのリレーションシップやリンクなど、マップがグラフィカルに表示されます。
- **[マップのテスト]** 機能を使用してサンプル XML メッセージを追加します。クリックするだけで、作成したマップをテストして、生成された出力を参照できます。
- 既存の Azure BizTalk Services マップ (.trfm) をアップロードし、変換 API のアプリを最大限まで活用してください。
- XML 形式のサポートが含まれます。


## 新しい BizTalk 変換 API アプリを作成する

1.	Azure ポータルにサインインし、スタート画面 (ホーム ページ) に移動します。

2.	**[新規]**、**[Web + モバイル]**、**[Azure Marketplace]**、**[API Apps]** の順に選択します。

	   ![][1]
 
	または、スタート画面で **[Marketplace]** を選択し、使用可能な一覧から **[API Apps]** を選択します。

	   ![][2]
 
3.	「**変換**」と入力して BizTalk 変換を参照し、**[BizTalk 変換サービス]** を選択します。

	   ![][4]
 
4.	**[BizTalk 変換サービス]** ブレードで **[作成]** を選択します。

       ![][5]
 
5.	**[新しい API アプリ]** ブレードで、次の情報を入力して **[作成]** を選択します。

	- [名前] – 変換 API アプリの名前を指定します 
	- [App Service プラン] – 新しいアプリ サービス計画を選択または作成します 
	- [価格レベル] - このアプリが属する価格レベルを選択します 
	- [リソース グループ] – アプリを配置するリソース グループを選択または作成します 
	- [場所] - アプリをデプロイする地理的な場所を選択します
	
	   ![][6]

6.	**[作成]** を選択します。数分以内に、BizTalk 変換 API アプリが作成されます。


## コネクタ API アプリのスキーマをダウンロードする
API のアプリ概要ページから、SQL、SAP、SharePoint などのコネクタ用の XML スキーマをダウンロードできます。たとえば、特定の SAP コネクタ API アプリ用の XML スキーマをダウンロードする場合は、API アプリを参照して概要ページを開きます。**[スキーマのダウンロード]** を選択すると、SAP アクションに対応するすべてのスキーマが格納されている zip ファイルがコンピューターにダウンロードされます。このスキーマを使用して、Visual Studio でマップ (.trfm) を作成することができます。

   ![][14]


## マップを作成および追加する
変換またはマップは、無料でダウンロードできる [Microsoft Azure BizTalk Services SDK](http://www.microsoft.com/download/details.aspx?id=39087) を使用して Visual Studio で作成します。

マップの作成の詳細については、[Visual Studio でのマップの作成](http://aka.ms/createamapinvs)に関するページを参照してください。マップを作成し、実稼働の準備ができると、Azure の管理ポータルで作成した BizTalk 変換 API アプリにマップ (.trfm ファイル) を追加できます。

アップロード後にマップが変更された場合、更新したマップをアップロードして変換 API アプリの既存のマップと置き換えることができます。

1.	Azure の管理ポータル (画面の左側) で **[参照]** を選択し、**[API Apps]** を選択します。**[API Apps]** が表示されない場合、**[すべて]** を選択し、使用可能な一覧から **[API Apps]** を選択します。

	![][7]

2.	お使いの Azure サブスクリプションに作成されたすべての **API アプリ**の一覧が表示されます。

	![][8]

3.	前のセクションで作成した BizTalk 変換 API アプリを選択します。

4.	API アプリの構成ブレードが開きます。[コンポーネント] セクションで**マップ**を表示できます。

	![][9]

5.	**[マップ]** を選択して、マップの一覧と共に新しいブレードを開きます。

6.	上部にある**マップの追加**アイコンを選択し、**[マップの追加]** ブレードを開きます。

	![][10]

7.	ファイル アイコンを選択して、ローカル コンピューターのマップ ファイル (.trfm) を見つけます。

8.  **[OK]** を選択すると、新しいマップが作成されます。新しいマップがマップの一覧に表示されます。


## ロジック アプリで BizTalk 変換 API アプリを使用する
マップの作成とテストが終わると、すぐに使用可能な状態になります。ユーザーは新しいロジック アプリを作成できます (**[新規]**、**[Logic Apps]** の順に選択)。

1. BizTalk の変換は、Logic Apps 内のギャラリーの右側から使用できます。**BizTalk 変換サービス** ギャラリーから選択します。[変換] はフローに追加されています。

	![][11]

2. **変換**アクションを選択します。入力パラメーターが表示されます。

	![][12]

3. **変換**アクションの構成を完了するために、次のパラメーターを入力します。
		 
	- 入力 XML
		- 変換 API アプリのマップの送信元スキーマに準拠した有効な XML コンテンツを入力します。ここには、"Call RFC – SAP" や "Insert Into Table – SQL" などのロジック アプリの前のアクションの出力を入力することができます。
		
	- マップ名 (省略可能)
		- 変換 API アプリに、既にアップロードされている有効なマップ名を入力します。マップが入力されていない場合、マップは送信元スキーマに準拠している入力 XML に基づいて自動的に選択されます。

	![][13]

4. アクション "Output XML" の出力は、ロジックのアプリの後続のアクションで使用できます。

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png




<!--HONumber=54-->