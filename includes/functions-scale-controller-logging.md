---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081072"
---
| プロパティ | 説明 |
|--|--|
|**`<DESTINATION>`**| ログの送信先。 有効な値は `AppInsights` と `Blob` です。<br/>`AppInsights` を使用する場合は、[関数アプリで Application Insights が有効になっている](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration)ことを確認してください。<br/>宛先を `Blob` に設定すると、`AzureWebJobsStorage` アプリケーション設定で設定されている既定のストレージ アカウントの `azure-functions-scale-controller` という名前の BLOB コンテナーにログが作成されます。 |
|**`<VERBOSITY>`** | ログ記録のレベルを指定します。 サポートされている値は、`None`、`Warning`、および `Verbose` です。<br/>`Verbose` に設定すると、スケール コントローラーは、すべてのワーカー数の変更の理由と、それらの決定の要因となるトリガーに関する情報をログに記録します。 詳細ログには、トリガー警告と、スケール コントローラーの実行前と実行後にトリガーによって使用されたハッシュが含まれます。 |

> [!TIP]
> スケール コントローラーのログを有効にしたままにすると、[関数アプリの監視にかかるかもしれないコスト](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits)に影響することに注意してください。 スケール コントローラーの動作を理解するのに十分なデータを収集するまでログ記録を有効にし、その後は無効にすることを検討してください。
