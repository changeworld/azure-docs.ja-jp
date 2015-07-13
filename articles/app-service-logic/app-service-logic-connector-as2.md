<properties 
   pageTitle="AS2 コネクタ" 
   description="AS2 コネクタ" 
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

#AS2 コネクタ
Microsoft Azure AS2 コネクタにより、AS2 トランスポート プロトコルを使用した企業間通信におけるメッセージの送受信が可能になります。AS2 は Applicability Statement 2 の略です。データはインターネット経由で安全かつ確実に転送されます。セキュリティはデジタル証明書と暗号化を使用して実現されています。

##前提条件
- TPM API App: AS2 コネクタを作成する前に、[BizTalk 取引先管理コネクタ][1]を作成する必要があります。
- SQL Azure データベース: B2B API Apps それぞれに、専用の Azure SQL Database が必要です。
- Azure Blob Storage コンテナー: AS2 アーカイブが有効な場合に、メッセージのプロパティを格納します。AS2 メッセージをアーカイブする必要がない場合は、Storage コンテナーは必要ありません。 

##AS2 コネクタの使用
AS2 コネクタを使用するにはまず、AS2 コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、または Azure Marketplace から AS2 コネクタ API アプリを選択することによって作成できます。

##AS2 コネクタの構成
取引先は、B2B (企業間) コミュニケーションに含まれるエンティティです。2 つのパートナーがある関係を確立した場合、この関係は "契約" と呼ばれます。契約は、2 つのパートナーが達成したいと考えているコミュニケーションに基づいて定義され、プロトコルまたはトランスポートに固有のものです。

取引先契約の作成に必要な手順については、[こちら][2]を参照してください。

##Logic Apps デザイナー画面での AS2 コネクタの使用
トリガーまたはアクションとして、AS2 コネクタを使用できます。

###トリガー
- Azure Logic Apps フロー デザイナーを起動します。
- 右側のウィンドウの [AS2 コネクタ] をクリックします。

	![トリガー設定][3]
- [->] (右矢印) をクリックします。

	![トリガー オプション][4]
- AS2 コネクタでは 1 つのトリガーが表示されます。*[受信およびデコード]* を選択します。

	![入力の受信およびデコード][5]
- このトリガーには、入力がありません。[->] (右矢印) をクリックします。

	![受信およびデコードの構成][6]
- 出力の一部として、コネクタは、AS2 固有のメタデータだけでなく AS2 ペイロードも返します。

###アクション
- 右側のウィンドウの [AS2 コネクタ] をクリックします。

	![アクションの設定][7]
- [->] (右矢印) をクリックします。

	![アクションの一覧][8]
- AS2 のコネクタはアクションを 1 つのみサポートします。*[エンコードおよび送信]* を選択します。

	![入力のエンコードおよび送信][9]
- アクションの入力を設定して構成します。

	![エンコードおよび送信の構成][10]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>Payload</td>
		<td>オブジェクト</td>
		<td>ペイロード</td>
	</tr>
	<tr>
		<td>AS2 From</td>
		<td>文字列</td>
		<td>AS2 の送信元</td>
	</tr>
	<tr>
		<td>AS2 の宛先</td>
		<td>文字列</td>
		<td>AS2 の宛先</td>
	</tr>
	<tr>
		<td>Partner URL</td>
		<td>文字列</td>
		<td>パートナーの URL</td>
	</tr>
	<tr>
		<td>Enable Archiving</td>
		<td>ブール値</td>
		<td>アーカイブの有効化</td>
	</tr>
</table>

アクションが正常に終了すると、HTTP 200 の応答コードを返します。

## コネクタでできること
これでコネクタが作成されたため、このコネクタを Logic App を使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->