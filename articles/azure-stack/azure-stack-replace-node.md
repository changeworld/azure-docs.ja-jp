---
title: Azure Stack 統合システムのスケール ユニット ノードを交換する | Microsoft Docs
description: Azure Stack 統合システムの物理的なスケール ユニット ノードを交換する方法を説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 9d53aa879c39eb68597a402133a7ff16737f4f65
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716312"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Azure Stack 統合システムのスケール ユニット ノードを交換する

*適用先:Azure Stack 統合システム*

この記事では、Azure Stack 統合システムの物理コンピューター ("スケール ユニット ノード" とも呼ばれます) を交換するための一般的なプロセスについて説明します。 実際のスケール ユニット ノード交換手順は、ご利用の OEM (Original Equipment Manufacturer) ハードウェア ベンダーによって異なります。 お使いのシステムに特化した詳しい手順については、ベンダーの現場交換可能ユニット (FRU) ドキュメントをご覧ください。

次のフロー図は、スケール ユニット ノード全体を交換するための一般的な FRU プロセスを示しています。

![ノード交換プロセスのフロー チャート](media/azure-stack-replace-node/replacenodeflow.png)

* このアクションは、ハードウェアの物理的な状態によっては必須でありません。

> [!Note]  
> シャットダウン操作に失敗した場合は、ドレイン操作に続けて停止操作を実行することをお勧めします。 詳細については、利用可能なノード操作に関するページを参照してください。  

## <a name="review-alert-information"></a>アラート情報を見直す

スケール ユニット ノードがダウンしている場合は、次の重大なアラートが送信されます。

- Node not connected to network controller\(ノードがネットワーク コントローラーに接続されていません\)
- Node inaccessible for virtual machine placement\(仮想マシンを配置するためのノードにアクセスできません\)
- Scale unit node is offline\(スケール ユニットがオフラインです\)

![スケール ユニット ダウン時のアラートの一覧](media/azure-stack-replace-node/nodedownalerts.png)

**[Scale unit node is offline]\(スケール ユニットがオフラインです\)** アラートを開くと、アクセスできないスケール ユニット ノードがアラートの説明に含まれます。 ハードウェア ライフサイクル ホストで実行されている OEM 固有の監視ソリューションで、追加のアラートが送信される場合もあります。

![ノード オフライン アラートの詳細](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>スケール ユニット ノード交換プロセス

次の手順は、スケール ユニット ノード交換プロセスの概略を示しています。 お使いのシステムに特化した詳しい手順については、OEM ハードウェア ベンダーの FRU ドキュメントを参照してください。 OEM 提供のマニュアルを参照せずにこれらの手順を実行しないでください。

1. **シャットダウン** アクションを使用して、スケール ユニット ノードを正常にシャットダウンします。 このアクションは、ハードウェアの物理的な状態によっては必須でありません。 

2. まれなケースでシャットダウン アクションに失敗した場合は、[ドレイン](azure-stack-node-actions.md#drain) アクションを使用して、スケール ユニット ノードをメンテナンス モードにします。 このアクションは、ハードウェアの物理的な状態によっては必須でありません。

   > [!NOTE]  
   > いずれの場合も、S2D (記憶域スペース ダイレクト) を中断せずに一度に無効化して電源をオフにできるノードは 1 つだけです。

3. スケール ユニット ノードがメンテナンス モードに入ったら、[停止](azure-stack-node-actions.md#stop)アクションを使用します。 このアクションは、ハードウェアの物理的な状態によっては必須でありません。

   > [!NOTE]  
   > 可能性は低いですが、電源オフ アクションが機能しない場合は、代わりに BMC (Baseboard Management Controller) Web インターフェイスを使用します。

4. 物理コンピューターを交換します。 通常、これは OEM ハードウェア ベンダーによって行われます。
5. [修復](azure-stack-node-actions.md#repair)アクションを使用して、新しい物理コンピューターをスケール ユニットに追加します。
6. 特権エンドポイントを使用して、[仮想ディスクの修復の状態を確認します](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 新しいデータ ドライブを使用するストレージ修復ジョブ全体は、システムの負荷と消費される領域に応じて、数時間かかる可能性があります。
7. 修復アクションが完了したら、すべてのアクティブなアラートが自動的に閉じていることを確認します。

## <a name="next-steps"></a>次の手順

- システムの電源が入った状態での物理ディスクの交換については、[ディスクの交換](azure-stack-replace-disk.md)に関するページを参照してください。 
- システムの電源をオフにする必要があるハードウェア コンポーネントの交換については、[ハードウェア コンポーネントの交換](azure-stack-replace-component.md)に関するページを参照してください。
