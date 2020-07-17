---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181010"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>ローカル ネットワーク ゲートウェイの IP アドレスを変更するには (ゲートウェイ接続がない場合)

ゲートウェイに接続していないローカル ネットワーク ゲートウェイに変更を加える場合には、以下の例を使用してください。 この値を変更するときには、同時にアドレス プレフィックスを変更することもできます。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** をクリックします。
2. **[IP アドレス]** ボックスの IP アドレスを変更します。
3. **[保存]** をクリックして設定を保存します。

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>ローカル ネットワーク ゲートウェイの IP アドレスを変更するには (既存のゲートウェイ接続がある場合)

接続されているローカル ネットワーク ゲートウェイを変更するには、まず接続を削除する必要があります。 接続を削除したら、ゲートウェイ IP アドレスを変更し、新しい接続を作成し直します。 同時にアドレス プレフィックスを変更することもできます。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 ゲートウェイの IP アドレスを変更するときに、VPN ゲートウェイを削除する必要はありません。 削除が必要になるのは、接続のみです。
 
#### <a name="1-remove-the-connection"></a>1.接続を削除します。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[接続]** をクリックします。
2. 接続の行の **[...]** をクリックし、 **[削除]** をクリックします。
3. **[Save]** をクリックして設定を保存します。

#### <a name="2-modify-the-ip-address"></a>2.IP アドレスを変更します。

同時にアドレス プレフィックスを変更することもできます。

1. **[IP アドレス]** ボックスの IP アドレスを変更します。
2. **[保存]** をクリックして設定を保存します。

#### <a name="3-recreate-the-connection"></a>3.接続を再作成します。

1. VNet の仮想ネットワーク ゲートウェイに移動します (ローカル ネットワーク ゲートウェイではありません)。
2. 仮想ネットワーク ゲートウェイの **[設定]** セクションで、 **[接続]** をクリックします。
3. **[+追加]** をクリックして **[接続の追加]** ブレードを開きます。
4. 接続を再作成します。
5. **[OK]** をクリックして、接続を作成します。