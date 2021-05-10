---
title: クイックスタート:Synapse SQL プールのコンピューティングをスケーリングする (Azure portal)
description: Azure portal を使用して、Synapse SQL プール (データ ウェアハウス) のコンピューティングをスケーリングできます。
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534228"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>クイック スタート:Azure portal を使用して Synapse SQL プールのコンピューティングをスケーリングする

Azure portal を使用して、Synapse SQL プール (データ ウェアハウス) のコンピューティングをスケーリングできます。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に

既存の SQL プールをスケーリングできます。または、[ポータルでの作成と接続に関するクイックスタート](create-data-warehouse-portal.md)を使用して、**mySampleDataWarehouse** という名前の SQL プールを作成してください。 このクイックスタートでは、**mySampleDataWarehouse** をスケーリングします。

>[!IMPORTANT] 
>スケーリングする SQL プールがオンラインになっている必要があります。 

## <a name="scale-compute"></a>コンピューティングのスケーリング

SQL プールのコンピューティング リソースは、Data Warehouse ユニットを増減することによってスケーリングできます。 「[クイック スタート:Azure portal を使用して Synapse SQL プールを作成し、クエリを実行する](create-data-warehouse-portal.md)」で、**mySampleDataWarehouse** を作成して 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには:

1. Azure portal の左側のページで **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。
2. **[Azure Synapse Analytics (以前の SQL DW)]** ページから **[mySampleDataWarehouse]** を選択します。 SQL プールが開きます。
3. **[スケール]** をクリックします。

    ![[スケール] をクリックします。](./media/quickstart-scale-compute-portal/click-scale.png)

2. [スケール] パネルで、スライダーを左または右に移動して DWU 設定を変更します。 次に、[スケール] を選択します。

    ![スライダーの移動](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>次のステップ
SQL プールに関する理解をさらに深めるために、[SQL プールへのデータの読み込み](./load-data-from-azure-blob-storage-using-copy.md)に関するチュートリアルに進んでください。
