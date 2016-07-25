<properties 
	pageTitle="Enterprise Integration Pack と Logic Apps を使用したフラット ファイルのエンコードまたはデコードについての詳細情報 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack および Logic Apps の機能を使用してフラット ファイルをエンコードまたはデコードします。" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# フラット ファイルでのエンタープライズ統合

## 概要

XML の内容をエンコードするロジック アプリ内から、コネクタをエンコードするフラット ファイルを使用します。B2B シナリオでのビジネス パートナーに送信する前に、XML の内容をエンコードする必要がある場合があります。作成するロジック アプリは、HTTP 要求トリガー、別のアプリケーション、または多くの[コネクタ](../connectors/apis-list.md)の 1 つでも、といったさまざまなソースから XML の内容を取得できます。Logic Apps の機能の詳細については、[Logic Apps のドキュメント](./app-service-logic-what-are-logic-apps.md "Logic Apps についての詳細情報")に関するページを参照してください。

## コネクタをエンコードするフラット ファイルの作成方法

ロジック アプリを作成し、そのロジック アプリにコネクタをエンコードするフラット ファイルを追加するには、次の手順に従います。

1. ロジック アプリを作成し、XML データのエンコードに使用するスキーマを含む[統合アカウントにリンクします](./app-service-logic-enterprise-integration-accounts.md "ロジック アプリへの統合アカウントのリンクについての詳細情報")。
2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. アクションをエンコードするフラット ファイルを追加します。
-  **プラス** 記号を選択します。
-  プラス記号を選択すると表示される **[アクションの追加]** リンクを選択します。
-  検索ボックスに "*Flat*" と入力し、すべてのアクションから使用するアクションだけをフィルタリングします。
-  リストから **[Flat File Encoding (エンコードするフラット ファイル)]** アクションを選択します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. ポップアップする [Flat File Encoding (エンコードするフラット ファイル)] コントロール上の **[コンテンツ]** テキスト ボックスを選択します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. エンコードする内容として body タグを選択します。body タグは、[コンテンツ] フィールドに入力します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. **[スキーマ名]** リスト ボックスを選択し、上側の *[コンテンツ]* の入力内容をエンコードするために使用するスキーマを選択します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. 作業内容を保存します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

この時点で、コネクタをエンコードするフラット ファイルの設定が終了します。実際のアプリケーションでは、SalesForce などの LOB アプリケーションにエンコードされたデータを格納したり、取引先にエンコードされたデータを送信したりする必要がある場合があります。提供されるその他のコネクタのいずれかを使用して、Salesforce または取引先にエンコードするアクションの出力を送信するアクションを簡単に追加することができます。

ここでコネクタをテストするには、HTTP エンドポイントへの要求を作成し、要求の本文に XML の内容を含めます。

## コネクタをデコードするフラット ファイルの作成方法

### 前提条件
次の手順を完了するには、スキーマ ファイルが統合アカウントに既にアップロードされている必要があります。

1. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. アクションをエンコードするフラット ファイルを追加します。
-  **プラス** 記号を選択します。
-  プラス記号を選択すると表示される **[アクションの追加]** リンクを選択します。
-  検索ボックスに "*Flat*" と入力し、すべてのアクションから使用するアクションだけをフィルタリングします。
-  リストから **[Flat File Decoding (デコードするフラット ファイル)]** アクションを選択します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- **[コンテンツ]** コントロールを選択します。これにより、デコードする内容として使用できる前の手順から、内容の一覧が作成されます。受信 HTTP 要求からの*本文*がデコードする内容として使用できるようになったことを確認できます。**[コンテンツ]** コントロールに直接デコードする内容を入力できることにも注意してください。この例では、*本文*を選択し、デコード手順の手順 1 から受信 HTTP 要求の本文を使用しました。
- *Body* タグを選択します。現在、body タグは [コンテンツ] コントロールにあることに注意してください。
- 内容のデコードに使用するスキーマの名前を選択します。この例では、*[OrderFile (オーダーファイル)]* を選択します。[OrderFile (オーダーファイル)] は、前の時点で統合アカウントにアップロードしたスキーマの名前であることに注意してください。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- メニューから **[保存]** リンクを選択し、作業内容を保存します。![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

この時点で、コネクタをデコードするフラット ファイルの設定が終了します。実際のアプリケーションでは、デコードされたデータを SalesForce などの LOB アプリケーション内に格納する必要がある場合があります。Salesforce にデコードするアクションの出力を送信するアクションを簡単に追加することができます。

ここでコネクタをテストするには、HTTP エンドポイントへの要求を作成し、要求の本文にデコードする XML の内容を含めます。

## 詳細情報
- [Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

<!---HONumber=AcomDC_0713_2016-->