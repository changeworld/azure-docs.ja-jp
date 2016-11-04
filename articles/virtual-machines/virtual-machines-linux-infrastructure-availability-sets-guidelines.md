---
title: 可用性セットのガイドライン | Microsoft Docs
description: Azure インフラストラクチャ サービスでの可用性セットのデプロイに関する主要な設計と実装のガイドラインについて説明します。
documentationcenter: ''
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou

---
# 可用性セットのガイドライン
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

この記事では、計画内または計画外のイベント中にユーザーがアプリケーションを利用できるようにするために必要な可用性セットの計画手順について中心に説明します。

## 可用性セットに関する実装ガイドライン
決めること:

* アプリケーション インフラストラクチャ内のさまざまなロールおよび階層に必要な可用性セットの数

タスク:

* 各アプリケーション層で必要な VM の数を定義する。
* アプリケーションに使用する障害ドメインまたは更新ドメインの数を調整する必要があるかどうかを決定する。
* 名前付け規則を使用して必要な可用性セットを定義し、可用性セット内にどの VM を配置するかを定義する。VM は 1 つの可用性セットにのみ配置できます。

## 可用性セット
Azure では、可用性セットと呼ばれる論理的なグループに仮想マシン (VM) を配置できます。可用性セット内で VM を作成する場合、Azure Platform により、基になるインフラストラクチャ全体にこれらの VM の配置が分散されます。Azure Platform または基になるハードウェア/インフラストラクチャの障害に対する計画済みメンテナンス イベントが発生する場合、可用性セットを使用することで、少なくとも 1 つの VM の実行を継続できます。

ベスト プラクティスとして、複数のアプリケーションを 1 つの VM に配置しないでください。可用性セットに 1 つの VM しか含まれていない場合、Azure Platform 内の計画済みまたは計画外のイベントから保護されません。[Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) では、基になるインフラストラクチャ全体に VM を分散できるように、1 つの可用性セット内に 2 つ以上の VM が必要です。

Azure における基になるインフラストラクチャは、更新ドメインと障害ドメインに分けられます。これらのドメインは、共通の一般的な更新サイクルを共有するホスト、または類似の物理インフラストラクチャ (電源やネットワークなど) を共有するホストで定義されます。Azure は、ドメイン間で可用性セット内に VM を自動的に分散し、可用性とフォールト トレランスを維持します。可用性セット内のアプリケーションのサイズと VM の数に応じて、使用するドメインの数を調整できます。詳細については、[更新ドメインと障害ドメインの可用性および使用の管理](virtual-machines-linux-manage-availability.md)に関する記事を参照してください。

アプリケーション インフラストラクチャを設計する場合は、使用するアプリケーション層について計画する必要もあります。目的が同じ VM を、可用性セットごとにグループ化します (nginx または Apache が実行されているフロントエンド VM の可用性セットなど)。MongoDB または MySQL が実行されているバックエンド VM 用に、別の可用性セットを作成します。目標は、アプリケーションの各コンポーネントを可用性セットで保護することで、少なくとも 1 つのインスタンスが常に実行されているようにすることです。

ロード バランサーを可用性セットと併せて各アプリケーション層の前で使用することで、実行中のインスタンスにトラフィックが常にルーティングされるようにすることができます。ロード バランサーを使用しなくても、計画内および計画外のメンテナンス イベント時に VM を実行し続けることはできますが、エンド ユーザーは、プライマリ VM が利用できない場合にそれらのイベントを解決できない可能性があります。

## 次のステップ
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->