---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/27/2021
ms.author: larryfr
ms.openlocfilehash: 366291acbf847ed38c5b598c76d9b9a14b2b32a3
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110616995"
---
> [!IMPORTANT]
> この記事の Azure CLI コマンドでは、Azure Machine Learning 用に `azure-cli-ml`、つまり 1.0 の拡張機能が __必要__ になります。 `ml` 拡張機能を使用した拡張 2.0 CLI (プレビュー) が現在利用可能であり、推奨されています。 これらの拡張機能には互換性がありません。そのため、2.0 CLI のコマンドは、この記事の手順では使用できません。 ただし、機械学習ワークスペースおよび基になるすべてのリソースは、どちらからでも操作できます。つまり、あるユーザーが 1.0 CLI を使用してワークスペースを作成し、別のユーザーが 2.0 CLI を使用してその同じワークスペースにジョブを送信できます。
>
> インストール済みの拡張機能を確認するには、`az extension list` を使用します。 __拡張機能__ のリストに `azure-cli-ml` が含まれている場合は、この記事の手順用の適切な拡張機能がインストールされています。
>
> 異なる拡張機能のインストールと使用の詳細については、以下の記事を参照してください。
> 
> * `azure-cli-ml` - [1.0 CLI をインストール、設定、および使用する](../articles/machine-learning/reference-azure-machine-learning-cli.md)
> * `ml` - [2.0 CLI をインストール、設定、および使用する](../articles/machine-learning/how-to-configure-cli.md)
