<properties 
   pageTitle="Azure App Service のロジック アプリで B2B メッセージを追跡する | Microsoft Azure" 
   description="このトピックでは、B2B 処理の追跡について説明します。" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
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


# B2B メッセージを追跡する

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## B2B 追跡情報
B2B 通信には取引先間のメッセージ処理が関係します。リレーションシップは、取引先との 2 者間の契約として定義されます。通信が確立されると、期待どおりに通信が行われているかどうかを監視する手段が必要になります。

AS2、EDIFACT、および X12 の B2B シナリオ用にメッセージ追跡が実装されています。

## AS2
AS2 API アプリのインスタンスを作成したら、このインスタンスを参照して **[追跡]** を選択します。ここでは AS2 のすべての追跡情報を表示して、フィルタリングできます。

![][1]

## EDIFACT
EDIFACT API アプリのインスタンスを作成したら、このインスタンスを参照して **[追跡]** を選択します。ここでは EDIFACT のすべての追跡情報を表示して、フィルタリングできます。さらに、インターチェンジ レベル、グループ レベル、トランザクション セット レベルのデータをすべて 1 つのビューに表示することができます。

関連付けられている取引先管理 API アプリの EDIFACT 契約の一部としてバッチが作成された場合、バッチ処理セクションにこれらのすべてのバッチが一覧表示されます。バッチを選択し、アクティブなメッセージ (存在する場合) や、完了したものの情報も表示することができます。

![][2]

## X12
X12 API アプリのインスタンスを作成したら、このインスタンスを参照して **[追跡]** を選択します。ここでは X12 のすべての追跡情報を表示して、フィルタリングできます。さらに、インターチェンジ レベル、グループ レベル、トランザクション セット レベルのデータをすべて 1 つのビューに表示することができます。

関連付けられている取引先管理 API アプリの X12 契約の一部としてバッチが作成された場合、バッチ処理セクションにこれらのすべてのバッチが一覧表示されます。バッチを選択し、アクティブなメッセージ (存在する場合) や、完了したバッチの情報も表示することができます。

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_0727_2016-->