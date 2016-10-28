<properties 
	pageTitle="Enterprise Integration Pack の概要 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack の機能を使用することにより、Microsoft Azure App Service を使ってビジネス プロセスと統合シナリオを実現できます。" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# XML 変換での Enterprise Integration

## 概要
Enterprise Integration Transform コネクタは、データをある形式から別の形式に変換します。たとえば、受信メッセージに YearMonthDay 形式で現在の日付が含まれていたとします。この日付を、変換を使用して MonthDayYear 形式に変更できます。

## 変換でできること
変換 (マップともいう) は、送信元 XML スキーマ (入力) と送信先 XML スキーマ (出力) で構成されます。さまざまな組み込み関数を使用すると、文字列操作、条件付き割り当て、算術式、日時の書式設定や、ループ構造の操作などで、データを操作および制御できます。

## 変換を作成する方法
Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas) を使用して、変換/マップを作成できます。変換の作成とテストが終了したら、変換を統合アカウントにアップロードします。

## 変換を使用する方法
変換を統合アカウントにアップロードしたら、それを使ってロジック アプリを作成できます。ロジック アプリは、ロジック アプリがトリガーされたとき (かつ変換が必要な入力コンテンツがあるとき) に変換を実行します。

**変換を使用する手順は次のとおりです**。

### 前提条件 
プレビューでは、次のことを行う必要があります。

-  [Azure Functions コンテナーを作成する](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Azure Functions コンテナーを作成する")
-  [Azure Functions コンテナーに関数を追加する](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "このテンプレートは、webhook ベースの C# Azure 関数を作成します。これは、ロジック アプリの統合シナリオで使用する変換機能を備えています。")
-  統合アカウントを作成してマップを追加する

>[AZURE.TIP] Azure Functions コンテナーと Azure 関数の名前をメモしておいてください。次の手順で必要になります。

前提条件が整ったら、ロジック アプリの作成を開始します。

1. ロジック アプリを作成し、マップを含む[統合アカウントにリンクします](./app-service-logic-enterprise-integration-accounts.md "ロジック アプリへの統合アカウントの関連付けについての詳細情報")。
2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. まず **[アクションの追加]** を選択し、**[Transform XML (XML 変換)]** アクションを追加します。![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. 検索ボックスに「*transform*」と入力し、すべてのアクションから使用するアクションだけをフィルター処理します。![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. **[Transform XML (XML 変換)]** アクションを選択します。![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. 使用する関数を含む**関数コンテナー**を選択します。これは前の手順で作成した Azure Functions コンテナーの名前です。
7. 使用する**関数**を選択します。これは前の手順で作成した Azure 関数の名前です。
8. 変換する XML **コンテンツ**を追加します。HTTP 要求で受信する XML データは、すべて**コンテンツ**として使用することができます。この例では、ロジック アプリをトリガーした HTTP 要求の本文を選択します。
9. 変換を実行するのに使用する**マップ**の名前を選択します。マップが既に統合アカウント内に存在している必要があります。前の手順で、既にロジック アプリには、マップが含まれる統合アカウントへのアクセス権が付与されています。
10. 作業内容を保存します。![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

この時点で、マップの設定が終了します。実際のアプリケーションでは、変換されたデータを SalesForce などの LOB アプリケーション内に格納する必要がある場合があります。変換の出力は、アクションとして Salesforce に簡単に送信することができます。

これで、HTTP エンドポイントに要求を送信して、変換をテストすることができます。

## 機能とユース ケース

- マップで作成される変換は、あるドキュメントから別のドキュメントに名前と住所をコピーするなど、単純な内容になります。または、設定不要なマップ操作を使用して、より複雑な変換を作成することができます。
- 文字列、日時の関数などの複合的なマップ操作や関数をすぐに使用できます。
- スキーマ間でデータを直接コピーできます。SDK に含まれるマッパーを使用すれば、この操作は送信元スキーマ内の要素から送信先スキーマの対応する部分に線を引くくらい簡単です。
- マップを作成するときには、作成したすべてのリレーションシップやリンクを示すマップがグラフィカルに表示されます。
- [マップのテスト] 機能を使用してサンプル XML メッセージを追加します。クリックするだけで、作成したマップをテストして、生成された出力を参照できます。
- 既存のマップをアップロードできます。
- XML 形式のサポートが含まれます。


## 詳細情報
- [Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")
- [マップについての詳細情報](./app-service-logic-enterprise-integration-maps.md "Enterprise Integration のマップについての詳細情報")
 

<!---HONumber=AcomDC_0803_2016-->