<properties 
   pageTitle="BizTalk Edifact コネクタ" 
   description="BizTalk Edifact コネクタ" 
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

#BizTalk Edifact コネクタ
Microsoft Azure Edifact サービスにより、Edifact プロトコルに従って企業間通信のメッセージを送受信できます。Edifact は、一般に ASC Edifact、または Accredited Standards Committee Edifact とも呼ばれています。これは、さまざまな業界で広く使用されています。

##前提条件
- TPM API App: Edifact コネクタを作成する前に、[BizTalk 取引先管理コネクタ][1]を作成する必要があります。
- SQL Azure データベース: B2B API Apps それぞれに、専用の Azure SQL Database が必要です。
- Azure Service Bus: これはオプションであり、バッチ処理の場合にのみ使用されます。

##Edifact コネクタを使用する
Edifact コネクタを使用するにはまず、AS2 コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から AS2 コネクタ API アプリを選択することによって作成できます。

##Edifact コネクタを構成する
取引先は、B2B (企業間) コミュニケーションに含まれるエンティティです。2 つのパートナーがある関係を確立した場合、この関係は "契約" と呼ばれます。契約は、2 つのパートナーが達成したいと考えているコミュニケーションに基づいて定義され、プロトコルまたはトランスポートに固有のものです。

取引先契約の作成に必要な手順については、[こちら][2]を参照してください。

##Logic Apps デザイナー画面で Edifact コネクタを使用する
トリガーまたはアクションとして、Edifact コネクタを使用できます。

###トリガー
- Azure Logic Apps フロー デザイナーを起動します。
- 右側のウィンドウの [Edifact コネクタ] をクリックします。

	![トリガー設定][3]
- [->] (右矢印) をクリックします。

	![トリガー オプション][4]
- Edifact コネクタでは 1 つのトリガーが表示されます。*[リリース バッチ]* を選択します。

	![バッチ入力のリリース][5]
- このトリガーには、入力がありません。[->] (右矢印) をクリックします。

	![リリース バッチの構成][6]
- 出力の一部として、コネクタは、メッセージがバッチ処理されているかどうかの情報だけでなく Edifact ペイロードと契約 ID も返します。

###アクション
- 右側のウィンドウの [Edifact コネクタ] をクリックします。

	![アクションの設定][7]
- [->] (右矢印) をクリックします。

	![アクションの一覧][8]
- Edifact コネクタは、多くのアクションをサポートしています。*[エンコード]* を選択します。

	![入力のエンコード][9]
- アクションの入力を設定して構成します。

	![エンコードの構成][10]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>コンテンツ</td>
		<td>文字列</td>
		<td>XML メッセージ</td>
	</tr>
	<tr>
		<td>契約 ID</td>
		<td>整数</td>
		<td>契約 ID</td>
	</tr>
	<tr>
		<td>Is Batched Message</td>
		<td>ブール値</td>
		<td>バッチ処理されたメッセージである</td>
	</tr>
	<tr>
		<td>Data Element Separator</td>
		<td>文字列</td>
		<td>データ要素の区切り記号</td>
	</tr>
	<tr>
		<td>Component Separator</td>
		<td>文字列</td>
		<td>コンポーネントの区切り記号</td>
	</tr>
	<tr>
		<td>Segment Terminator</td>
		<td>文字列</td>
		<td>セグメントの終端記号</td>
	</tr>
	<tr>
		<td>Decimal Point Indicator</td>
		<td>文字列</td>
		<td>小数点のインジケーター</td>
	</tr>
	<tr>
		<td>Repetition Separator</td>
		<td>文字列</td>
		<td>繰り返しの区切り記号</td>
	</tr>
	<tr>
		<td>Escape Character</td>
		<td>文字列</td>
		<td>エスケープ文字</td>
	</tr>
	<tr>
		<td>Replacement Character</td>
		<td>文字列</td>
		<td>置換文字</td>
	</tr>
	<tr>
		<td>Segment Terminator Suffix</td>
		<td>文字列</td>
		<td>セグメント終端記号のサフィックス</td>
	</tr>
</table>

アクションが正常に完了すると、EDIFACT ペイロードを含むオブジェクトが返されます。

## コネクタでできること
これでコネクタが作成されたため、このコネクタを Logic App を使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。


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

<!---HONumber=July15_HO3-->