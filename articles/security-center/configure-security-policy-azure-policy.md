---
title: REST API を使用して Azure Policy セキュリティ ポリシーを作成および編集する
description: REST API を使用した Azure Policy ポリシー管理について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430943"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>REST API を使用して Azure Policy でセキュリティ ポリシーを構成する

Azure Policy とのネイティブ統合の一環として、Azure Security Center では、Azure Policy の REST API を利用してポリシー割り当てを作成できます。 次の手順では、ポリシー割り当ての作成と既存の割り当てのカスタマイズの手順について説明します。 

Azure Policy で重要な概念: 

- **ポリシー定義**はルールです 

- **イニシアティブ**はポリシー定義 (ルール) のコレクションです 

- **割り当て**は、特定のスコープ (管理グループ、サブスクリプションなど) にイニシアティブまたはポリシーを適用することです 

Security Center では、そのセキュリティ ポリシーをすべて含んだ組み込みイニシアティブがあります。 Azure リソースに対する Security Center のポリシーを評価するには、評価する管理グループやサブスクリプションで割り当てを作成する必要があります。

組み込みイニシアティブでは、既定で Security Center のポリシーがすべて有効になっています。 組み込みのイニシアチブから特定のポリシーを無効にすることもできます。 たとえば、**Web アプリケーション ファイアウォール**を除くすべての Security Center のポリシーを適用するには、ポリシーの effect パラメーターの値を **Disabled** に変更します。 

## <a name="api-examples"></a>API の例

以下の例では、次のように変数を置き換えてください。

- **{scope}** には、ポリシーを適用する管理グループまたはサブスクリプションの名前を入力します。
- **{poicyAssignmentName}** には、[関連するポリシー割り当ての名前](#policy-names)を入力します。
- **{name}** 名、またはポリシーの変更を承認する管理者の名前を入力します。

この例では、サブスクリプションまたは管理グループで組み込み Security Center イニシアティブを割り当てる方法を示します
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

この例では、次のポリシーを無効にして、サブスクリプションで組み込み Security Center イニシアティブを割り当てる方法を示します。 

- システムの更新プログラム ("systemUpdatesMonitoringEffect") 

- セキュリティ構成 ("systemConfigurationsMonitoringEffect") 

- エンドポイント保護 ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
この例では、割り当てを削除する方法を示します。
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>ポリシー名参照<a name="policy-names"></a>

|Security Center でのポリシー名|Azure Policy で表示されるポリシー名 |ポリシー効果パラメーター名|
|----|----|----|
|SQL の暗号化 |暗号化されていない SQL データベースの Azure Security Center での監視 |sqlEncryptionMonitoringEffect| 
|SQL 監査 |未監査の SQL データベースの Azure Security Center での監視 |sqlAuditingMonitoringEffect|
|システムの更新プログラム |システムの更新プログラムの欠落の Azure Security Center での監視 |systemUpdatesMonitoringEffect|
|ストレージ暗号化 |ストレージ アカウントでの BLOB 暗号化の欠落の監査 |storageEncryptionMonitoringEffect|
|JIT ネットワーク アクセス |可能なネットワーク Just In Time (JIT) アクセスの Azure Security Center での監視 |jitNetworkAccessMonitoringEffect |
|アダプティブ アプリケーション制御 |可能なアプリのホワイトリスト登録の Azure Security Center での監視 |adaptiveApplicationControlsMonitoringEffect|
|ネットワーク セキュリティ グループ |制限が少なすぎるネットワーク アクセスの Azure Security Center での監視 |networkSecurityGroupsMonitoringEffect| 
|セキュリティ構成 |OS の脆弱性の Azure Security Center での監視 |systemConfigurationsMonitoringEffect| 
|エンドポイント保護 |Endpoint Protection の欠落の Azure Security Center での監視 |endpointProtectionMonitoringEffect |
|ディスクの暗号化 |暗号化されていない VM ディスクの Azure Security Center での監視 |diskEncryptionMonitoringEffect|
|脆弱性評価 |VM の脆弱性の Azure Security Center での監視 |vulnerabilityAssessmentMonitoringEffect|
|Web アプリケーション ファイアウォール |保護されていない Web アプリケーションの Azure Security Center での監視 |webApplicationFirewallMonitoringEffect |
|次世代のファイアウォール |保護されていないネットワーク エンドポイントの Azure Security Center での監視| |


## <a name="next-steps"></a>次のステップ

その他の関連資料については、次の記事を参照してください。 

- [カスタム セキュリティ ポリシー](custom-security-policies.md)
- [セキュリティ ポリシーの概要](tutorial-security-policy.md)