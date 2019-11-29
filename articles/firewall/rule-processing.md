---
title: Azure Firewall ルール処理ロジック
description: Azure Firewall には、NAT ルール、ネットワーク ルール、およびアプリケーション ルールがあります。 これらのルールは、ルールの種類に応じて処理されます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166779"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Firewall ルール処理ロジック
Azure Firewall には、NAT ルール、ネットワーク ルール、およびアプリケーション ルールがあります。 これらのルールは、ルールの種類に応じて処理されます。


## <a name="network-rules-and-applications-rules"></a>ネットワーク ルールとアプリケーション ルール 
ネットワーク ルールが先に適用され、次にアプリケーション ルールが適用されます。 その後、これらのルールが終了します。 そのため、ネットワーク ルールで一致が見つかると、アプリケーション ルールは処理されません。  一致するネットワーク ルールが存在せず、パケットのプロトコルが HTTP/HTTPS の場合、パケットはアプリケーション ルールによって評価されます。 一致が見つからない場合、パケットはインフラストラクチャ ルール コレクションに対して評価されます。 それでも一致するものがない場合、パケットは既定では拒否されます。

## <a name="nat-rules"></a>NAT 規則
受信接続を有効にするには、宛先ネットワーク アドレス変換 (DNAT) を「[チュートリアル:Azure portal で Azure Firewall DNAT を使用して受信トラフィックをフィルター処理する](tutorial-firewall-dnat.md)」の説明に従って構成します。 DNAT ルールが最初に適用されます。 一致が見つかると、変換されたトラフィックを許可する暗黙的な対応するネットワーク ルールが追加されます。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 これらの接続には、アプリケーション ルールは適用されません。


## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。