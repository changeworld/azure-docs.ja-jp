---
title: リリース ノート:Azure Synapse Analytics (ワークスペース プレビュー)
description: Azure Synapse Analytics のリリース ノート (ワークスペース プレビュー)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031672"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Azure Synapse Analytics (ワークスペース プレビュー) のリリース ノート

この記事では、Azure Synapse Analytics (ワークスペース) の制限と問題について説明します。 関連情報については、[Azure Synapse Analytics (ワークスペース)](overview-what-is.md) に関するページを参照してください。

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- 問題およびユーザーへの影響:SDK によって作成されたワークスペースから、Synapse Studio を起動できません。

- 対処法:次の手順を実行します。 
  1.    `az synapse workspace create` を実行してワークスペースを作成します。
  2.    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` を実行してマネージド ID を抽出します。
  3.    ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` を実行して、ストレージ アカウントにロールとしてワークスペースを追加します。
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` を実行して、ファイアウォール規則を追加します。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse とは](overview-what-is.md)
* [開始するには](get-started.md)
* [FAQ](overview-faq.md)
