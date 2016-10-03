<properties 
	pageTitle="スキーマと Enterprise Integration Pack の概要 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack と Logic Apps でスキーマを使用する方法について説明します" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="deonhe"/>

# スキーマと Enterprise Integration Pack についての詳細情報  

## スキーマを使用する理由
スキーマを使用すると、受信した XML ドキュメントが有効であることを確認できます。つまり、予期したデータが定義済みの形式でドキュメントに含まれていることを確認できます。

## スキーマを追加する方法
Azure ポータルで次の手順を実行します。

1. **[そのほかのサービス]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. スキーマの追加先となる**統合アカウント**を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)
4. **[スキーマ]** タイルを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)

#### 2 MB 未満のスキーマ ファイルを追加するには  

5. 開いた [スキーマ] ブレードの **[追加]** ボタンを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)
6. スキーマの**名前**を入力し、**[スキーマ]** ボックスの右側にあるフォルダー アイコンを選択して、スキーマ ファイルをアップロードします。アップロード プロセスが完了したら、**[OK]** ボタンを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)

#### 2 MB を超える (最大 8 MB) スキーマ ファイルを追加するには  

7. BLOB のセキュリティ アクセス レベルが "**パブリック**" の場合 ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)

  * スキーマをストレージにアップロードし、URI をコピーします。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)

  * [スキーマの追加] で [Large file (大きなファイル)] を選択し、[コンテンツ URI] に URI を指定します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

8. BLOB のセキュリティ アクセス レベルが "**匿名アクセスはありません**" の場合 ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)

  * スキーマをストレージにアップロードします。 ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

  * スキーマの Shared Access Signature を生成します。 ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

  * [スキーマの追加] で [Large file (大きなファイル)] を選択し、[コンテンツ URI] に Shared Access Signature URI を指定します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)

9. 新しく追加されたスキーマが表示されます。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## スキーマを使用する方法
- スキーマは、B2B シナリオで交換されるメッセージを検証するために使用します。

## スキーマを編集する方法
1. **[Schemas (スキーマ)]** タイルを選択します。
2. 開いた [Schemas (スキーマ)] ブレードで、編集するスキーマを選択します。
3. [スキーマ] ブレードの **[アップロード]** リンクを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)
4. 開いたファイル ピッカー ダイアログを使用して、アップロードするスキーマ ファイルを選択します。
5. ファイル ピッカーの **[開く]** を選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)
6. アップロードが成功したことを示す通知が表示されます。

## スキーマを削除する方法
1. **[Schemas (スキーマ)]** タイルを選択します。
2. 開いた [Schemas (スキーマ)] ブレードで、削除するスキーマを選択します。
3. [スキーマ] ブレードのメニュー バーから **[削除]** リンクを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)
4. 選択したスキーマを本当に削除する場合は、[スキーマの削除] ダイアログで **[はい]** を選択し、選択内容を確定します。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)
5. これで、[スキーマ] ブレードにあるスキーマの一覧が更新され、削除したスキーマが一覧に表示されなくなります。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)

## 次のステップ

- [Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

<!---HONumber=AcomDC_0921_2016-->