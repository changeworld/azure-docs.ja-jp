---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027689"
---
以下の手順では、次の構成を使用しました。

- コンピューター:Ubuntu Server 18.04
- 依存関係: strongSwan


次のコマンドを使用して、必要な strongSwan 構成をインストールします。

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

次のコマンドを使用して、Azure コマンド ライン インターフェイスをインストールします。

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI のインストール方法の詳細](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)