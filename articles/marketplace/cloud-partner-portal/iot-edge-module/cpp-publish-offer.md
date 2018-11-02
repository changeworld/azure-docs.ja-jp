---
title: Azure IoT Edge モジュール プランの発行 | Microsoft Docs
description: IoT Edge モジュール プランを発行する方法です。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: dfa512a26334567301812ecefd8c5673b0ea4094
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955902"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge モジュール プランの発行

 **[新しいプラン]** ページに情報を指定して新しいプランを作成した後、プランを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、"Go Live" へのプランの発行処理の主な手順を示しています。

![IoT Edge モジュール プランの発行手順](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表に、各手順を完了するための推定所要時間 (最大) と共に、各発行手順を示しています。
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **発行手順**           | **Time**    | **説明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 前提条件の検証         | 15 分   | プラン情報とプラン設定が有効化されます。                        |
| 認定                  | 2 週間 | プランが Azure 認定チームによって分析されます。 この手順では、ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティ問題のスキャンを実行します。 また、この IoT Edge モジュールのプランが、すべての適性条件を満たしていることを確認します ([前提条件](./cpp-prerequisites.md)および[技術資産の準備](./cpp-create-technical-assets.md)に関するページをご覧ください)。 問題が見つかった場合、フィードバックが提供されます。 |
| 梱包 | 1 時間  | プランの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成され設定されます。 |
|  発行元のサインオフ             |  -        | プランがライブ状態になる前の、最終的な発行元のレビューと確認。 (プラン情報の手順で) 選択されたサブスクリプション内にプランをデプロイして、すべての要件を満たしていることを確認できます。  プランが次の手順に進めるように、**[Go Live]\(ライブにする\)** を選択します。 |
| 梱包                 | 1 時間 | マーケットプレースの実稼働システムとリージョンに、完成したプランがレプリケートされます。 | 
| ライブ                           | 4 日 |プランが、必要なリージョンにリリース、レプリケートされて、一般公開されます。 |

発行プロセスが完了するまで最大 10 営業日の期間が許可され、プランがリリースされます。 発行プロセスが完了した後、IoT Edge モジュールが [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) に一覧表示されます。

## <a name="next-steps"></a>次の手順

- [Azure Marketplace で既存の IoT Edge モジュール プランを更新する](./cpp-update-existing-offer.md)
