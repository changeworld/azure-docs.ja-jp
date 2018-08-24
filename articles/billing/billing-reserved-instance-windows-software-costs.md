---
title: Azure の予約における Windows ソフトウェアのコスト | Microsoft Docs
description: Azure 予約 VM インスタンスのコストに含まれない Windows ソフトウェア メーターについて説明します。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 8ff2d126fd491ddc40f6223b6d4ec1fc4505b8fb
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630113"
---
# <a name="windows-software-costs-not-included-with-azure-reservations"></a>Azure の予約に含まれない Windows ソフトウェアのコスト

予約仮想マシン インスタンスに Azure ハイブリッド使用特典がない場合、以下のセクションに記載されている Windows ソフトウェアの測定に関して料金が発生します。

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>予約のコストに含まれない Windows ソフトウェアの測定

| MeterId | 使用状況ファイルにおける MeterName | VM による使用 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | 占有 - Windows Svr バースト (1 コア) | B シリーズ |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | 占有 - Windows Svr バースト (2 コア) | B シリーズ |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | 占有 - Windows Svr バースト (4 コア) | B シリーズ |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | 占有 - Windows Svr バースト (8 コア) | B シリーズ |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | 占有 - Windows Svr (1 コア) | B シリーズを除くすべて |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | 占有 - Windows Svr (2 コア) | B シリーズを除くすべて |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | 占有 - Windows Svr (4 コア) | B シリーズを除くすべて |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | 占有 - Windows Svr (6 コア) | B シリーズを除くすべて |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | 占有 - Windows Svr (8 コア) | B シリーズを除くすべて |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | 占有 - Windows Svr (12 コア) | B シリーズを除くすべて |
| 02968a6b-1654-4495-ada6-13f378ba7172 | 占有 - Windows Svr (16 コア) | B シリーズを除くすべて |
| 175434d8-75f9-474b-9906-5d151b6bed84 | 占有 - Windows Svr (20 コア) | B シリーズを除くすべて |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | 占有 - Windows Svr (24 コア) | B シリーズを除くすべて |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | 占有 - Windows Svr (32 コア) | B シリーズを除くすべて |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | 占有 - Windows Svr (40 コア) | B シリーズを除くすべて |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | 占有 - Windows Svr (64 コア) | B シリーズを除くすべて |
| da612742-e7cc-4ca3-9334-0fb7234059cd | 占有 - Windows Svr (72 コア) | B シリーズを除くすべて |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | 占有 - Windows Svr (128 コア) | B シリーズを除くすべて |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | 占有 - Windows Svr (256 コア) | B シリーズを除くすべて |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | 占有 - Windows Svr (96 コア) | B シリーズを除くすべて |

これらのメーターごとのコストは、Azure RateCard API を使用して取得できます。 Azure メーターの料金を取得する方法については、「[Get price and metadata information for resources used in an Azure subscription (Azure サブスクリプションで使用されるリソースの料金とメタデータ情報を取得する)](https://msdn.microsoft.com/library/azure/mt219004)」を参照してください。

## <a name="next-steps"></a>次の手順
Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure での予約の管理](billing-manage-reserved-vm-instance.md)
- [予約割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。



