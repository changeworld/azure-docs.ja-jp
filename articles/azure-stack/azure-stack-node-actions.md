---
title: "Azure Stack でのスケール ユニットのノード操作 | Microsoft Docs"
description: "Azure Stack 統合システム上でノードの状態を表示し、電源オン、電源オフ、ドレイン、および再開のノード操作を使用する方法を説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mabrigg
ms.openlocfilehash: 4b94092f1284abfa2462ddef04b6e84136e54dde
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack でのスケール ユニット ノードの操作

*適用対象: Azure Stack 統合システム*

この記事では、スケール ユニットとそれに関連するノードの状態を表示し、使用可能なノード操作を行う方法について説明します。 ノード操作には、電源オン、電源オフ、ドレイン、再開、および修復が含まれます。 通常、これらのノード操作は、パーツのフィールド交換中や、ノード復旧において使用されます。

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>スケール ユニットとそのノードの状態の表示

管理者ポータルで、スケール ユニットとその関連するノードの状態を簡単に表示できます。

スケール ユニットの状態を表示するには、以下のようにします。

1. **[Region management]\(リージョンの管理\)** タイルで、リージョン名をクリックします。
2. 左側の **[インフラストラクチャ リソース]** で、**[スケール ユニット]**を選択します。
3. 結果画面で、スケール ユニットを選択します。
 
ここでは、次の情報を表示できます。

- リージョン名
- システムの種類
- 論理コアの合計
- メモリの合計
- 各々のノードとその状態 (実行中または停止) の一覧。

![各ノードの実行中の状態を示すスケール ユニットのタイル](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>スケール ユニット ノードに関する情報の表示

1 つのノードを選択すると、次の情報を表示できます。

- リージョン名
- サーバー モデル
- ベースボード管理コントローラー (BMC) の IP アドレス
- 操作状態
- コアの合計数
- メモリの総量
 
![各ノードの実行中の状態を示すスケール ユニットのタイル](media/azure-stack-node-actions/NodeActions.PNG)

ここから、スケール ユニットのノードの操作を実行することもできます。

## <a name="scale-unit-node-actions"></a>スケール ユニットのノード操作

スケール ユニットのノードに関する情報を表示すると、次のようなノード操作を行うこともできます。

- 電源オンと電源オフ
- ドレインと再開
- 修復

ノードの操作状態によって、使用可能なオプションが決まります。

### <a name="power-off"></a>電源オフ

**電源オフ**操作は、ノードをオフにします。 これは、電源ボタンを押した場合と同じです。 オペレーティング システムにシャット ダウンの信号を送ることは**しません**。 計画された電源オフ操作では、まず最初にスケール ユニットのノードをドレインしてください。

この操作は通常、ノードが停止状態であり要求に応答しないときに使用されます。  

PowerShell で電源オフ操作を実行するには、以下のようにします。

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

可能性は低いですが、電源オフ操作が動作しない場合には、代わりに BMC Web インターフェイスを使用します。

### <a name="power-on"></a>電源投入

**電源オン**操作は、ノードをオンにします。 これは、電源ボタンを押した場合と同じです。 

PowerShell で電源オン操作を実行するには、以下のようにします。

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

可能性は低いですが、電源オン操作が動作しない場合には、代わりに BMC Web インターフェイスを使用します。

### <a name="drain"></a>ドレイン

**ドレイン**操作では、すべてのアクティブなワークロードを、その特定のスケール ユニット内の残りのノードに分散することによって、退避させます。

この操作は通常、ノード全体の交換などの、パーツのフィールド交換中に使用されます。

> [!IMPORTANT]
> ノードのドレインは、ユーザーに通知済みの計画されたメンテナンス期間中にのみ行うように注意してください。 状況によっては、アクティブなワークロードが中断されることがあります。

PowerShell でドレイン操作を実行するには、以下のようにします。

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>再開

**再開**操作は、ドレインされたノードを再開し、ワークロード配置にアクティブとしてマーク付けします。 ノードで実行されていた以前のワークロードはフェールバックしません。 (ノードを電源オンに戻す場合、ノードをドレインしてから電源オフにすると、ワークロード配置にアクティブとしてマークされません。 準備ができたら、再開操作を使用しノードをアクティブとしてマークする必要があります。)

PowerShell で再開操作を実行するには、以下のようにします。

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>修復

**修復**アクションは、ノードを修復します。 次のシナリオのいずれかに対してのみ使用します。

- ノードの完全交換 (新しいデータ ディスクあり、またはなし)
- ハードウェア コンポーネントの障害と交換の後 (フィールド交換可能装置 (FRU) ドキュメントで推奨されている場合)。

> [!IMPORTANT]
> ノードまたは個々のハードウェア コンポーネントを置き換える必要がある場合の正確な手順については、OEM ハードウェア ベンダーの FRU ドキュメントを参照してください。 FRU ドキュメントでは、ハードウェア コンポーネントを交換した後、修復操作を実行する必要があるかどうかを指定します。  

修復操作を実行する場合、BMC の IP アドレスを指定する必要があります。 

PowerShell で修復操作を実行するには、以下のようにします。

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


