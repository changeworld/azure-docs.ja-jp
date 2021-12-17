---
title: '`arcdata` 拡張機能をインストールする'
description: Azure (az) CLI 用 `arcdata` 拡張機能をインストールする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5e1ddfdc13ff7bf11a2a1293ebf39c4c527c6d0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750280"
---
# <a name="install-arcdata-azure-cli-extension"></a>`arcdata` Azure CLI 拡張機能をインストールする

> [!IMPORTANT]
> 新しいリリースに更新する場合は、最新バージョンの Azure CLI と `arcdata` 拡張機能も更新するようにしてください。


## <a name="install-latest-azure-cli"></a>最新の Azure CLI をインストールする 

最新の Azure CLI を取得するには、[Azure CLI をインストールする](/cli/azure/install-azure-cli)方法に関する記事を参照してください。


## <a name="add-arcdata-extension"></a>`arcdata` 拡張機能の追加

拡張機能をインストールするには、次のコマンドを実行します。 

```azurecli
az extension add --name arcdata 
```

[Azure CLI の拡張機能について詳しく説明します](/cli/azure/azure-cli-extensions-overview)。

## <a name="update-arcdata-extension"></a>`arcdata` 拡張機能の更新

拡張機能を既にお持ちの場合は、次のコマンドを実行して更新できます。

```azurecli
az extension update --name arcdata
```

## <a name="next-steps"></a>次のステップ

[Azure Arc データ コントローラーを作成する](create-data-controller.md)
