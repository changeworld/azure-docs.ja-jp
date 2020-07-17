---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485951"
---
`deploymentconfig.json` ドキュメントのエントリは、[AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) のパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `computeType` | NA | コンピューティング ターゲット。 ACI の場合、値は `ACI` である必要があります。 |
| `containerResourceRequirements` | NA | CPU およびメモリ エンティティのコンテナー。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 割り当てる CPU コアの数。 既定値、`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | この Web サービスに割り当てるメモリの量 (GB 単位)。 既定値、`0.5` |
| `location` | `location` | この Web サービスのデプロイ先となる Azure リージョン。 指定されていない場合、ワークスペースの場所が使用されます。 利用できるリージョンの詳細はこちらにあります。[ACI リージョン](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | この Web サービスに対して認証を有効にするかどうか。 既定値は False です |
| `sslEnabled` | `ssl_enabled` | この Web サービスに対して SSL を有効にするかどうか。 既定値は False です。 |
| `appInsightsEnabled` | `enable_app_insights` | この Web サービスに対して AppInsights を有効にするかどうか。 既定値は False です |
| `sslCertificate` | `ssl_cert_pem_file` | SSL が有効な場合、証明書ファイルが必要です |
| `sslKey` | `ssl_key_pem_file` | SSL が有効な場合、キー ファイルが必要です |
| `cname` | `ssl_cname` | SSL が有効な場合の cname |
| `dnsNameLabel` | `dns_name_label` | スコアリング エンドポイントの dns 名ラベル。 指定されていない場合、一意の dns 名ラベルがスコアリング エンドポイントに対して生成されます。 |

次の JSON は、CLI で使用するデプロイ構成の例です。

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```