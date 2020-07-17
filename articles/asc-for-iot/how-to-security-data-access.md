---
title: セキュリティおよび推奨事項データにアクセスする
description: Azure Security Center for IoT を使用する場合に、セキュリティのアラートと推奨事項のデータにアクセスする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311005"
---
# <a name="access-your-security-data"></a>セキュリティ データにアクセスする

Azure Security Center for IoT は、Log Analytics ワークスペースにセキュリティのアラート、推奨事項、および未加工のセキュリティ データ (保存する場合) を格納します。

## <a name="log-analytics"></a>Log Analytics

使用する Log Analytics ワークスペースを構成するには:

1. IoT ハブを開きます。
1. **[セキュリティ]** セクションの下の **[概要]** ブレードをクリックします
1. **[設定]** をクリックし、Log Analytics ワークスペースの構成を変更します。

構成後に Log Analytics ワークスペース内でアラートと推奨事項にアクセスするには:

1. Azure Security Center for IoT でアラートまたは推奨事項を選択します。
1. **[Further investigation]** \(さらに調査\) をクリックし、 **[To see which devices have this alert click here and view the DeviceId column]** \(このアラートがどのデバイスのものかを確認するには、ここをクリックして DeviceId 列を見てください\) をクリックします。

Log Analytics からデータのクエリを実行する方法の詳細については、「[Log Analytics のクエリの概要](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)」を参照してください。

## <a name="security-alerts"></a>セキュリティのアラート

セキュリティ アラートは、Azure Security Center for IoT ソリューションに対して構成されている Log Analytics ワークスペース内の _AzureSecurityOfThings.SecurityAlert_ テーブルに格納されます。

セキュリティ アラートの調査を始めるときに役立つ便利なクエリがいくつか用意されています。

### <a name="sample-records"></a>サンプル レコード

レコードをいくつかランダムに選択します

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 説明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | デバイスに対するブルート フォース攻撃が成功しました        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | デバイスへのローカル ログインの成功が検出されました     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスへのローカル ログインの試行に失敗しました  | デバイスにローカル ログインの試行の失敗が検出されました |    { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary"></a>デバイスの概要

先週検出された個別のセキュリティ アラートの数を取得して、IoT ハブ、デバイス、アラートの重大度、アラートの種類ごとにグループ化します。

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 暗号化コイン採掘者                     | 4   |

### <a name="iot-hub-summary"></a>IoT ハブの概要

IoT Hub、アラートの重要度、アラートの種類で、先週アラートが発生した個々のデバイスの数を選択します

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | ブルート フォース攻撃が成功しました           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | デバイスでのローカル ログインの成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | 暗号化コイン採掘者                     | 1          |

## <a name="security-recommendations"></a>セキュリティに関する推奨事項

セキュリティに関する推奨事項は、Azure Security Center for IoT ソリューションに対して構成されている Log Analytics ワークスペース内の _AzureSecurityOfThings.SecurityRecommendation_ テーブルに格納されます。

セキュリティに関する推奨事項の調査を始めるときに役立つ便利なクエリがいくつか用意されています。

### <a name="sample-records"></a>サンプル レコード

レコードをいくつかランダムに選択します

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 説明 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | アクティブ | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | アクティブ | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>デバイスの概要

個別のアクティブなセキュリティに関する推奨事項の数を取得して、IoT Hub、デバイス、推奨事項の重大度、および種類ごとにグループ化します。

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 4   |

## <a name="next-steps"></a>次のステップ

- Microsoft Azure Security Center for IoT の[概要](overview.md)を読みます
- Azure Security Center for IoT の[アーキテクチャ](architecture.md)を確認します
- [Azure Security Center for IoT のアラート](concept-security-alerts.md)を理解し、探索します
- [Azure Security Center for IoT の推奨事項](concept-recommendations.md)を理解し、探索します
