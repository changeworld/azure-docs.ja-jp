---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026637"
---
CRON 式で使用する既定のタイム ゾーンは、協定世界時 (UTC) です。 別のタイム ゾーンに基づく CRON 式を使うには、Function App 用に `WEBSITE_TIME_ZONE` という名前のアプリ設定を作成します。 

この設定の値は、関数アプリを実行するオペレーティング システムとプランによって異なります。

|オペレーティング システム |プラン |値 |
|-|-|-|
| **Windows** |All | Windows コマンド `tzutil.exe /L` によって指定された各ペアの 2 行目に指定されているように、この値を目的のタイム ゾーンの名前に設定します。 |
| **Linux** |Premium<br/>専用 |この値を、[tz データベース](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)に関するページに示されている目的のタイム ゾーンの名前に設定します。 |

> [!NOTE]
> Linux 従量課金プランでは、現在 `WEBSITE_TIME_ZONE` はサポートされていません。

たとえば、米国の東部標準時 (`Eastern Standard Time` (Windows) または `America/New_York` (Linux)) では現在、標準時では UTC-05:00、夏時間では UTC-04:00 を使用します。 タイマー トリガーが毎日東部標準時の午前 10 時に発生するように設定するには、関数アプリのアプリ設定を `WEBSITE_TIME_ZONE` という名前で作成し、その値を `Eastern Standard Time` (Windows) または `America/New_York` (Linux) に設定して、次の NCRONTAB 式を使用します。 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE` を使用すると、夏時間などの特定のタイムゾーンでの時間変更や標準時での変更に対応するように、時刻が調整されます。
