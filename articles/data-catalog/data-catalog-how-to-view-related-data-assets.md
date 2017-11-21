---
title: "Azure Data Catalog の関連するデータ資産を表示する方法 | Microsoft Docs"
description: "この記事では、Azure Data Catalog で選択したデータ資産に関連するデータ資産を表示する方法について説明します。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: da7ac8f323f9b2ec6303fbc85aa570373a538bcf
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Azure Data Catalog で関連するデータ資産を表示する方法を説明します。
Azure Data Catalog を使用すると、選択したデータ資産に関連するデータ資産を表示し、それらの関係を表示できます。 

## <a name="supported-data-sources"></a>サポートされるデータ ソース 
次のデータ ソースからデータ資産を登録すると、選択したデータ資産間の結合関係に関するメタデータが Azure Data Catalog に自動的に登録されます。 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Data Catalog で 2 つのデータ資産間のリレーションシップをインポートするには、これらの資産を同時に登録する必要があります。 これらのいずれかを個別に追加している場合は、再度これともう一方のデータ資産を追加して、それらの間のリレーションシップをインポートします。

## <a name="view-related-data-assets"></a>関連するデータ資産の表示
選択したデータ セットに関連するデータ資産を表示するには、次の図に示すように、**[リレーションシップ]** タブを使用します。 

![Azure Data Catalog - 関連するデータ資産の表示](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

この例では、選択した **ProductSubcategory** データ資産に 2 つの関係があります。 

- Product テーブルの ProductSubcategoryID 列は、選択した ProductSubcategory テーブルの ProductSubcategoryID 列との間に外部キー リレーションシップがあります。 
- ProductSubCategory テーブルの ProductCategoryID 列は、選択した ProductCategory テーブルの ProductCategoryID 列との間に外部キー リレーションシップがあります。

> [!NOTE]
> リレーションシップ ツリー ビューの矢印の方向に注意してください。  

列の完全修飾名などの詳細を表示するには、マウスを移動して上に重ねます。次の画像のようなポップアップが表示されます。 

![Azure Data Catalog - リレーションシップのポップアップ](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

既に登録されている資産間のリレーションシップを含めるには、これらの資産を再登録します。

## <a name="next-steps"></a>次のステップ
- [データ資産を管理する方法](data-catalog-how-to-manage.md)