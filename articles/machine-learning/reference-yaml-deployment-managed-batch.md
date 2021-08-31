---
title: CLI (v2) マネージド バッチ デプロイ YAML スキーマ
titleSuffix: Azure Machine Learning
description: CLI (v2) マネージド バッチ デプロイ YAML スキーマのリファレンス ドキュメント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 284d9ac70c1ecb8929b31e6994a87686e1e4ec0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779549"
---
# <a name="cli-v2-managed-batch-deployment-yaml-schema"></a>CLI (v2) マネージド バッチ デプロイ YAML スキーマ

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>スキーマ

ソース JSON スキーマは https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json にあります。 スキーマは、便宜上、以下の JSON 形式と YAML 形式で提供されています。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/batchDeployment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/batchDeployment.schema.yml":::

---

## <a name="remarks"></a>解説

`az ml deployment` コマンドは、Azure Machine Learning のデプロイを管理するために使用できます。

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)
