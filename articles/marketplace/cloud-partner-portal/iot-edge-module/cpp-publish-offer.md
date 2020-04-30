---
title: Azure IoT Edge モジュール プランの発行 | Azure Marketplace
description: IoT Edge モジュール プランを発行する方法です。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141842"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge モジュール プランの発行

>[!Important]
>2020 年 4 月 13 日より、IoT Edge モジュール オファーの管理はパートナー センターに移行されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 移行後のオファーは、「[IoT Edge モジュール オファーの作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)」の手順に従って管理します。

 **[新しいプラン]** ページに情報を指定して新しいプランを作成した後、プランを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、"Go Live" へのプランの発行処理の主な手順を示しています。

![IoT Edge モジュール プランの発行手順](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表に、各手順を完了するための推定所要時間 (最大) と共に、各発行手順を示しています。
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **発行ステップ**           | **Time**    | **説明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 前提条件の検証         | 15 分   | プラン情報とプラン設定が有効化されます。                        |
| 認定                  | 2 週間 | オファーが Azure 認定チームによって分析されます。 この手順では、ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティ問題のスキャンを実行します。 また、この IoT Edge モジュールのプランが、すべての適性条件を満たしていることを確認します ([前提条件](./cpp-prerequisites.md)および[技術資産の準備](./cpp-create-technical-assets.md)に関するページをご覧ください)。 問題が見つかった場合、フィードバックが提供されます。 |
| 梱包 | 1 時間  | プランの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成され設定されます。 |
|  発行元のサインオフ             |  -        | オファーが稼働状態になる前に、最終的な発行元のレビューと確認が行われます。 (オファー情報の手順で) 選択されたサブスクリプション内にオファーをデプロイして、すべての要件を満たしていることを確認できます。  プランが次の手順に進めるように、 **[Go Live]\(ライブにする\)** を選択します。 |
| 梱包                 | 1 時間 | マーケットプレースの実稼働システムとリージョンに、完成したプランがレプリケートされます。 | 
| ライブ                           | 4 日 |プランが、必要なリージョンにリリース、レプリケートされて、一般公開されます。 |

発行プロセスが完了するまで最大 10 営業日の期間が許可され、プランがリリースされます。 発行プロセスが完了した後、IoT Edge モジュールが [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) に一覧表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Marketplace で既存の IoT Edge モジュール プランを更新する](./cpp-update-existing-offer.md)
