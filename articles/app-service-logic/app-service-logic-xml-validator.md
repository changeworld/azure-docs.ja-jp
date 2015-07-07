<properties
   pageTitle="BizTalk XML Validator"
   description="BizTalk XML Validator"
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
   ms.date="03/20/2015"
   ms.author="rajram"/>

# BizTalk XML Validator

BizTalk XML Validate コネクタは、アプリで XML データを定義済みの XML スキーマに対して検証するために役立ちます。ユーザーは、既存のスキーマを使用することも、フラット ファイル インスタンス、JSON インスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。

##BizTalk XML Validator の使用
1. BizTalk XML Validator を使用するには、まず、BizTalk XPath Extractor API アプリのインスタンスを作成する必要があります。これは、ロジック アプリを作成するときにインラインで、または Azure Marketplace から BizTalk XML Validator API アプリを選択することによって、行うことができます。

###BizTalk XML Validator を構成する
BizTalk XML Validator は、構成の一部としてスキーマを受け取ります。ユーザーは、Azure ポータルから直接 API アプリを起動するか、デザイナー画面で API アプリをダブルクリックすることで、API アプリ構成ブレードを起動できます。

![BizTalk XML Validator の構成][1]

API アプリ ブレードでは、*[スキーマ]* 部分をクリックすることでスキーマを構成できます。

![BizTalk XML Validator のスキーマ部分][2]

ユーザーは、ディスクからスキーマをアップロードするか、フラット ファイル インスタンスまたは JSON インスタンスからスキーマを生成することができます。

![BizTalk XML Validator のスキーマ][3]


###デザイン画面での BizTalk Flat File Encoder の使用
構成が終わったら、[->] をクリックし、アクションの一覧からアクションを選択できます。

![BizTalk XML Validator のアクション リスト][4]

####XML を検証する

XML 検証アクションは、特定の XML の入力を構成済みのスキーマに対して検証します。

![BizTalk XML Validator の XML の検証][5]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>入力 XML</td>
		<td>string</td>
		<td>検証される入力 XML</td>
	</tr>
</table>


アクションは出力を文字列として返します。出力には、XML Validator の応答を表すモデルが含まれます。それは、結果、スキーマ名、ルート ノード、およびエラーの説明で構成されます。

![6]

<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG
[6]: ./media/app-service-logic-xml-validator/img1.PNG
 

<!---HONumber=62-->