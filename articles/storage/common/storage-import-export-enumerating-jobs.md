---
title: すべての Azure Import/Export ジョブを列挙する | MicrosoftDocs
description: サブスクリプションに含まれるすべての Azure Import/Export サービス ジョブを列挙する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520882"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export サービス内のジョブの列挙
サブスクリプション内のすべてのジョブを列挙するには、[List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) 操作を呼び出します。 `List Jobs` は、ジョブの一覧と次の属性を返します。

-   ジョブのタイプ (インポートまたはエクスポート)

-   ジョブの現在の状態

-   ジョブに関連付けられたストレージ アカウント

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
