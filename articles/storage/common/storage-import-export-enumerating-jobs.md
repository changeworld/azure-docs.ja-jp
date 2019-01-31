---
title: すべての Azure Import/Export ジョブを列挙する | MicrosoftDocs
description: サブスクリプションに含まれるすべての Azure Import/Export サービス ジョブを列挙する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462923"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export サービス内のジョブの列挙
サブスクリプション内のすべてのジョブを列挙するには、[List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) 操作を呼び出します。 `List Jobs` は、ジョブの一覧と次の属性を返します。

-   ジョブのタイプ (インポートまたはエクスポート)

-   ジョブの現在の状態

-   ジョブに関連付けられたストレージ アカウント

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
