---
title: Azure Containers イメージ オファーを発行する | Microsoft Docs
description: Azure コンテナー オファーを発行する方法。
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980218"
---
# <a name="publish-container-offer"></a>コンテナー オファーを発行する

 **[New Offer](新しいオファー)** ページを使用して新しいオファーを作成した後で、そのオファーを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、"Go Live" へのプランの発行処理の主な手順を示しています。

![コンテナー オファーの発行手順](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表で、発行の各手順について説明します。 各手順を完了するまでの推定時間も示されます。


|  **発行ステップ**           | **Time**    | **説明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 前提条件の検証         | 15 分   | プラン情報とプラン設定が有効化されます。                        |
| 認定                  | 1 週間 | オファーが Azure 認定チームによって分析されます。 ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティの問題についてオファーがスキャンされます。 適格性の条件がすべて満たされていることを確認するため、オファーがチェックされます。 詳細については、[前提条件](./cpp-prerequisites.md)と[技術資産の準備](./cpp-create-technical-assets.md)に関するページを参照してください。 問題が見つかった場合はフィードバックが提供されます。 |
| 梱包 | 1 時間  | プランの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成され設定されます。 |
|  発行元のサインオフ             |  -        | オファーが稼働状態になる前に、最終的な発行元のレビューと確認が行われます。 (プラン情報の手順で) 選択されたサブスクリプション内にプランをデプロイして、すべての要件を満たしていることを確認できます。  プランが次の手順に進めるように、**[Go Live]\(ライブにする\)** を選択します。 |
| 梱包                 | 1 時間 | マーケットプレースの実稼働システムとリージョンに、完成したオファーがレプリケートされます。 | 
| ライブ                           | 4 日 |プランが、必要なリージョンにリリース、レプリケートされて、一般公開されます。 |

発行プロセスが完了するまで最大 10 営業日の期間が許可され、プランがリリースされます。 発行プロセスが完了した後、コンテナー オファーが [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) に一覧表示されます。

## <a name="next-steps"></a>次の手順

[Azure Marketplace で既存のコンテナー オファーを更新する](./cpp-update-existing-offer.md)
