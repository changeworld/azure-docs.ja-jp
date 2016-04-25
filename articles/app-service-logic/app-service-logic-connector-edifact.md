<properties 
   pageTitle="Logic Apps での BizTalk Edifact コネクタの使用 | Microsoft Azure App Service" 
   description="BizTalk Edifact コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/10/2016"
   ms.author="rajram"/>

# BizTalk Edifact コネクタの使用開始とロジック アプリへの追加  
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Edifact サービスを使用して、Edifact プロトコルに従って企業間通信のメッセージを送受信します。Edifact は、一般に ASC Edifact (Accredited Standards Committee Edifact) とも呼ばれており、さまざまな業界で広く使用されています。

この BizTalk Edifact コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## 前提条件
- TPM API App: Edifact コネクタを作成する前に、[BizTalk 取引先管理コネクタ][1]を作成する必要があります。
- SQL Azure データベース: B2B API Apps それぞれに、専用の Azure SQL Database が必要です。
- Azure Service Bus: これはオプションであり、バッチ処理の場合にのみ使用されます。

## Edifact コネクタを使用する
Edifact コネクタを使用するにはまず、AS2 コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から AS2 コネクタ API アプリを選択することによって作成できます。

## Edifact コネクタを構成する
取引先は、B2B (企業間) コミュニケーションに含まれるエンティティです。2 つのパートナーがある関係を確立した場合、この関係は "契約" と呼ばれます。契約は、2 つのパートナーが達成したいと考えているコミュニケーションに基づいて定義され、プロトコルまたはトランスポートに固有のものです。

[取引先契約の作成][2]に必要な手順をご確認ください。

## Logic Apps デザイナー画面で Edifact コネクタを使用する
トリガーまたはアクションとして、Edifact コネクタを使用できます。

### トリガー
- Azure Logic Apps フロー デザイナーを起動します。
- 右側のウィンドウの [Edifact コネクタ] をクリックします。![トリガー設定][3]
- [->] (右矢印) をクリックします。![トリガー オプション][4]
- Edifact コネクタでは 1 つのトリガーが表示されます。*[リリース バッチ]* を選択します。![バッチ入力のリリース][5]
- このトリガーには、入力がありません。[->] (右矢印) をクリックします。![リリース バッチの構成][6]
- 出力の一部として、コネクタは、メッセージがバッチ処理されているかどうかの情報だけでなく Edifact ペイロードと契約 ID も返します。

### アクション
- 右側のウィンドウの [Edifact コネクタ] をクリックします。![アクションの設定][7]
- [->] (右矢印) をクリックします。![アクションの一覧][8]
- Edifact コネクタは、多くのアクションをサポートしています。*[エンコード]* を選択します。![入力のエンコード][9]
- アクションの入力を設定して構成します。![エンコードの構成][10]

	パラメーター|型|パラメーターの説明
---|---|---
コンテンツ|文字列|XML メッセージ
Agreement ID|整数|契約 ID
Is Batched Message|ブール値|バッチ処理されたメッセージ
Data Element Separator|文字列|データ要素の区切り記号
Component Separator|文字列|コンポーネントの区切り記号
Segment Terminator|文字列|セグメントの終端記号
Decimal Point Indicator|文字列|小数点のインジケーター
Repetition Separator|文字列|繰り返しの区切り記号
Escape Character|文字列|エスケープ文字
Replacement Character|文字列|置換文字
Segment Terminator Suffix|文字列|セグメント終端記号のサフィックス

アクションが正常に完了すると、EDIFACT ペイロードを含むオブジェクトが返されます。

## コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

 


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-edifact/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-edifact/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-edifact/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-edifact/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-edifact/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-edifact/EncodeConfigured.PNG

<!---HONumber=AcomDC_0413_2016-->