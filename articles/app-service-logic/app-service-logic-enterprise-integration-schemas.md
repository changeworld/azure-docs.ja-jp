<properties 
	pageTitle="スキーマと Enterprise Integration Pack の概要 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack と Logic Apps でスキーマを使用する方法について説明します" 
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

# スキーマと Enterprise Integration Pack についての詳細情報  

## スキーマを使用する理由
スキーマを使用すると、受信した XML ドキュメントが有効であることを確認できます。つまり、予期したデータが定義済みの形式でドキュメントに含まれていることを確認できます。

## スキーマを追加する方法
Azure ポータルで次の手順を実行します。
1. **[参照]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. スキーマの追加先となる**統合アカウント**を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **[Schemas (スキーマ)]** タイルを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-1.png)
5. 開いた [Schemas (スキーマ)] ブレードの **[追加]** ボタンを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-2.png)
6. スキーマの**名前**を入力し、**[スキーマ]** ボックスの右側にあるフォルダー アイコンを選択して、スキーマ ファイルをアップロードします。アップロード プロセスが完了したら、**[OK]** ボタンを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-3.png)
7. *ベル*通知アイコンを選択して、スキーマ アップロード プロセスの進捗状況を確認します。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-4.png)
8. **[Schemas (スキーマ)]** タイルを選択します。これにより、タイルが更新されます。新しいスキーマが正しく追加されたことが反映されており、スキーマの増加後の数が表示されます。**[Schemas (スキーマ)]** タイルを選択すると、右側の [Schemas (スキーマ)] ブレードにも、新しく追加されたスキーマが表示されます。 ![](./media/app-service-logic-enterprise-integration-schemas/schema-5.png)


## スキーマを使用する方法
- スキーマは、B2B シナリオで交換されるメッセージを検証するために使用します。

## スキーマを編集する方法
1. **[Schemas (スキーマ)]** タイルを選択します。
2. 開いた [Schemas (スキーマ)] ブレードで、編集するスキーマを選択します。
3. [Schemas (スキーマ)] ブレードの **[アップロード]** リンクを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/edit-1.png)
4. 開いたファイル ピッカー ダイアログを使用して、アップロードするスキーマ ファイルを選択します。
5. ファイル ピッカーの **[開く]** を選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/edit-2.png)
6. アップロードが成功したことを示す通知が表示されます。 ![](./media/app-service-logic-enterprise-integration-schemas/edit-3.png)

## スキーマを削除する方法
1. **[Schemas (スキーマ)]** タイルを選択します。
2. 開いた [Schemas (スキーマ)] ブレードで、削除するスキーマを選択します。
3. [Schemas (スキーマ)] ブレードのメニュー バーから **[削除]** リンクを選択します。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-1.png)
4. 選択したスキーマを本当に削除する場合は、[Delete schema (スキーマの削除)] ダイアログで **[はい]** を選択し、選択内容を確定します。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-2.png)
5. 最後に、[Schemas (スキーマ)] ブレードにあるスキーマの一覧が更新され、削除したスキーマが一覧に表示されなくなったことがわかります。 ![](./media/app-service-logic-enterprise-integration-schemas/delete-3.png)

## 次のステップ

- [Enterprise Integration Pack についての詳細情報](./app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")

      

<!---HONumber=AcomDC_0713_2016-->