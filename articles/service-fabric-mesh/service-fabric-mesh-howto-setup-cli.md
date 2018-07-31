---
title: Azure Service Fabric mesh CLI の設定 | Microsoft Docs
description: Azure Service Fabric mesh CLI を設定する方法について説明します。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cf899e236b7fa21a90a0784e1e2b4fce0feae055
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213437"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定
Service Fabric mesh CLI は、Service Fabric mesh でリソースをデプロイして管理するために必要です。 

プレビューでは、Azure Service Fabric mesh CLI は Azure CLI の拡張機能として記述されています。 これは、Azure Cloud Shell または Azure CLI のローカル インストールでインストールできます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.35 以降をインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 最新バージョンの CLI をインストールするか、最新バージョンにアップグレードする場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」を参照してください。

Azure Service Fabric mesh CLI モジュールの以前のインストールを削除します。

```azurecli-interactive
az extension remove --name mesh
```

次のコマンドを使用して、Azure Service Fabric mesh CLI 拡張モジュールをインストールします。 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

[Windows 開発環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)を設定することもできます。

[azure-cli-install]: /cli/azure/install-azure-cli