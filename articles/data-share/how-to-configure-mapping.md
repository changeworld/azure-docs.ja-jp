---
title: Azure Data Share でデータセットのマッピングを構成する
description: Azure Data Share を使用して、受信した共有のデータセットのマッピングを構成する方法について説明します。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964245"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure Data Share で受信した共有のデータセットのマッピングを構成する方法

この記事では、Azure Data Share で受信した共有のデータセットのマッピングを構成する方法について説明します。 データ共有への招待を受け入れて [Accept and configure later]\(受け入れて後で構成する\) を選択した場合、またはデータがインプレースで共有される場合は、これを行うことができます。 データの共有先を変更する必要がある場合、またはデータを SQL Server 内に受信する場合は、データセットのマッピングを構成できます。 

## <a name="navigate-to-a-received-data-share"></a>受信したデータ共有に移動する

Azure Data Share サービスで、受信した共有に移動し、 **[詳細]** タブを選択します。 

![データセットのマッピング](./media/dataset-mapping.png "データセットのマッピング") 

ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 既存のマッピングをマップ解除するには、 **[マップ解除]** を選択します。 **[+ ターゲットへのマップ]** を選択して、新しいターゲット先ストアを選択します。 

![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

## <a name="select-a-new-target-store"></a>新しいターゲット ストアを選択する

データを配置するターゲットのデータ型を選択します。 スナップショット ベースの共有の場合、以前にマップされたストレージ アカウント内に既に存在しているデータが、新しいターゲット ストアに自動的に移動されることはありません。 インプレース共有の場合は、指定された場所にあるデータ ストアを選択します。 この場所は、データ プロバイダーのソース データ ストアが配置されている Azure データ センターです。

![ターゲット ストレージ アカウント](./media/dataset-map-target-sql.png "ターゲット ストレージ") 

## <a name="select-a-file-format-sql-sources-only"></a>ファイル形式の選択 (SQL ソースのみ)

ソース データが SQL ベースのソースからのものである場合は、どの形式で受信するかを選択できます。 

![形式の選択](./media/sql-file-formats.png "SQL ファイルの形式")

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。



