---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: b960b19f67dee802b9058896d5924f883d7463a9
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846067"
---
`deploymentconfig.json` ドキュメントのエントリは、[LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration) のパラメーターにマッピングされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `computeType` | NA | コンピューティング ターゲット。 ローカル ターゲットの場合、値は `local` である必要があります。 |
| `port` | `port` | サービスの HTTP エンドポイントを公開するローカル ポート。 |

この JSON は、CLI で使用するデプロイ構成の例です。


:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/deploymentconfig.json":::

この JSON を `deploymentconfig.json` という名前のファイルとして保存します。