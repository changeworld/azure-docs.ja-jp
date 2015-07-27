<properties
   pageTitle="BizTalk XPath Extractor"
   description="BizTalk XPath Extractor"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/01/2015"
   ms.author="rajram"/>

#BizTalk XPath Extractor

BizTalk XPath Extract コネクタは、指定された XPath に基づいて XML コンテンツからデータを検索して抽出します。

##BizTalk XPath Extractor の使用
1. BizTalk XPath Extractor を使用するには、まず、BizTalk XPath Extractor API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から BizTalk XPath Extractor API アプリを選択することによって作成できます。

	>[AZURE.NOTE]
2. [新しいロジック アプリを作成します]。作成した Logic App で [トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。
3. デザイナーの右側のウィンドウに、フローの作成に使用できる API Logic Apps が一覧表示されます。"BizTalk XPath Extractor" を検索します。これを選択すると、フローに Xpath Extractor が追加され、そのインスタンスがプロビジョニングされます。
2. プロビジョニングが終わると、デザイナーに BizTalk XPath Extractor API App に関連付けられたアクションが表示されます。

![BizTalk XPath Extractor のアクション選択][1]

3. [XPath を使用して抽出] を選択します。

[XPath を使用して抽出] は、指定された入力 XML で入力 XPath 式を評価します。

![BizTalk XPath Extractor の入力][2]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>string</td>
		<td>XMl 内部のクエリ パスです。</td>
	</tr>
	<tr>
		<td>入力 XML</td>
		<td>string</td>
		<td>入力 XML の内容。</td>
	</tr>
</table>

アクションは文字列として出力を返します (結果)。結果には、XML 内部のクエリ パスの値が含まれています。

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[新しいロジック アプリを作成します]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO3-->