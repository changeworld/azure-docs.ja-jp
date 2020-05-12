---
title: Windows Virtual Desktop のホスト プールの負荷分散 - Azure
description: Windows Virtual Desktop 環境でのホスト プールの負荷分散方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15d50033a1316601dd8c36bd5748c659f4397d66
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612046"
---
# <a name="host-pool-load-balancing-methods"></a>ホスト プールの負荷分散方法

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトと Spring 2020 更新プログラムの組み合わせに適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Virtual Desktop では 2 つの負荷分散方法がサポートされます。 それぞれの方法では、ユーザーがホスト プール内のリソースに接続するときにそのユーザーのセッションをホストするセッション ホストが決定されます。

Windows Virtual Desktop では以下の負荷分散方法を使用できます。

- 幅優先の負荷分散では、ホスト プール内のセッション ホスト間でユーザー セッションを均等に分散させることができます。
- 深さ優先の負荷分散では、セッション ホストをホスト プール内のユーザー セッションで飽和状態にすることができます。 最初のセッションがそのセッション制限のしきい値に達すると、新しいユーザー接続はロード バランサーによってホスト プール内の次のセッション ホストに制限に達するまで送られ、以下同様に送られます。

各ホスト プールで構成できるのは、そのホスト プールに固有の 1 種類の負荷分散だけです。 ただし、属しているホスト プールに関係なく、両方の負荷分散方法が以下の動作を共有します。

- ユーザーが既にホスト プール内にセッションを持っていて、そのセッションに再接続する場合、ロード バランサーは、既存のセッションがあるセッション ホストにそれらを正常にリダイレクトします。 この動作は、そのセッション ホストの AllowNewConnections プロパティが False に設定されている場合でも適用されます。
- ユーザーがホスト プール内にセッションをまだ持っていない場合、ロード バランサーでは、AllowNewConnections プロパティが False に設定されているセッション ホストは負荷分散時に考慮されません。

## <a name="breadth-first-load-balancing-method"></a>幅優先の負荷分散方法

幅優先の負荷分散方法では、このシナリオに対して最適化するためにユーザー接続を分散できます。 この方法は、プールされた仮想デスクトップ環境に接続しているユーザーに最適なエクスペリエンスを提供することを望む組織に最適です。

幅優先の方法では、まず、新しい接続を許可するセッション ホストのクエリが実行されます。 次に、セッションの数が最も少ないセッション ホストが選択されます。 同数のものがある場合は、クエリ内の最初のセッション ホストが選択されます。

## <a name="depth-first-load-balancing-method"></a>深さ優先の負荷分散方法

深さ優先の負荷分散方法では、このシナリオに対して最適化するために、一度に 1 つのセッション ホストを飽和状態にすることができます。 この方法は、ホスト プールに割り当てられている仮想マシンの数をより細かく制御することを望むコスト意識の高い組織に最適です。

深さ優先の方法では、まず、新しい接続を許可し、セッションの上限を超えていないセッション ホストのクエリが実行されます。 次に、セッションの数が最も多いセッション ホストが選択されます。 同数のものがある場合は、クエリ内の最初のセッション ホストが選択されます。