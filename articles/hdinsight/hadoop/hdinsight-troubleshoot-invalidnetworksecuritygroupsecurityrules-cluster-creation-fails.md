---
title: InvalidNetworkSecurityGroupSecurityRules エラー - Azure HDInsight
description: ErrorCode InvalidNetworkSecurityGroupSecurityRules でクラスターの作成が失敗する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289104"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>シナリオ:InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight でクラスターの作成が失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

エラー コード `InvalidNetworkSecurityGroupSecurityRules` を受け取り、"サブネットで構成されたネットワーク セキュリティ グループのセキュリティ規則で、必要な受信/送信接続が許可されない。" のような説明が表示されます。

## <a name="cause"></a>原因

クラスターに対して構成された受信[ネットワーク セキュリティ グループ](../../virtual-network/virtual-network-vnet-plan-design-arm.md)規則に問題がある可能性があります。

## <a name="resolution"></a>解像度

Azure portal に移動し、クラスターがデプロイされているサブネットに関連付けられている NSG を特定します。 **受信セキュリティ規則** セクションで、 [ここ](../control-network-traffic.md)に記述されている IP アドレスについてポート 443 への受信アクセスがルールによって許可されていることを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]