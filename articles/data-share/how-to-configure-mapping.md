---
title: Azure Data Share でデータセットのマッピングを構成する
description: Azure Data Share を使用して、受信した共有のデータセットのマッピングを構成する方法について説明します。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490624"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure Data Share で受信した共有のデータセットのマッピングを構成する方法

この記事では、Azure Data Share で受信した共有のデータセットのマッピングを構成する方法について説明します。 データ共有への招待を承諾し、[Accept and Configure later] (承諾して後で構成する) を選択した場合、これを行うことができます。 データの共有先を変更する必要がある場合、またはデータを SQL Server 内に受信する場合は、データセットのマッピングを構成できます。 

## <a name="navigate-to-a-received-data-share"></a>受信したデータ共有に移動する

Azure Data Share サービスで、受信した共有に移動し、 **[詳細]** タブを選択します。 

![データセットのマッピング](./media/dataset-mapping.png "データセットのマッピング") 

ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 既存のマッピングをマップ解除するには、 **[マップ解除]** を選択します。 **[+ ターゲットへのマップ]** を選択して、新しいターゲット先ストアを選択します。 

![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

## <a name="select-a-new-destination-store"></a>新しいターゲット先ストアを選択する

データを配置するターゲットのデータ型を選択します。 既にマッピングされているストレージ アカウントに存在するデータが、新しいターゲットに自動的に移動されることはありません。

![ターゲット ストレージ アカウント](./media/dataset-map-target-sql.png "ターゲット ストレージ") 

## <a name="select-a-file-format-sql-sources-only"></a>ファイル形式の選択 (SQL ソースのみ)

ソース データが SQL ベースのソースからのものである場合は、どの形式で受信するかを選択できます。 

![形式の選択](./media/sql-file-formats.png "SQL ファイルの形式")

## <a name="next-steps"></a>次の手順

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。



