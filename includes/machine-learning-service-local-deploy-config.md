---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: a7a6d9eb7e552ee5dededc58516927d0d8e0eef6
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107952176"
---
`deploymentconfig.json` ドキュメントのエントリは、[LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration) のパラメーターにマッピングされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `computeType` | NA | コンピューティング ターゲット。 ローカル ターゲットの場合、値は `local` である必要があります。 |
| `port` | `port` | サービスの HTTP エンドポイントを公開するローカル ポート。 |

この JSON は、CLI で使用するデプロイ構成の例です。

```json
{
    "computeType": "local",
    "port": 32267
}
```

この JSON を `deploymentconfig.json` という名前のファイルとして保存します。