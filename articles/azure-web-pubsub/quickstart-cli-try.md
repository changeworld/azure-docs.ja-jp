---
title: クイックスタート - Azure Web PubSub インスタンスに接続して、操作する
description: Azure CLI からインスタンスを操作する方法を示すクイックスタート
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 99045ab4675527f4f0a821025bf6f870d63bb296
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998069"
---
# <a name="quickstart-connect-to-the-azure-web-pubsub-instance-from-cli"></a>クイックスタート: CLI から Azure Web PubSub インスタンスに接続する

このクイックスタートでは、[Azure コマンドライン インターフェイス (Azure CLI)](/cli/azure) を使用して、Azure Web PubSub インスタンスに接続し、接続されているクライアントにメッセージを発行する方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub インスタンスを作成する

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="play-with-the-instance"></a>インスタンスを操作する

[!INCLUDE [Try the Web PubSub instance](includes/cli-awps-client.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Web PubSub サービスに接続する方法と、接続されているクライアントにメッセージを発行する方法の基本概念について説明しています。

実際のアプリケーションでは、さまざまな言語の SDK を使用して独自のアプリケーションを構築できます。 また、サーバーレス アプリケーションを簡単に構築するための関数の拡張機能も用意しています。

[!INCLUDE [next step](includes/include-next-step.md)]
