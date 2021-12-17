---
title: インクルード ファイル
description: インクルード ファイル
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: f738a72c4316b51d13cd93194576549839d8127f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638984"
---
| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | ステージング コピー機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | Azure SQL Database または Azure Synapse Analytics との間でコピーするときにのみ必要です。その他の場合は省略可能です。 ポート 1433 を開かずに SQL Database または Azure Synapse Analytics にデータをコピーするには、ステージング コピー機能を使用します。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store との間でコピーするときにのみ必要です。その他の場合は省略可能です。 |
