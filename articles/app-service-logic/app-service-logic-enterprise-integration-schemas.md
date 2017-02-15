---
title: "スキーマと Enterprise Integration Pack の概要 | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps でスキーマを使用する方法について説明します"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2e0419c46e9380eb7e1848524a99501b4961d35c


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>スキーマと Enterprise Integration Pack についての詳細情報
## <a name="why-use-a-schema"></a>スキーマを使用する理由
スキーマを使用して、受信した XML ドキュメントが有効であり、予期したデータが定義済みの形式でドキュメントに含まれていることを確認します。 スキーマは、B2B シナリオで交換されるメッセージを検証するために使用します。

## <a name="add-a-schema"></a>スキーマを追加する
Azure ポータルで次の手順を実行します。  

1. **[そのほかのサービス]** を選択します。  
   ![[そのほかのサービス] が強調表示されている Azure Portal のスクリーンショット](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. フィルター検索ボックスに「**統合**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![フィルター検索ボックスのスクリーンショット](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. スキーマの追加先となる**統合アカウント**を選択します。    
   ![統合アカウントの一覧のスクリーンショット](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. **[スキーマ]** タイルを選択します。  
   ![[スキーマ] が強調表示されている EIP 統合アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>2 MB 未満のスキーマ ファイルを追加する
1. (前の手順の結果) 開いた **[スキーマ]** ブレードで、**[追加]** を選択します。  
   ![[追加] ボタンが強調表示されている [スキーマ] ブレードのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. スキーマの名前を入力します。 次に、スキーマ ファイルをアップロードするために、**[スキーマ]** テキスト ボックスの横にあるフォルダー アイコンをクリックします。 アップロード プロセスが完了したら、**[OK]** を選択します。    
   ![[小さいファイル] が強調表示されている [スキーマの追加] のスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>2 MB 以上のスキーマ ファイルを追加する (最大 8 MB)
このプロセスは、BLOB コンテナーのアクセス レベル (**パブリック** または **匿名アクセスはありません**) によって異なります。 このアクセス レベルを確認するには、**Azure Storage Explorer** で、**[BLOB コンテナー]** から、目的の BLOB コンテナーを選択します。 **[セキュリティ]** を選択し、**[アクセス レベル]** タブを選択します。

1. BLOB のセキュリティ レベルが **[パブリック]** の場合は、次の手順に従います。  
   ![BLOB コンテナー、[セキュリティ]、および [パブリック] が強調表示されている Azure Storage Explorer のスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
    a. スキーマをストレージにアップロードし、URI をコピーします。  
    ![URI が強調表示されているストレージ アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  
   
    b. **[スキーマの追加]** で **[大きいファイル]** を選択し、**[コンテンツ URI]** に URI を指定します。  
    ![[追加] ボタンと [大きいファイル] が強調表示されている [スキーマ] のスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
2. BLOB のセキュリティ レベルが **[匿名アクセスはありません]** の場合は、次の手順に従います。  
   ![BLOB コンテナー、[セキュリティ]、および [匿名アクセスはありません] が強調表示されている Azure Storage Explorer のスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
    a. スキーマをストレージにアップロードします。  
    ![ストレージ アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   
    b. スキーマの共有アクセス署名を生成します。  
    ![[共有アクセス署名] タブが強調表示されているストレージ アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   
    c. **[スキーマの追加]** で **[大きいファイル]** を選択し、**[コンテンツ URI]** に共有アクセス署名 URI を指定します。  
    ![[追加] ボタンと [大きいファイル] が強調表示されている [スキーマ] のスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
3. EIP 統合アカウントの **[スキーマ]** ブレードに、新たに追加されたスキーマが表示されます。  
   ![[スキーマ] と新しいスキーマが強調表示されている EIP 統合アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>スキーマを編集する
1. **[スキーマ]** タイルを選択します。  
2. 開いた **[スキーマ]** ブレードで、編集するスキーマを選択します。
3. **[スキーマ]** ブレードで、**[編集]** を選択します。  
   ![[スキーマ] ブレードのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. 開いたファイル ピッカー ダイアログ ボックスを使用して、編集するスキーマ ファイルを選択します。
5. ファイル ピッカーの **[開く]** を選択します。  
   ![ファイル ピッカーのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. アップロードが成功したことを示す通知が表示されます。  

## <a name="delete-schemas"></a>スキーマを削除する
1. **[スキーマ]** タイルを選択します。  
2. 開いた **[スキーマ]** ブレードで、削除するスキーマを選択します。  
3. **[スキーマ]** ブレードで、**[削除]** を選択します。
   ![[スキーマ] ブレードのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. 選択を確認するには、**[はい]** を選択します。  
   ![[スキーマの削除] 確認メッセージのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. これで、**[スキーマ]** ブレードにあるスキーマの一覧が更新され、削除したスキーマが一覧に表示されなくなります。  
   ![[スキーマ] が強調表示されている IEP 統合アカウントのスクリーンショット](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  




<!--HONumber=Nov16_HO3-->


