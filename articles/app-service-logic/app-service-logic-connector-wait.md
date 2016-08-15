<properties 
   pageTitle="Logic Apps での待機コネクタの使用 | Microsoft Azure App Service" 
   description="待機コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/19/2016"
   ms.author="rajram"/>

# 待機コネクタの使用開始とロジック アプリへの追加

>[AZURE.NOTE] このコネクタのサポートは終了します。現在、その機能は、新しいロジック アプリの作成時に既定で**遅延アクション**として組み込まれるようになっているためです。このコネクタを使用しているすべてのロジック アプリをアップグレードすることをお勧めします。本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

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
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

 

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0803_2016-->