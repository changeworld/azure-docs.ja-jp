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
ms.openlocfilehash: b71e6d41dcdd7efb2d179486f9195c14dae97194
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037558"
---
| Resource | 上限 |メモ|
| --- | --- |---|
| Automation アカウントあたりの 30 秒ごとに送信できる新しいジョブの最大数 (スケジュールされていないジョブ) |100 |この制限に到達すると、次のジョブ作成要求は失敗します。 クライアントにエラー応答が届きます。|
| Automation アカウントあたりのインスタンスの同じ時刻に同時実行するジョブの最大数 (スケジュールされていないジョブ) |200 |この制限に到達すると、次のジョブ作成要求は失敗します。 クライアントにエラー応答が届きます。|
| Automation アカウントあたりの 30 秒ごとにインポートできるモジュールの最大数 |5 ||
| モジュールの最大サイズ |100 MB ||
| ジョブ実行時間 - Free レベル |カレンダー月あたりサブスクリプションごとに 500 分 ||
| サンドボックスごとに許可される最大ディスク容量**<sup>1</sup>** |1 GB |Azure サンドボックスにのみ適用されます|
| サンドボックスに割り当てる最大メモリ量 **<sup>1</sup>** |400 MB |Azure サンドボックスにのみ適用されます|
| サンドボックスごとに許可されるネットワーク ソケットの最大数 **<sup>1</sup>** |1,000 |Azure サンドボックスにのみ適用されます|
| Runbook ごとに許可される最長実行時間 **<sup>1</sup>** |3 時間 |Azure サンドボックスにのみ適用されます|
| サブスクリプションでの Automation アカウントの最大数 |制限なし ||
|1 つの Hybrid Runbook Worker で実行される同時実行ジョブの最大数|50 ||

**<sup>1</sup>** サンドボックスは、複数のジョブで利用できる共有環境です。同じサンドボックスを利用するジョブには、そのサンドボックスのリソース制限が適用されます。
