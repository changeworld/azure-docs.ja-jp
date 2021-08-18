---
title: InvalidNetworkSecurityGroupSecurityRules エラー - Azure HDInsight
description: ErrorCode InvalidNetworkSecurityGroupSecurityRules でクラスターの作成が失敗する
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: ed3fae13651119d9f7c85ec1fac559c39f15be06
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299827"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>シナリオ: InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight でクラスターの作成が失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

エラー コード `InvalidNetworkSecurityGroupSecurityRules` を受け取り、"サブネットで構成されたネットワーク セキュリティ グループのセキュリティ規則で、必要な受信/送信接続が許可されない。" のような説明が表示されます。

## <a name="cause"></a>原因

クラスターに対して構成された受信[ネットワーク セキュリティ グループ](../../virtual-network/virtual-network-vnet-plan-design-arm.md)規則に問題がある可能性があります。

## <a name="resolution"></a>解決方法

Azure portal に移動し、クラスターがデプロイされているサブネットに関連付けられている NSG を特定します。 **受信セキュリティ規則** セクションで、[ここ](../control-network-traffic.md)に記述されている IP アドレスについてポート 443 への受信アクセスがルールによって許可されていることを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]