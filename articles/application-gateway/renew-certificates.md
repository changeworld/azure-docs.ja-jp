---
title: Azure Application Gateway の証明書を更新する
description: アプリケーション ゲートウェイ リスナーに関連付けられている証明書を更新する方法を説明します。
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598268"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway の証明書を更新する

SSL 暗号化対応にアプリケーション ゲートウェイを構成した場合、いつかは、証明書の更新が必要になります。

Azure portal、Azure PowerShell、または Azure CLI を使って、リスナーに関連付けられている証明書を更新することができます。

## <a name="azure-portal"></a>Azure ポータル

ポータルからリスナー証明書を更新するには、アプリケーション ゲートウェイ リスナーに移動します。 更新が必要な証明書のあるリスナーをクリックして、**[選択した証明書の更新または編集]** をクリックします。

![証明書の更新](media/renew-certificate/ssl-cert.png)

新しい PFX 証明書をアップロードし、名前を指定し、パスワードを入力してから、**[保存]** をクリックします。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使って証明書を更新するには、次のコマンドレットを使います。

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>次の手順

Azure Application Gateway で SSL オフロードを構成する方法を学習するには、[SSL オフロードの構成](application-gateway-ssl-portal.md)に関するページをご覧ください。
