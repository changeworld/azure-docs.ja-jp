---
ms.service: hpc-cache
ms.topic: include
ms.date: 06/17/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 37f2895ed476cc0dbe0bdb3def034a17448d8dca
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587226"
---
| 使用モデル | キャッシュ モード | バックエンド検証 | ライトバックの最大遅延 |
|--|--|--|--|
| 負荷が高く、頻度の低い書き込みを読み取ります <!--read_heavy_infreq-->| Read | なし | なし |
| 書き込みが 15% を超えています <!--write_workload_15-->| 読み取り/書き込み | 8 時間 | 1 時間 |
| クライアントはキャッシュをバイパス <!--write_around-->| Read | 30 秒 | なし |
| 書き込みが 15% を超え、頻繁なバックエンド チェックが発生しています (30 秒) <!--write_workload_check_30-->| 読み取り/書き込み | 30 秒 | 1 時間 |
| 書き込みが 15% を超え、頻繁なバックエンド チェックが発生しています (60 秒) <!--write_workload_check_60-->| 読み取り/書き込み | 60 秒 | 1 時間 |
| 書き込みが 15% を超え、頻繁なライトバックが発生しています <!--write_workload_cloudws-->| 読み取り/書き込み | 30 秒 | 30 秒 |
| 読み取りの負荷が高く、バッキング サーバーの確認が 3 時間ごとに行われます <!--read_heavy_check_180-->| Read | 3 時間 | なし |
