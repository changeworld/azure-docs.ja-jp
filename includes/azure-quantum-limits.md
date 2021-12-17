---
title: インクルード ファイル
description: インクルード ファイル
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 10/27/2021
ms.author: dasto
ms.openlocfilehash: 1811faeeb5de3e300a35f7107c5cdf23bf20151c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520147"
---
### <a name="provider-limits--quota"></a>プロバイダーの制限とクォータ

Azure Quantum サービスでは、ファーストパーティとサードパーティの両方のサービス プロバイダーがサポートされています。 サードパーティ プロバイダーには、制限とクォータがあります。 ユーザーは、サードパーティ プロバイダーを構成するときに、Azure portal でプランと制限を表示できます。 

Microsoft のファーストパーティ最適化ソリューション プロバイダーの公開済みクォータ制限を下に示します。 

#### <a name="learn--develop-sku"></a>Learn & Develop SKU

| リソース | 制限 |
| --- | --- |
| CPU ベースの同時実行ジョブ | 最大 5<sup>1</sup> の同時実行ジョブ |
| FPGA ベースの同時実行ジョブ | 最大 2<sup>1</sup> の同時実行ジョブ |
| CPU ベースのソルバー時間 | 20 時間/月  |
| FPGA ベースのソルバー時間 | 1 時間/月  |

Learn & Develop SKU を使用している場合、クォータ制限の上限の引き上げを要求することは **できません**。 代わりに、Performance at Scale SKU に切り替える必要があります。

#### <a name="performance-at-scale-sku"></a>Performance at Scale SKU

| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| CPU ベースの同時実行ジョブ | 最大 100<sup>1</sup> の同時実行ジョブ | 既定の制限と同じ |
| FPGA ベースの同時実行ジョブ | 最大 10<sup>1</sup> の同時実行ジョブ | 既定の制限と同じ |
| ソルバー時間 | 1,000 時間/月  | 最大 50,000 時間/月 |

制限の引き上げを要求するには、Azure サポートにお問い合わせください。

詳細については、[Azure Quantum の価格に関するページ](https://aka.ms/AQ/Pricing)を参照してください。
サード パーティのオファリングについて詳しくは、Azure portal 内の関連プロバイダーの価格に関するページをご確認ください。

<sup>1</sup> 同時にキューに入れることができるジョブの数を示します。
