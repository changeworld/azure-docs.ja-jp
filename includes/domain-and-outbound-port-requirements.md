---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559286"
---
| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | セルフホステッド統合ランタイムが Azure Data Factory のデータ移動サービスに接続するために必要です。 |
| `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | Azure SQL Database または Azure SQL Data Warehouse との間でコピーするときにのみ必要です。その他の場合は省略可能です。 ポート 1433 を開かずに SQL Database または SQL Data Warehouse にデータをコピーするには、ステージング コピー機能を使用します。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Azure Data Lake Store との間でコピーするときにのみ必要です。その他の場合は省略可能です。 |
