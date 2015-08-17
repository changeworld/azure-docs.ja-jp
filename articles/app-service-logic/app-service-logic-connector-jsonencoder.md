<properties
   pageTitle="BizTalk JSON エンコーダー"
   description="BizTalk JSON エンコーダー"
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk JSON エンコーダー
BizTalk JSON エンコード/デコード コネクタは、JSON データと XML データの間でアプリを相互運用するのに役立ちます。JSON インスタンスを指定して XML に変換したり、その逆方向に変換したりできます。

##BizTalk JSON エンコーダーを使用する
BizTalk JSON エンコーダー を使用するには、まず、BizTalk JSON Encoder API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から BizTalk JSON エンコーダー API アプリを選択することによって作成できます。

##Logic Apps のデザイナー画面で BizTalk JSON エンコーダーを使用する
[ロジック アプリの作成]に関するページの手順に従います。BizTalk JSON エンコーダーは、アクションとして使用できます。トリガーはありません。

###アクション
- 右側のウィンドウの [BizTalk JSON エンコーダー] をクリックします。

	![アクションの設定][3]
- [->] (右矢印) をクリックします。

	![アクションの一覧][4]
- BizTalk JSON エンコーダーは、2 つの操作をサポートします。*[Xml から JSON へ]* を選択する

	![Xml 入力から JSON 入力への変換][5]
- アクションの入力を設定して構成します。

	![エンコードおよび送信の構成][6]

パラメーター|型|パラメーターの説明
---|---|---
Input Xml|オブジェクト|入力 XML のコンテンツ
Remove Outer Envelope|文字列|XML コンテンツからルート ノードを削除するために設定されるフラグ

アクションは、入力コンテンツを JSON 表記で返します。

## コネクタでできること
これでコネクタが作成されたため、このコネクタを Logic App を使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[ロジック アプリの作成]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO6-->