---
title: インクルード ファイル
description: インクルード ファイル
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 03a1235b9bf20fa19f378802923d3bab7dbf4900
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
| リソース | 上限 |
| --- | --- |
| Automation アカウントあたりの 30 秒ごとに送信できる新しいジョブの最大数 (スケジュールされていないジョブ) |100 |
| Automation アカウントあたりのインスタンスの同じ時刻に同時実行するジョブの最大数 (スケジュールされていないジョブ) |200 |
| Automation アカウントあたりの 30 秒ごとにインポートできるモジュールの最大数 |5 |
| モジュールの最大サイズ |100 MB |
| ジョブ実行時間 - Free レベル |カレンダー月あたりサブスクリプションごとに 500 分 |
| ジョブに割り当てるメモリの最大量 <sup>1</sup> |400 MB |
| ジョブごとに許可されるネットワーク ソケットの最大数 <sup>1</sup> |1,000 |
| サブスクリプションでの Automation アカウントの最大数 |制限なし |

<sup>1</sup> これらの制限は、Azure のサンドボックスにのみ適用されます。ハイブリッド worker の場合、ハイブリッド worker が使用しているコンピューターの機能によってのみ制限されます。