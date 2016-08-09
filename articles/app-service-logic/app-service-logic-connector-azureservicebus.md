<properties
   pageTitle="Logic Apps での Azure Service Bus コネクタの使用 | Microsoft Azure App Service"
   description="Azure Service Bus コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
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
   ms.date="03/16/2016"
   ms.author="rajram"/>


# Azure Service Bus コネクタの使用開始とロジック アプリへの追加 
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。2015-08-01-preview スキーマ バージョンについては、こちらの [Service Bus API](../connectors/connectors-create-api-servicebus.md) をクリックしてください。

Azure Service Bus に接続して、メッセージをキューとトピックに送信、またはメッセージをキューとサブスクリプションから受信します。コネクタは、"ワークフロー" の一部として Logic Apps で使用されます。

## トリガーとアクション
トリガーとは、発生するイベントを指します。たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。アクションは、トリガーの結果です。たとえば、注文または新しいメッセージがキューに配置されたときに、アラートまたはメッセージを送信します。

Azure Service Bus コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。

Azure Service Bus コネクタでは、次のようなトリガーとアクションが使用できます。

トリガー | アクション
--- | ---
Message Available | メッセージを送信

## Azure Service Bus コネクタの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Azure Service Bus コネクタ" を検索して選択し、**[作成]** を選択します。
3. 名前、App Service プラン、その他のプロパティを入力します。
![][1]

4. 次のパッケージの設定を入力します。

	名前 | 説明
--- | ---
接続文字列 | Azure Service Bus の接続文字列。たとえば、「*Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key]*」と入力します。
エンティティ名 | キューまたはトピックの名前を入力します。
サブスクリプション名 | メッセージの受信元となるサブスクリプションの名前を入力します。

5. **[作成]** をクリックします。

## ロジック アプリでの Azure Service Bus コネクタの使用
コネクタが作成されたら、ロジック アプリのトリガーやアクションとして Azure Service Bus コネクタを使用できます。これを行うには、次の手順を実行します。

1.	新しいロジック アプリを作成し、Azure Service Bus コネクタが存在する同じリソース グループを選択します。
![][2]

2.	[トリガーとアクション] を開き、Logic Apps デザイナーを開いてワークフローを構成します。
![][3]

3. Azure Service Bus コネクタが右側のギャラリーの [このリソース グループの API Apps] セクションに表示されます。
![][4]

4. [Azure Service Bus コネクタ] をクリックして、Azure Service Bus コネクタをエディターにドロップできます。

5.	これで、ワークフローで Azure Service Bus コネクタを使用できるようになりました。今後は、Azure Service Bus トリガー ("Message Available") を使って取得したメッセージを、フローの他のアクションで使用できます。
![][5]

	![][6]

Azure Service Bus の "Send Message" アクションを使用することもできます。
![][7]

![][8]

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=AcomDC_0727_2016-->