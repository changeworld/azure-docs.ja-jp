---
title: "Azure Stack 統合システムのスケール ユニット ノードを交換する | Microsoft Docs"
description: "Azure Stack 統合システムの物理的なスケール ユニット ノードを交換する方法を説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: f3d6215b672be0d8de8ba0d94320b1a2413eadf1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Azure Stack 統合システムのスケール ユニット ノードを交換する

*適用対象: Azure Stack 統合システム*

この記事では、Azure Stack 統合システムの物理コンピューター ("*スケール ユニット ノード*" とも呼ばれます) を交換するための一般的なプロセスについて説明します。 実際のスケール ユニット ノード交換手順は、ご利用の OEM (Original Equipment Manufacturer) ハードウェア ベンダーによって異なります。 お使いのシステムに特化した詳しい手順については、ベンダーの現場交換可能ユニット (FRU) ドキュメントをご覧ください。

次のフロー図は、スケール ユニット ノード全体を交換するための一般的な FRU プロセスを示しています。

![ノード交換プロセスのフロー チャート](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* このアクションは、ハードウェアの物理的な状態によっては必須でありません。

## <a name="review-alert-information"></a>アラート情報を見直す

スケール ユニット ノードがダウンしている場合は、次の重大なアラートがすべて送信されます。

- Node not connected to network controller\(ノードがネットワーク コントローラーに接続されていません\)
- Node inaccessible for virtual machine placement\(仮想マシンを配置するためのノードにアクセスできません\)
- Scale unit node is offline\(スケール ユニットがオフラインです\)

![スケール ユニット ダウン時のアラートの一覧](media/azure-stack-replace-node/NodeDownAlerts.PNG)

[Scale unit node is offline]\(スケール ユニットがオフラインです\) アラートを開くと、アクセスできないスケール ユニット ノードがアラートの説明に含まれます。 ハードウェア ライフサイクル ホストで実行されている OEM 固有の監視ソリューションで、追加のアラートが送信される場合もあります。

![ノード オフライン アラートの詳細](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>スケール ユニット ノード交換プロセス

次の手順は、スケール ユニット ノード交換プロセスの概略を示しています。 お使いのシステムに特化した詳しい手順については、OEM ハードウェア ベンダーの FRU ドキュメントをご覧ください。 OEM 提供のマニュアルを参照せずにこれらの手順を実行しないでください。

1. [ドレイン](azure-stack-node-actions.md#scale-unit-node-actions) アクションを使用して、スケール ユニット ノードをメンテナンス モードにします。 このアクションは、ハードウェアの物理的な状態によっては必須でありません。
2. ノードの電源がまだオンになっている場合は、[電源オフ](azure-stack-node-actions.md#scale-unit-node-actions) アクションを使用します。 このアクションは、ハードウェアの物理的な状態によっては必須でありません。
 
   > [!NOTE]
   > 可能性は低いですが、電源オフ アクションが機能しない場合は、代わりに BMC (Baseboard Management Controller) Web インターフェイスを使用します。

1. 物理コンピューターを交換します。 通常、これは OEM ハードウェア ベンダーによって行われます。
2. [修復](azure-stack-node-actions.md#scale-unit-node-actions)アクションを使用して、新しい物理コンピューターをスケール ユニットに追加します。
3. 特権エンドポイントを使用して、[仮想ディスクの修復の状態を確認します](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 新しいデータ ドライブを使用するストレージ修復ジョブ全体は、システムの負荷と消費される領域に応じて、数時間かかる可能性があります。
4. 修復アクションが完了したら、すべてのアクティブなアラートが自動的に閉じていることを確認します。

## <a name="next-steps"></a>次のステップ

- ホットスワップ可能物理ディスクの交換については、[ディスクの交換](azure-stack-replace-disk.md)に関する記事を参照してください。 
- ホットスワップが可能でないハードウェア コンポーネントの交換の詳細については、[ハードウェア コンポーネントの交換](azure-stack-replace-component.md)に関する記事を参照してください。 