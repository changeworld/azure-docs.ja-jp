---
title: Azure Firewall のアクティブ FTP のサポート
description: 既定では、アクティブ FTP は Azure Firewall で無効になっています。 PowerShell、CLI、および ARM テンプレートを使用して有効にできます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 2c89eb19025212490d29c97a061da52e779ecf42
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704399"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall のアクティブ FTP のサポート

アクティブ FTP では、FTP サーバーは、指定された FTP クライアント データ ポートへのデータ接続を開始します。 クライアント側ネットワーク上のファイアウォールは、通常、内部クライアント ポートに対する外部接続要求をブロックします。 詳細については、[アクティブ FTP とパッシブ FTP の明確な説明](https://slacksite.com/other/ftp.html)に関する記事を参照してください。

既定では、Azure Firewall のアクティブ FTP サポートは、FTP `PORT` コマンドを使用した FTP バウンス攻撃から保護するために無効になっています。 ただし、Azure PowerShell、Azure CLI、または Azure ARM テンプレートを使用してデプロイする場合は、アクティブ FTP を有効にすることができます。

> [!NOTE]
> 現在、アクティブ FTP は、仮想ネットワークにデプロイされたファイアウォールに対してのみサポートされています。 Azure Virtual WAN サポートは、後で追加される予定です。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してデプロイするには、`AllowActiveFTP` パラメーターを使用します。 詳細については、[アクティブ FTP の許可を使用してファイアウォールを作成する](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-)方法に関する記事を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してデプロイするには、`--allow-active-ftp` パラメーターを使用します。 詳細については、[Az.Network のファイアウォールの作成](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters)に関する記事を参照してください。 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレート

ARM テンプレートを使用してデプロイするには、`AdditionalProperties` フィールドを使用します。

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
詳細については、[Microsoft.Network の azureFirewalls](/azure/templates/microsoft.network/azurefirewalls) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Firewall のデプロイ方法については、「[チュートリアル: Azure PowerShell を使用して Azure Firewall のデプロイと構成を行う](deploy-ps.md)」を参照してください。