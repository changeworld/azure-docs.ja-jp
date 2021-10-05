---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699196"
---
### <a name="connection-string-and-instrumentation-key"></a>接続文字列とインストルメンテーション キー

コード不要の監視を使用している場合は、接続文字列のみが必要です。 ただし、手動のインストルメンテーションを実行する場合は、SDK の以前のバージョンとの下位互換性を維持するためにインストルメンテーション キーを設定することをお勧めします。

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights の標準メトリックと Azure App Service メトリックの違い

Application Insights では、アプリケーションに対して行われた要求のテレメトリを収集します。 WebApps/WebServer でエラーが発生し、要求がユーザー アプリケーションに到達しなかった場合、Application Insights にはそれに関するテレメトリがありません。

Application Insights によって計算された `serverresponsetime` の期間は、必ずしも Web Apps によって監視されているサーバーの応答時間と一致しません。 これは、Application Insights では、要求がユーザー アプリケーションに実際に到達した場合の期間のみがカウントされるためです。 WebServer で要求が停止しているか、キューに入れられた場合、その待機時間は Web Apps のメトリックに含まれますが、Application Insights のメトリックには含まれません。


