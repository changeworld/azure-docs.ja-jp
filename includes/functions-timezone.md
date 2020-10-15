---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569282"
---
CRON 式で使用する既定のタイム ゾーンは、協定世界時 (UTC) です。 別のタイム ゾーンに基づく CRON 式を使うには、Function App 用に `WEBSITE_TIME_ZONE` という名前のアプリ設定を作成します。 

この設定の値は、関数アプリを実行するオペレーティング システムとプランによって異なります。

|オペレーティング システム |プラン |値 |
|-|-|-|
| **Windows** |All | この値を、[Microsoft のタイム ゾーン インデックス](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))に関するページに示されている目的のタイム ゾーンの名前に設定します。 |
| **Linux** |Premium<br/>専用 |この値を、[tz データベース](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)に関するページに示されている目的のタイム ゾーンの名前に設定します。 |

> [!NOTE]
> Linux 従量課金プランでは、現在 `WEBSITE_TIME_ZONE` はサポートされていません。

たとえば、*Eastern Standard Time* (Windows) と *America/New_York* (Linux) は UTC-05:00 です。 タイマー トリガーが毎日東部標準時の午前 10 時に発生するように設定するには、UTC タイム ゾーンを考慮した次の NCRONTAB 式を使用できます。

```
"0 0 15 * * *"
``` 

または、関数アプリのアプリ設定を `WEBSITE_TIME_ZONE` という名前で作成し、その値を `Eastern Standard Time` (Windows) または `America/New_York` (Linux) に設定して、次の NCRONTAB 式を使用します。 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE` を使用すると、夏時間などの特定のタイムゾーンでの時間変更に対応するように、時刻が調整されます。 
