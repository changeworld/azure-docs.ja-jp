---
title: "マップと Enterprise Integration Pack の概要 | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps でマップを使用する方法について説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 542278b38584d8e87a5dbf88af020dee3941cfa1


---
# <a name="learn-about-maps-and-the-enterprise-integration-pack"></a>マップと Enterprise Integration Pack についての詳細情報
## <a name="overview"></a>Overview
Enterprise Integration では、ある形式から別の形式に XML データを変換するのにマップを使用します。 

## <a name="what-is-a-map"></a>マップとは
マップとは、ドキュメント内のどのデータを別の形式に変換するかを定義する XML ドキュメントです。 

## <a name="why-use-maps"></a>マップを使用する理由
たとえば、B2B の注文または請求書を顧客から定期的に受け取るとします。顧客が使う日付の形式は YYYMMDD です。 しかし自分の組織では、MMDDYYY の形式で日付を保存しています。 マップを使用することで、注文または請求書の詳細を顧客活動データベースに保存する前に、日付の形式を YYYMMDD から MMDDYYY に*変換*できます。

## <a name="how-do-i-create-a-map"></a>マップを作成する方法
Visual Studio 2015 の [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") を使用すると、Biztalk の統合プロジェクトを作成できます。  統合マップ ファイルを作成すると、2 つの XML スキーマ ファイルの間でアイテムを視覚的にマップできます。  このプロジェクトをビルドした後は、XSLT ドキュメントが出力されます。

## <a name="how-to-upload-a-map"></a>マップをアップロードする方法
Azure ポータルで次の手順を実行します。  

1. **[参照]** を選択します。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. マップの追加先となる**統合アカウント**を選択します。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. **[マップ]** タイルを選択します。  
   ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)  
5. 開いた [マップ] ブレードの **[追加]** ボタンを選択します。  
   ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)  
6. マップの**名前**を入力し、**[マップ]** ボックスの右側にあるフォルダー アイコンを選択して、マップ ファイルをアップロードします。 アップロード プロセスが完了したら、 **[OK]** ボタンを選択します。  
   ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)  
7. 対象の統合アカウントにマップが追加されています。 マップ ファイルの追加が成功または失敗したことを示す通知が画面に表示されます。 通知の表示後、**[マップ]** タイルを選択すると、[マップ] ブレードで新しく追加されたマップを確認できます。    
   ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)  

## <a name="how-to-edit-a-map"></a>マップを編集する方法
マップを編集するには、必要な変更を施した新しいマップ ファイルをアップロードする必要があります。 最初にマップをダウンロードして編集できます。 

既存のマップに替わる新しいマップをアップロードするには、次の手順に従います。  

1. **[マップ]** タイルを選択します。  
2. [Maps (マップ)] ブレードが開いたら、編集するマップを選択します。  
3. **[マップ]** ブレードの **[更新]** リンクを選択します。  
   ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)   
4. 開いたファイル ピッカー ダイアログを使用して、アップロードするマップ ファイルを選択してから、ファイル ピッカーの **[開く]** を選択します。   
   ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)   
5. マップがアップロードされると、通知のポップアップが表示されます。    

## <a name="how-to-delete-a-map"></a>マップを削除する方法
1. **[マップ]** タイルを選択します。  
2. [Maps (マップ)] ブレードが開いたら、削除するマップを選択します。  
3. **[削除]** リンクを選択します。    
   ![](./media/app-service-logic-enterprise-integration-maps/delete.png)   
4. マップを本当に削除するか確認されます。  
   ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)   

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  
* [契約についての詳細情報](app-service-logic-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  
* [変換の詳細](app-service-logic-enterprise-integration-transform.md "エンタープライズ統合変換についての詳細")  




<!--HONumber=Nov16_HO3-->


