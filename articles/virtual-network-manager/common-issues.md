---
title: Azure Virtual Network Manager (プレビュー) で発生する一般的な問題
description: Azure Virtual Network Manager の使用時に発生する一般的な問題について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bcf66883ba766189215fd4ce267391c9358b10f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089813"
---
# <a name="common-issues-seen-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager (プレビュー) で発生する一般的な問題

この記事では、Azure Virtual Network Manager の使用時に直面する可能性のある一般的な問題について取り上げ、考えられる解決策を示します。

## <a name="why-isnt-my-configuration-getting-applied"></a>構成が適用されていない理由 

**構成が適用されていない一般的な理由は、次のとおりです。** 

* 仮想ネットワークが配置されているリージョンに、構成がデプロイされていません。 

* 構成をまだデプロイしていません。 構成を有効にするには、構成をデプロイする必要があります。 

* 構成が有効になるまでに十分な時間がありませんでした。 構成をコミットしてから、構成が適用されるまでにかかる時間は、約 15 分から 20 分です。 ネットワーク グループ メンバーシップに更新プログラムがある場合は、変更が反映されるまでに約 10 分かかります。 

## <a name="i-updated-my-configuration-but-the-changes-arent-reflected-in-my-environment"></a>構成を更新しましたが、環境に変更が反映されていません。 

構成を変更した後は、新しい構成をデプロイする必要があります。 

## <a name="why-is-my-connectivity-configuration-not-working"></a>接続構成が機能しない理由 

**次の項目を考慮する必要があります。** 

* ハブ アンド スポーク トポロジで、"*ハブをゲートウェイとして使用する*" オプションを有効にする場合は、ハブ仮想ネットワークにゲートウェイが必要です。 それ以外の場合、ハブとスポーク仮想ネットワークとの間での仮想ネットワーク ピアリングの作成は失敗します。 

* ハブ アンド スポーク トポロジ構成のリージョン間での相互通信を目的として、ネットワーク グループ内にメンバーを必要とする場合は、グローバル メッシュ オプションを有効にする必要があります。 

## <a name="next-steps"></a>次のステップ

よく寄せられる質問への回答については、[Azure Virtual Network Manager の FAQ](faq.md) に関する記事を参照してください。
