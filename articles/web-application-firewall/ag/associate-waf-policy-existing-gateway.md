---
title: Web アプリケーション ファイアウォール ポリシーを既存の Azure Application Gateway に関連付ける
description: Web アプリケーション ファイアウォール ポリシーを既存の Azure Application Gateway に関連付ける方法について説明します。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: b455849c889c463fbda305e690f998d58fab0d8f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511528"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>WAF ポリシーを既存の Application Gateway に関連付ける

Azure PowerShell を使用して [WAF ポリシーを作成](create-waf-policy-ag.md)できますが、Application Gateway を既にお持ちで、単に WAF ポリシーをそれに関連付けたい場合があります。 この記事では、WAF ポリシーを作成してから、既に存在する Application Gateway にそれを関連付ける作業を行います。 

1. Application Gateway とファイアウォール ポリシーを取得します。 既存のファイアウォール ポリシーがない場合は、手順 2 を参照してください。 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (省略可能) ファイアウォール ポリシーを作成します。

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > WAF 構成から WAF ポリシーに移行するためにこの WAF ポリシーを作成している場合、ポリシーは古い構成の完全なコピーになっている必要があります。これはつまり、すべての除外、カスタム ルール、無効化されたルール グループなどが、WAF 構成内のものとまったく同じになっている必要があるということです。
3. (省略可能) ニーズに合わせて WAF ポリシーを構成できます。 これには、カスタム ルール、ルール/ルール グループの無効化、除外、ファイル アップロード制限の設定などが含まれます。この手順を省略すると、すべて既定値が選択されます。 
   
4. ポリシーを保存して、それを Application Gateway にアタッチします。 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>次の手順
[カスタム ルールについて学習する。](/configure-waf-custom-rules.md)
