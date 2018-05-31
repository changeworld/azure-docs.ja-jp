---
title: Azure REST API による Security Center ポリシーへの準拠の確認 | Microsoft Docs
description: REST API を利用してSecurity Center ポリシーに準拠しているかどうかを確認する方法を説明します｡
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077767"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>REST API を利用した Security Center ポリシーへの準拠の確認

Security Center では､ご利用の Azure リソースが定義済みのセキュリティ ポリシーに準拠しているかどうかを定期的に検証しています｡ Security Center はまた､ご利用のアプリケーションから準拠を確認するための REST API の提供もしています｡このためサービスに対して直接にクエリを実行したり､JSON 結果の他のアプリケーションにインポートしたりできます｡ 

ここでは､サブスクリプションに関連付けられているすべての Azure リソースから現在の一群のレコメンデーションを取得する方法を説明します｡

現在の一群のレコメンデーションを取得するには､
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>要求を作成する  

`{subscription-id}` パラメーターは必須であり､ポリシーを定義している Azure サブスクリプションのサブスクリプション ID を指定する必要があります｡ 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)を参照してください｡  

`api-version` パラメーターは必須です。 現時点では､これらのエンドポイントは `api-version=2015-06-01-preview` でのみサポートされています｡ 

次のヘッダーは必須です｡ 

|要求ヘッダー|[説明]|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [ アクセス トークン](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します｡ |  

## <a name="response"></a>Response  

応答に成功すると､状態コード 200 (OK) が返されます｡応答は､Azure リソースを保護するための推奨タスクの一覧です｡

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

**value** 内の項目はそれぞれお勧めを表します｡

|Response プロパティ|[説明]|
|----------------|----------|
|**state** | レコメンデーションが `active` または `resolved` のどちらであるかを示します｡ |
|**creationTimeUtc** | UTC での日時｡レコメンデーションが作成された日時を示します｡ |
|**lastStateChangeUtc** | 前回状態変更の UTV 日時 (該当する場合)｡ |
|**securityTaskParameters** | レコメンデーションの詳細｡プロパティは対象のレコメンデーションによって異なります｡ |
||
  
現在サポートされているレコメンデーションについては､[Implement security recommendations](https://docs.microsoft.com/azure/security-center/security-center-recommendations)をご覧ください｡

その他の状態コードは､エラー状態を示します｡ その場合､response オブジェクトには､要求が失敗した理由を示す説明が含まれます｡

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>応答の例  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

この応答は､2 件のレコメンデーションを示しています｡一覧のそれぞれの項目は特定のレコメンデーションに対応しています｡ 1 つ目は Linux 仮想マシンのストレージの暗号化､2 つ目は SQL サーバーに対する監査を有効にするという提案です｡

これらのレコメンデーションは､実際に有効にされているポリシーによって異なります｡ 現在利用できるレコメンデーションなどの詳細は､[Managing security recommendations in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)をご覧ください｡


## <a name="see-also"></a>関連項目  
- [セキュリティ ポリシーの設定](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure セキュリティ リソース プロバイダーとしての REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Azure Rest API の開始](https://docs.microsoft.com/rest/api/azure/)   
- [Azure Security Center の PowerShell モジュール](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
