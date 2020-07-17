---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275170"
---
0\.0.0.0/0 が宛先のユーザー定義のルートと GatewaySubnet の NSG は**サポートされていません**。 この構成で作成されたゲートウェイの作成はブロックされます。 ゲートウェイが正常に機能するためには、管理コントローラーへのアクセスが必要です。 ゲートウェイの可用性を確保するために、GatewaySubnet で [[BGP ルート伝達]](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) を [有効] に設定する必要があります。 これが無効に設定されていると、ゲートウェイは機能しません。
