---
title: Azure Service Fabric mesh CLI の設定 | Microsoft Docs
description: Azure Service Fabric mesh CLI を設定する方法について説明します。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c0e2aefe1222263b169e21490da079b165a57321
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42108480"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Service Fabric mesh CLI の設定
Service Fabric mesh CLI は、Service Fabric mesh でリソースをデプロイして管理するために必要です。 

プレビューでは、Azure Service Fabric mesh CLI は Azure CLI の拡張機能として記述されています。 これは、Azure Cloud Shell または Azure CLI のローカル インストールでインストールできます。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.43 以降をインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 最新バージョンの CLI をインストールするか、最新バージョンにアップグレードする場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」を参照してください。

次のコマンドを使用して、Azure Service Fabric mesh CLI 拡張モジュールをインストールします。 

```azurecli-interactive
az extension add --name mesh
```

既存の Azure Service Fabric Mesh CLI モジュールを更新するには、次のコマンドを実行します。

```azurecli-interactive
az extension update --name mesh
```

[Windows 開発環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)を設定することもできます。

[azure-cli-install]: /cli/azure/install-azure-cli