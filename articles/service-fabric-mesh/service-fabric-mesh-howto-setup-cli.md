---
title: Azure Service Fabric Mesh CLI の設定 | Microsoft Docs
description: Azure Service Fabric Mesh CLI を設定する方法について説明します。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977433"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI の設定
Service Fabric Mesh コマンド ライン インターフェイス (CLI) は、Service Fabric Mesh でリソースをデプロイして管理するために必要です。 

プレビューでは、Azure Service Fabric Mesh CLI は Azure CLI の拡張機能として記述されています。 これは、Azure Cloud Shell または Azure CLI のローカル インストールでインストールできます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Service Fabric Mesh CLI をローカルにインストールする
CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.43 以降をインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 最新バージョンの CLI をインストールするか、最新バージョンにアップグレードする場合は、「[Azure CLI のインストール][azure-cli-install]」を参照してください。

次のコマンドを使用して、Azure Service Fabric Mesh CLI 拡張モジュールをインストールします。 

```azurecli-interactive
az extension add --name mesh
```

既存の Azure Service Fabric Mesh CLI モジュールを更新するには、次のコマンドを実行します。

```azurecli-interactive
az extension update --name mesh
```
## <a name="next-steps"></a>次の手順

[Windows 開発環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)を設定することもできます。

[よく寄せられる質問と問題](service-fabric-mesh-faq.md)に対する回答を確認します。

[azure-cli-install]: /cli/azure/install-azure-cli
