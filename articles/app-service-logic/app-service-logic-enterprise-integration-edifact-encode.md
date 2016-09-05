<properties 
	pageTitle="Enterprise Integration Pack の Encode EDIFACT Message コネクタの詳細情報 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack と Logic Apps を使用してパートナーを使用する方法について説明します。" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Encode EDIFACT Message を使ってみる

EDI およびパートナー固有のプロパティを検証する

## 接続の作成

### 前提条件

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。

* Encode EDIFACT Message コネクタを使用するには、統合アカウントが必要です。[統合アカウント](./app-service-logic-enterprise-integration-create-integration-account.md)、[パートナー](./app-service-logic-enterprise-integration-partners.md)、および [EDIFACT 契約](./app-service-logic-enterprise-integration-edifact.md)の作成方法の詳細を確認してください。

### 次の手順に従って、Decode EDIFACT Message に接続します。

1. [ロジック アプリの作成](./app-service-logic-create-a-logic-app.md)に関する記事に例が記載されています。

2. このコネクタにはトリガーがありません。ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「EDIFACT」と入力します。[Encode EDIFACT Message by agreement name (契約名による Encode EDIFACT Message)] または [Encode to EDIFACT message by identities (ID による Encode EDIFACT Message)] を選択します。

	![search EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)

3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。

	![create integration account connection](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)

4. 統合アカウントの詳細を入力します。アスタリスクが付いているプロパティは必須です。

	| プロパティ | 詳細 |
	| -------- | ------- |
	| 接続名 * | 接続の任意の名前を入力します。 |
	| 統合アカウント * | 統合アカウント名を入力します。統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 

	入力を完了すると、接続の詳細は次のようになります。

	![integration account connection](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
	
5. **[作成]** を選択します。
	
6. 接続が作成されたことを確認します。

	![integration account connection details](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)
	
#### 契約名による Encode EDIFACT Message

7.	エンコードする EDIFACT 契約名と xml メッセージを指定します。

	![provide mandatory fields](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)
	
#### ID による Encode EDIFACT Message

7. 送信者の識別子、送信者の修飾子、受信者の識別子、および受信者の修飾子を、EDIFACT 契約で構成されているとおりに指定します。エンコードする xml メッセージを選択します。

	![provide mandatory fields](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)
	
## EDIFACT Encode の機能

* 送信者の修飾子および識別子を受信者の修飾子および識別子と照合することで、契約を解決する
* XML エンコード メッセージをインターチェンジ内の EDI トランザクション セットに変換して、EDI インターチェンジをシリアル化する
* トランザクション セット ヘッダーおよびトレーラー セグメントを適用する
* 各送信インターチェンジのインターチェンジ制御番号、グループ制御番号、およびトランザクション セット制御番号を生成する
* ペイロード データの区切り記号を置換する
* EDI およびパートナー固有のプロパティを検証する
	* メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ検証
	* トランザクション セット データ要素に対して実行される EDI 検証
	* トランザクション セット データ要素に対して実行される拡張検証
* 各トランザクション セットの XML ドキュメントを生成する
* 技術確認または機能確認を要求する (構成されている場合)
	* 技術確認として、CONTRL メッセージはインターチェンジの受信を示します。
	* 機能確認として、CONTRL メッセージは受信したインターチェンジ、グループ、またはメッセージの受理または拒否を、エラーまたはサポートされていない機能の一覧と共に示します。

## 次のステップ

[Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

<!---HONumber=AcomDC_0824_2016-->