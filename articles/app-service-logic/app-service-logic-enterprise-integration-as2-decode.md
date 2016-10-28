<properties 
	pageTitle="Enterprise Integration Pack の Decode AS2 Message コネクタの詳細情報 | Microsoft Azure App Service | Microsoft Azure" 
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

# Decode AS2 Message を使ってみる

Decode AS2 Message に接続すると、メッセージを転送するときに、セキュリティと信頼性を確保できます。デジタル署名、解読、およびメッセージ処理通知 (MDN) による受信確認が可能になります。

## 接続の作成

### 前提条件

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。

* Decode AS2 Message コネクタを使用するには、統合アカウントが必要です。[統合アカウント](./app-service-logic-enterprise-integration-create-integration-account.md)、[パートナー](./app-service-logic-enterprise-integration-partners.md)、および [AS2 契約](./app-service-logic-enterprise-integration-as2.md)の作成方法の詳細を確認してください。

### 次の手順に従って、Decode AS2 Message に接続します。

1. [ロジック アプリの作成](./app-service-logic-create-a-logic-app.md)に関する記事に例が記載されています。

2. このコネクタにはトリガーがありません。ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「AS2」と入力します。[AS2 – Decode AS2 Message] を選択します。

	![Search AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。

	![Create integration connection](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. 統合アカウントの詳細を入力します。アスタリスクが付いているプロパティは必須です。

	| プロパティ | 詳細 |
	| --------   | ------- |
	| 接続名 * | 接続の任意の名前を入力します。 |
	| 統合アカウント * | 統合アカウント名を入力します。統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

  	入力を完了すると、接続の詳細は次のようになります。

  	![integration connection](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. **[作成]** を選択します。
	
6. 接続が作成されたことを確認します。これで、ロジック アプリで他の手順に進むことができます。

	![integration connection created](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png)

7. 要求の出力から本文とヘッダーを選択します。

	![provide mandatory fields](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png)

## AS2 Decode の機能

* AS2/HTTP ヘッダーを処理する
* 署名を検証する (構成されている場合)
* メッセージを解読する (構成されている場合)
* メッセージを展開する (構成されている場合)
* 受信した MDN と元の送信メッセージを調整する
* 否認不可データベースのレコードを更新し、関連付ける
* AS2 状態レポート用のレコードを書き込む
* 出力ペイロードの内容は、base64 でエンコードされる
* AS2 契約の構成に基づいて、MDN が必要かどうかや、MDN を同期にするか非同期にするかを判断する
* 同期または非同期の MDN を生成する (契約の構成に基づいて)
* MDN の関連付けトークンとプロパティを設定する

##試してみる

実際に試してみましょう。Logic Apps AS2 の機能を使用して、本格的な機能を備えた独自のロジック アプリをデプロイするには、[こちら](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)をクリックしてください。

## 次のステップ

[Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

<!---HONumber=AcomDC_0914_2016-->