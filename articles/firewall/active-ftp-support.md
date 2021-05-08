---
title: Azure Firewall のアクティブ FTP のサポート
description: 既定では、アクティブ FTP は Azure Firewall で無効になっています。 PowerShell、CLI、および ARM テンプレートを使用して有効にできます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: e08be08f2d898b017bb34ed38c9c3a69ee0582fa
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312979"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall のアクティブ FTP のサポート

アクティブ FTP では、FTP サーバーは、指定された FTP クライアント データ ポートへのデータ接続を開始します。 クライアント側ネットワーク上のファイアウォールは、通常、内部クライアント ポートに対する外部接続要求をブロックします。 詳細については、[アクティブ FTP とパッシブ FTP の明確な説明](https://slacksite.com/other/ftp.html)に関する記事を参照してください。

既定では、Azure Firewall のアクティブ FTP サポートは、FTP `PORT` コマンドを使用した FTP バウンス攻撃から保護するために無効になっています。 ただし、Azure PowerShell、Azure CLI、または Azure ARM テンプレートを使用してデプロイする場合は、アクティブ FTP を有効にすることができます。

アクティブ モードの FTP をサポートするには、次の TCP ポートを開く必要があります。

- 任意の場所から FTP サーバーのポート 21 (クライアントが接続を開始)
- FTP サーバーのポート 21 からポート > 1023 (サーバーはクライアントの制御ポートに応答)
- FTP サーバーのポート 20 からクライアントのポート > 1023 (サーバーはクライアントのデータ ポートへのデータ接続を開始)
- クライアントのポート > 1023 から FTP サーバーのポート 20 (クライアントはサーバーのデータ ポートに ACK を送信)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してデプロイするには、`AllowActiveFTP` パラメーターを使用します。 詳細については、[アクティブ FTP の許可を使用してファイアウォールを作成する](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)方法に関する記事を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してデプロイするには、`--allow-active-ftp` パラメーターを使用します。 詳細については、[Az.Network のファイアウォールの作成](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)に関する記事を参照してください。 

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