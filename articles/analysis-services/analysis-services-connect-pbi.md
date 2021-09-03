---
title: Power BI で Azure Analysis Services に接続する | Microsoft Docs
description: Azure Analysis Services サーバーに Power BI を使って接続する方法を説明します。 接続すると、ユーザーはモデル データを探すことができます。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a956d5e3be0a5f92e22dcd1536339ff5244e19dc
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213357"
---
# <a name="connect-with-power-bi"></a>Power BI を使用した接続

Azure でサーバーを作成して、表形式モデルを展開すると、社内のユーザーはすぐに接続してデータの分析を開始できるようになります。 

> [!NOTE]
> Power BI Desktop モデルを Power BI サービスに公開する場合は、Azure Analysis Services サーバーで、Case-Sensitive 照合順序サーバーのプロパティが選択されていない (既定) ことを確認します。 Case-Sensitive サーバーのプロパティは、SQL Server Management Studio を使用して設定できます。
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop での接続

1. Power BI Desktop で、 **[データの取得]**  >  **[Azure]**  >  **[Azure Analysis Services データベース]** の順にクリックします。

2. **[サーバー]** にサーバー名を入力します。 必ず URL 全体を指定してください。たとえば、「asazure://westcentralus.asazure.windows.net/advworks」のように入力します。

3. **[データベース]** には、接続先の表形式モデル データベースまたはパースペクティブの名前がわかっている場合はその名前を貼り付けます。 それ以外の場合は、このフィールドを空のままにし、後からデータベースまたはパースペクティブを選択することができます。

4. 接続オプションを選択し、 **[接続]** を押します。 

    **[ライブ接続]** と **[インポート]** の両方のオプションがサポートされています。 ただし、インポート モードにはいくつかの制限があるため、ライブ接続を使用することをお勧めします。特に、インポート時にはサーバーのパフォーマンスが影響を受ける可能性があります。
    
    [混合ストレージ モード](/power-bi/transform-model/desktop-composite-models)の Power BI モデルがある場合は、 **[ライブ接続]** オプションが **[[DirectQuery]](/power-bi/connect-data/desktop-directquery-datasets-azure-analysis-services)** オプションに置き換えられます。 モデルがインポートから混在ストレージ モードに切り替えられた場合、ライブ接続も自動的に DirectQuery にアップグレードされます。

5. 資格情報の入力を求めるメッセージが表示されたら、**Microsoft アカウント** を選択し、 **[サインイン]** をクリックします。 

    :::image type="content" source="media/analysis-services-connect-pbi/aas-sign-in.png" alt-text="Azure AS にサインインする":::

   > [!NOTE]
   > Windows 基本認証はサポートされていません。 

6. **[ナビゲーター]** で、サーバーを展開し、接続先のモデルまたはパースペクティブを選択してから **[接続]** をクリックします。 モデルまたはパースペクティブをクリックすると、そのビューのすべてのオブジェクトが表示されます。

    Power BI Desktop でモデルが開き、[レポート] ビューに空のレポートが表示されます。 [フィールド] リストには、非表示にされているものを除くすべてのモデル オブジェクトが表示されます。 右下隅に接続ステータスが表示されます。

## <a name="connect-in-power-bi-service"></a>Power BI (サービス) での接続

1. サーバー上のモデルにライブ接続できる Power BI Desktop ファイルを作成します。
2. [Power BI](https://powerbi.microsoft.com) で、 **[データの取得]**  >  **[ファイル]** をクリックしてから、お客様の .pbix ファイルを検索して選択します。

## <a name="request-memory-limit"></a>要求メモリ制限

システムのパフォーマンスを確保するために、Azure Analysis Services に対して Power BI レポートによって発行されるすべてのクエリに、メモリ制限が強制されます。Azure Analysis Services サーバー上で構成されている[クエリ メモリ制限](/analysis-services/server-properties/memory-properties?view=azure-analysis-services-current&preserve-view=true)は関係ありません。 クエリがメモリを集中的に消費しすぎる場合は、クエリまたはクエリの計算を簡素化することを検討する必要があります。

|クエリの種類| 要求メモリ制限 |
|-----------------------------------------------------------|----------------------|
| Power BI からのライブ接続                            | 10 GB  |
| 共有ワークスペースの Power BI レポートからの DirectQuery  | 1 GB   |
| Premium ワークスペースの Power BI レポートからの DirectQuery | 10 GB  |
| Power BI Q & A | 100 MB |

## <a name="see-also"></a>関連項目
[Azure Analysis Services に接続する](analysis-services-connect.md)   
[クライアント ライブラリ](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
