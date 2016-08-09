<properties 
   pageTitle="Logic Apps での BizTalk 取引先管理コネクタの使用 | Microsoft Azure App Service" 
   description="BizTalk 取引先管理コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法" 
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
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk 取引先管理の使用開始とロジック アプリへの追加

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


BizTalk 取引先管理 (TPM) サービスにより、パートナーや契約などの企業間の関係を、スキーマや証明書などの関連する成果物と共に定義して保持することができます。これらの関係は、AS2、EDIFACT、および X12 などの関連する API サービスによって適用できます。

TPM API アプリは、AS2 コネクタ、X12 API アプリ、または EDIFACT API アプリの基本要件です。BizTalk 取引先管理をビジネス ワークフローに追加し、ロジック アプリ内の企業間ワークフローの一部としてデータを処理できます。

## 前提条件
- 空の SQL Azure データベース - 新しい TPM API App を作成する前に、空の SQL Azure データベースを作成する必要があります。

## パートナー、契約およびプロファイルを理解する
[取引先契約の作成の詳細についてはこちらをご覧ください][1]。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_0727_2016-->