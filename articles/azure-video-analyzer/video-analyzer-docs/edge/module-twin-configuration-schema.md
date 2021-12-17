---
title: Azure Video Analyzer モジュール ツイン JSON スキーマ
description: この記事では、Azure Video Analyzer edge モジュールのモジュールツイン JSON スキーマの概要について説明します。
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 360102a551ea6321a4b40063123176fc13672929
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559392"
---
# <a name="module-twin-configuration-schema"></a>モジュール ツインの構成スキーマ

[!INCLUDE [header](includes/edge-env.md)]

デバイス ツインは、デバイスに関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 Azure IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 詳細については、「[IoT Hub のモジュール ツインの理解と使用](../../../iot-hub/iot-hub-devguide-module-twins.md)」を参照してください。

このトピックでは、Azure Video Analyzer edge モジュールのモジュールツイン JSON スキーマについて説明します。

## <a name="module-twin-properties"></a>モジュール ツイン プロパティ

Video Analyzer edge モジュールは、次のモジュールツインプロパティを公開します。

| プロパティ                    | 必須 | 動的 | 説明                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | はい      | いいえ      | エッジモジュールを検証し、クラウドサービスをプロビジョニングするための認証トークン (Video Analyzer アカウントへのアクセスを含む) |
| `ApplicationDataDirectory`    | はい      | いいえ      | マウントされているボリュームに構成を保存する目的でマップされる、モジュールのファイル システム内のパス。       |
| `DiagnosticsEventsOutputName` | いいえ       | はい     | 診断イベントのハブ出力。 (空白は診断が公開されていないことを意味します) |
| `OperationalEventsOutputName` | いいえ       | はい     | 操作イベントのハブ出力。 (空白は操作イベントが公開されていないことを意味します) |
| `LogLevel`                    | いいえ       | はい     | 次のいずれか: · 詳細 · 情報 (既定) · 警告 · エラー · なし |
| `LogCategories`               | いいえ       | はい     | 以下のコンマ区切りリスト: Application、MediaPipeline、Events 既定: Application、Events |
| `AllowUnsecuredEndpoints`     | いいえ       | はい     | `#Microsoft.VideoAnalyzer.UnsecuredEndpoint` など、セキュリティで保護されていないエンドポイントでトポロジを作成することを許可するブール値の設定、既定 -true        |
| `TelemetryOptOut`             | いいえ       | いいえ     | テレメトリ送信をオプトアウトするためのブール値の設定、既定 -false       |
| `DebugLogsDirectory`          | いいえ       | はい     | デバッグ ログのディレクトリ。 存在する場合、ログが生成されます。存在しない場合、デバッグ ログは無効になります。       |

動的プロパティは、モジュールを再起動しなくても更新できます。 

オプションの診断設定の役割の詳細については、「[監視とログ](monitor-log-edge.md)」を参照してください。

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>次のステップ

[ダイレクト メソッド](direct-methods.md)
