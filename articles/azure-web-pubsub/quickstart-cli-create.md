---
title: クイックスタート - Azure CLI を使用して Web PubSub インスタンスを作成する
description: Azure CLI を使用して Web PubSub インスタンスを作成する方法を説明するクイックスタート
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 426b77d1e1a79c3a9818e028edcc79bcad5ef2ec
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998084"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>クイックスタート: Azure CLI を使用して Web PubSub インスタンスを作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 このクイックスタートでは、Azure CLI を使用して Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub インスタンスを作成する

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>新しく作成したインスタンスを試す

> [!div class="nextstepaction"]
> [CLI を使用して、新しく作成したインスタンスを試す](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [ブラウザーから、新しく作成したインスタンスを試す](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

実際のアプリケーションでは、さまざまな言語の SDK を使用して独自のアプリケーションを構築できます。 また、サーバーレス アプリケーションを簡単に構築するための関数の拡張機能も用意しています。

[!INCLUDE [next step](includes/include-next-step.md)]
