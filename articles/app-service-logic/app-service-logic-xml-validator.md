<properties
   pageTitle="Azure App Service のロジック アプリでの BizTalk XML Validator の使用 | Microsoft Azure"
   description="ロジック アプリ内で BizTalk XML Validator を使用してスキーマを検証します"
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
   ms.date="02/18/2016"
   ms.author="rajram"/>

# BizTalk XML Validator

>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

アプリで BizTalk XML Validator コネクタを使用し、定義済みの XML スキーマと照らして XML データを検証します。既存のスキーマを使用することも、フラット ファイルのインスタンス、JSON のインスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。

## BizTalk XML Validator を使用する
BizTalk XML Validator を使用するには、まず、BizTalk XPath Validator API アプリのインスタンスを作成します。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から BizTalk XML Validator API アプリを選択することによって作成できます。

### BizTalk XML Validator を構成する
BizTalk XML Validator は、構成の一部としてスキーマを受け取ります。Azure ポータルから直接 API アプリを起動するか、デザイナー画面で API アプリをダブルクリックすることで、API アプリ構成ブレードを起動できます。

![BizTalk XML Validator の構成][1]

API アプリ ブレードでは、*[スキーマ]* を選択してスキーマを構成できます。

![BizTalk XML Validator のスキーマ部分][2]

ディスクからスキーマをアップロードするか、フラット ファイル インスタンスまたは JSON インスタンスからスキーマを生成することができます。

![BizTalk XML Validator のスキーマ][3]


### デザイン画面での BizTalk Flat File Encoder の使用
構成が終わったら、*[->]* をクリックし、アクションの一覧からアクションを選択できます。

![BizTalk XML Validator のアクション リスト][4]

#### XML を検証する

XML 検証アクションは、特定の XML の入力を、構成済みのスキーマに照らして検証します。

![BizTalk XML Validator の XML の検証][5]

パラメーター|型|パラメーターの説明
---|---|---
入力 XML|文字列|検証する入力 XML

アクションは出力をオブジェクトとして返します。出力には、XML Validator からの応答を表すモデルが含まれます。これは、結果、スキーマ名、ルート ノード、およびエラーの説明で構成されます。


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0224_2016-->