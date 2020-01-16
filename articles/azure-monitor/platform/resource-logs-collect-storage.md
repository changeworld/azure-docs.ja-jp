---
title: Azure リソース ログをストレージ アカウントにアーカイブする | Microsoft Docs
description: Azure リソース ログを長期保有するためにストレージ アカウントにアーカイブする方法を説明します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e99a0e8e42bcfb5c7967a9cb1c91631bacbea53a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980072"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure リソース ログをストレージ アカウントにアーカイブする
Azure のアクティビティ ログとリソース ログを含む Azure の[プラットフォーム ログ](platform-logs-overview.md)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。  この記事では、アーカイブ対象のデータを保持するための、Azure ストレージ アカウントへのプラットフォーム ログの収集について説明します。

## <a name="prerequisites"></a>前提条件
[Azure ストレージ アカウント](../../storage/common/storage-account-create.md)をまだお持ちでない場合は、作成する必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセス権を持っている限り、ストレージ アカウントはログを送信するリソースと同じサブスクリプションに属している必要はありません。


> [!IMPORTANT]
> Azure Data Lake Storage Gen2 アカウントは、Azure portal では有効なオプションとして表示されていますが、診断設定の送信先としては現在サポートされていません。


データへのアクセスをより適切に制御できるように、他の非監視データが格納されている既存のストレージ アカウントは使用しないでください。 アクティビティ ログとリソース ログを一緒にアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じストレージ アカウントを使用できます。

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
Azure リソース用の診断設定を作成して、ストレージやその他の送信先にプラットフォーム ログを送信します。 詳細については、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。


## <a name="collect-data-from-compute-resources"></a>コンピューティング リソースからのデータの収集
診断設定によって、他のリソースと同様に、Azure コンピューティング リソースのリソース ログが収集されますが、ゲスト オペレーティング システムやワークロードについては収集されません。 このデータを収集するには、[Windows Azure Diagnostics エージェント](diagnostics-extension-overview.md)をインストールします。 詳細については、「[Azure Storage への診断データの保存と表示](diagnostics-extension-to-storage.md)」を参照してください。


## <a name="schema-of-platform-logs-in-storage-account"></a>ストレージ アカウントのプラットフォーム ログのスキーマ

診断設定の作成後、有効にしたいずれかのログ カテゴリのイベントが発生するとすぐ、ストレージ アカウントにストレージ コンテナーが作成されます。 コンテナー内の BLOB には、次の名前付け規則が使用されます。

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、ネットワーク セキュリティ グループの BLOB には、次のような名前を付けることができます。

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h = 12)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 リソース ログ イベントは 1 時間ごとに個々の BLOB に分類されるため、分の値 (m = 00) は常に 00 です。

PT1H.json ファイル内では、各イベントは、次の形式で保存されます。 これには一般的な最上位スキーマを使用しますが、「[リソース ログのスキーマ](diagnostic-logs-schema.md)」および「[アクティビティ ログのスキーマ](activity-log-schema.md)」で説明されているように、各 Azure サービスに固有です。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> プラットフォーム ログは、[JSON Lines](http://jsonlines.org/) を使用して BLOB ストレージに書き込まれ、各イベントは 1 行で示され、改行文字は新しいイベントがあることを示します。 この形式は、2018 年 11 月に実装されました。 この日付より前は、「[ストレージ アカウントにアーカイブされている Azure Monitor プラットフォーム ログの形式変更のための準備](resource-logs-blob-format.md)」で説明されているように、ログはレコードの JSON 配列として BLOB ストレージに書き込まれていました。

## <a name="next-steps"></a>次のステップ

* [リソース ログの詳細について読む](platform-logs-overview.md)
* [Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)
* [分析のための BLOB のダウンロード](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Azure Monitor による Azure Active Directory ログのアーカイブ](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
