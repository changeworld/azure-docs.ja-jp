---
title: Azure Firewall のパブリック プレビューを有効にする
description: Azure PowerShell を使用して Azure Firewall のパブリック プレビューを有効にします
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992385"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Azure Firewall のパブリック プレビューを有効にする

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Azure PowerShell を使用して有効にする

Azure Firewall のパブリック プレビューを有効にするには、次の Azure PowerShell コマンドを使用します。

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

機能の登録が完了するまで、最長で 30 分かかります。 次の Azure PowerShell コマンドを実行することで、登録状態を確認できます。

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
登録が完了した後、次のコマンドを実行します。

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>次の手順

- [チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)

