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
ms.openlocfilehash: 34cae9172d9b024bd6866742d39d82ad496bfc52
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45570413"
---
| リソース | 上限 |メモ|
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
| Runbook ジョブの最大パラメーター サイズ   | 512 KB||
| Runbook の最大パラメーター   | 50|パラメーターに JSON または XML 文字列を渡し、パラメーターの 50 の上限に達したら、Runbook を使用して解析することができます。|
| Webhook のペイロードの最大サイズ |  512 KB|

**<sup>1</sup>** サンドボックスは、複数のジョブで利用できる共有環境です。同じサンドボックスを利用するジョブには、そのサンドボックスのリソース制限が適用されます。
