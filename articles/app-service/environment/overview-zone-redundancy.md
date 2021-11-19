---
title: App Service Environment でのゾーンの冗長性
description: App Service Environment でのゾーンの冗長性の概要
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 3ce5a0925ae4c1dc1bc33fec73987455d612d661
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525569"
---
# <a name="availability-zone-support-for-app-service-environments"></a>App Service 環境での可用性ゾーンのサポート

> [!NOTE]
> これは、Isolated v2 App Service プランで使用される App Service Environment v3 に関する記事です
>

App Service Environment (ASE) は、[可用性ゾーン (AZ)](../../availability-zones/az-overview.md) 全体にデプロイできます。 このアーキテクチャは、ゾーン冗長とも呼ばれます。 ASE がゾーン冗長として構成されていると、プラットフォームによって、選択されたリージョン内の 3 つのゾーンすべてに対して ASE 内で App Service プラン インスタンスが自動的に分散されます。 3 を超える容量が指定されていて、インスタンスの数が 3 で割り切れる場合は、インスタンスが均等に分散されます。 それ以外の場合は、3*N を超えるインスタンス数が残りの 1 つまたは 2 つのゾーンに対して分散されます。

ASE を作成するときにゾーン冗長性を構成すると、その ASE に作成されたすべての App Service プランがゾーン冗長になります。 ゾーン冗長を指定できるのは、*新しい* App Service 環境を作成するときのみです。 ゾーン冗長は、[一部のリージョン](./overview.md#regions)でのみサポートされています。

ゾーンがダウンした場合は、App Service プラットフォームによって、失われたインスタンスが検出され、新しい置換インスタンスの検索が自動的に試行されます。 自動スケーリングも構成している場合は、より多くのインスタンスが必要と判断されると、自動スケーリングによって App Service への要求が発行され、インスタンスが追加されます (自動スケーリング動作は App Service のプラットフォーム動作に依存していません)。 失われたインスタンスの補填はベストエフォートで実行されるため、ゾーンがダウンするシナリオにおけるインスタンスの要求は、成功するとは限らないことに注意する必要があります。 推奨される解決策は、ゾーンが失われることを考慮して App Service プランを拡張することです。

ゾーン冗長 ASE にデプロイされたアプリケーションは、同じリージョン内の他のゾーンで障害が発生した場合でも、引き続き実行され、トラフィックが処理されます。 ただし、非実行時の動作 (App Service プランのスケーリング、アプリケーションの作成、アプリケーションの構成、およびアプリケーションの発行を含む) では、他の可用性ゾーンの障害から影響を受ける可能性があります。 App Service 環境のゾーン冗長で確保されるのは、デプロイされたアプリケーションの継続的なアップタイムのみです。

App Service プラットフォームによってインスタンスが ASE でゾーン冗長 App Service プランに割り当てられると、[基になる Azure Virtual Machine Scale Sets によって提供されるベスト エフォートのゾーン負荷分散](../../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)が使用されます。 App Service プランの "バランスが取れる" のは、各ゾーンに、その App Service プランで使用される他のすべてのゾーンと同じ数のインスタンスまたは +/- 1 個のインスタンスがある場合です。

## <a name="pricing"></a>価格

 ゾーン冗長 ASE には、9 つの App Service プラン インスタンスという最小料金があります。 App Service プランのインスタンスが 9 件以上ある場合でも、可用性ゾーンのサポートのための追加料金は発生しません。 ゾーン冗長 ASE の App Service プラン全体で使用しているインスタンス (任意のサイズ) が 9 つ未満の場合、9 と実行中のインスタンス数の差が、追加の Windows I1v2 インスタンスとして課金されます。

## <a name="next-steps"></a>次の手順

* [可用性ゾーン](../../availability-zones/az-overview.md)について理解を深める。
