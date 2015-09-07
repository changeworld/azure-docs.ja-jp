<properties 
   pageTitle="Logic Apps での待機コネクタの使用 | Microsoft Azure App Service"
	description="待機コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="rajram"/>

# 待機コネクタの使用開始とロジック アプリへの追加
待機コネクタを使用すると、指定した期間が経過するまで、または指定した時間になるまで、アプリの実行を遅らせることができます。この待機コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。ロジック アプリの中で使用して、実行を遅らせることができます。

## 待機コネクタを使用する
待機コネクタを使用するには、まず待機コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成中にインラインで作成するか、Azure Marketplace から待機コネクタ API アプリを選択することで実行できます。

## Logic Apps デザイナー画面で待機コネクタを使用する
待機コネクタは、アクションとして使用できます。トリガーはありません。

### アクション
- 右側のウィンドウの [待機コネクタ] をクリックします。![アクションの一覧][1]
- 待機コネクタは、2 つのアクションをサポートします。 
	- 遅延
	- この時間まで遅延
	 
- *[遅延]* を選択します。![遅延の入力][2]
- アクションの入力を設定して構成します。![構成されたアクション][3]

パラメーター|型|パラメーターの説明
---|---|---
期間 (分)|整数|分単位の遅延期間


## コネクタでできること
これでコネクタが作成されたため、このコネクタを Logic App を使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=August15_HO9-->