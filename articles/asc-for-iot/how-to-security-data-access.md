---
title: ASC for IoT を使用してデータにアクセスする (プレビュー) | Microsoft Docs
description: ASC for IoT を使用する場合に、セキュリティのアラートと推奨事項のデータにアクセスする方法について説明します。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541535"
---
# <a name="access-your-security-data"></a>セキュリティ データにアクセスする 

> [!IMPORTANT]
> ASC for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ASC for IoT は、Log Analytics ワークスペースにセキュリティのアラート、推奨事項、および未加工のセキュリティ データ (保存する場合) を格納します。

## <a name="log-analytics"></a>Log Analytics

使用する Log Analytics ワークスペースを構成するには:

1. IoT ハブを開きます。
1. **[セキュリティ]** をクリックします。
2. **[設定]** をクリックし、Log Analytics ワークスペースの構成を変更します。

構成後の Log Analytics ワークスペースにアクセスするには:

1. ASC for IoT でアラートを選択します。 
2. **[Further investigation]** \(さらに調査\) をクリックし、**[To see which devices have this alert click here and view the DeviceId column]** \(このアラートがどのデバイスのものかを確認するには、ここをクリックして DeviceId 列を見てください\) をクリックします。

Log Analytics からデータのクエリを実行する方法の詳細については、「[Log Analytics のクエリの概要](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)」を参照してください。

## <a name="security-alerts"></a>セキュリティのアラート

セキュリティのアラートは、構成されている Log Analytics ワークスペース内の **ASCforIoT.SecurityAlert** テーブルに格納されています。

次の基本的な kql クエリを使用すると、セキュリティのアラートの調査を開始できます。

### <a name="sample-records-query"></a>サンプル レコードのクエリ

いくつかのレコードをランダムに選択するには: 

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

#### <a name="sample-query-results"></a>サンプル クエリの結果 

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 説明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | デバイスに対するブルート フォース攻撃が成功しました        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | デバイスへのローカル ログインの成功が検出されました     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスへのローカル ログインの試行に失敗しました  | デバイスにローカル ログインの試行の失敗が検出されました |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary-query"></a>デバイスの概要クエリ

先週 IoT Hub が検出した個々のセキュリティのアラートの数、デバイス、アラートの重要度、アラートの種類を選択するには、この kql クエリを使用します。

```
// Select number of distinct security alerts detected last week by 
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

#### <a name="device-summary-query-results"></a>デバイスの概要クエリの結果

| IoTHubId | deviceId| AlertSeverity| DisplayName | Count |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 暗号化コイン採掘者                     | 4   |
|

### <a name="iot-hub-summary"></a>IoT Hub の概要

先週 IoT Hub によるアラートが発生した個々のデバイスの数、アラートの重要度、アラートの種類を選択するには、この kql クエリを使用します。

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

#### <a name="iot-hub-summary-query-results"></a>IoT Hub の概要クエリの結果

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | ブルート フォース攻撃が成功しました           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | デバイスでのローカル ログインの成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | 暗号化コイン採掘者                     | 1          |



## <a name="next-steps"></a>次の手順

- ASC for IoT の[概要](overview.md)を参照してください
- ASC for IoT の[アーキテクチャ](architecture.md)を参照してください
- [ASC for IoT アラート](concept-security-alerts.md)について参照し、理解を深めます
