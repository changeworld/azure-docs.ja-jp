---
title: Azure Healthcare API のログ記録
description: この記事では、ログのしくみと、Azure Healthcare API のログ記録を有効にする方法について説明します
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787529"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Azure Healthcare API のログ記録 (プレビュー)

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

Azure プラットフォームには、3 種類のログ、アクティビティ ログ、リソース ログ、Azure Active Directoryがあります。 アクティビティ ログの詳細を [参照してください](../azure-monitor/essentials/platform-logs-overview.md)。 この記事では、Azure Healthcare API のログ記録のしくみについて説明します。

## <a name="auditlogs"></a>AuditLogs
Azure portal から各 Azure リソースに対してアクティビティ ログを使用できる一方で、Healthcare API は、AuditLogs と DiagnosticLogs の 2 つのカテゴリを含むリソース ログを出力します。

- AuditLogs は、ユーザーまたはアプリケーションが FHIR サービスにアクセスするときに呼び出し元の IP アドレスやリソース URL など、医療サービスの監査証跡を提供します。 各サービスは、必要なプロパティを出力し、必要に応じて追加のプロパティを実装します。
- DiagnosticLogs は、ログ レベル (情報、警告、エラー) やログ メッセージなど、サービスの操作に関する分析情報を提供します。

現在、医療 API では、パブリック プレビューの AuditLogs のみをサポートしています。 DiagnosticLogs は、サービスが一般提供されている場合に使用できます。

AuditLog の 1 つの例を次に示します。

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>次の手順

医療 API でサポートされている最新のメトリックについては、こちらをAzure Monitor [覧ください](../azure-monitor/essentials/metrics-supported.md)。

DICOM サービス のログとメトリックの詳細については、こちらを参照 [してください](./dicom/enable-diagnostic-logging.md)。

ログとメトリックのIoT Connectorについては、こちらを参照[してください](./azure-api-for-fhir/iot-metrics-display.md)。