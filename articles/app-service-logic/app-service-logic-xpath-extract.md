<properties
   pageTitle="Azure App Service のロジック アプリでの BizTalk XPath Extractor の使用 | Microsoft Azure"
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
   ms.date="12/07/2015"
   ms.author="rajram"/>

#BizTalk XPath Extractor

BizTalk XPath Extract コネクタは、指定された XPath に基づいて XML コンテンツからデータを検索して抽出します。

## BizTalk XPath Extractor の使用
1. BizTalk XPath Extractor を使用するには、まず、BizTalk XPath Extractor API アプリのインスタンスを作成する必要があります。これは、ロジック アプリを作成するときにインラインで、または Azure Marketplace から BizTalk XPath Extractor API アプリを選択することによって、行うことができます。

	>[AZURE.NOTE]BizTalk Xpath Extractor に関連付けられた構成設定はありません。
2. [新しいロジック アプリを作成します]。作成したロジック アプリの中で [トリガーとアクション] を開くことで Logic Apps デザイナーを開き、フローを構成します。
3. デザイナーの右側のウィンドウに、フローを構成するために使用できる API Apps が表示されます。"BizTalk XPath Extractor" を検索します。これを選択すると、フローに Xpath Extractor が追加され、そのインスタンスがプロビジョニングされます。
4. プロビジョニングが終わると、デザイナーに BizTalk XPath Extractor API アプリに関連付けられたアクションが表示されます。![BizTalk XPath Extractor のアクション選択][1]

5. [XPath を使用して抽出] を選択します。[XPath を使用して抽出] は、指定された入力 XML で入力 XPath 式を評価します。![BizTalk XPath Extractor の入力][2]

	パラメーター|型|パラメーターの説明
---|---|---
XPath|string|XMl 内部のクエリ パスです。
入力 XML|string|入力 XML の内容。

アクションは文字列として出力を返します (結果)。結果には、XML 内部のクエリ パスの値が含まれています。

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[新しいロジック アプリを作成します]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_1210_2015-->