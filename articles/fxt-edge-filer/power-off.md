---
title: Microsoft Azure FXT Edge Filer ユニットをシャットダウンする方法
description: クラスター コントロール パネル ソフトウェアを使用し、Azure FXT Edge Filer を起動し、安全にシャットダウンする手順について説明します。
author: femila
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: femila
ms.openlocfilehash: c3b9f9c317de7ff1bdf2801d1348769882cb777b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024241"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge Filer ハードウェアの電源を安全に切る方法

個々のノードの電源をオンにするために物理的な電源ボタンを使用することはできますが、通常の状況では、電源ボタンを使用してユニットをシャットダウンしてはいけません。

Azure FXT Edge Filer ノードがクラスターの一部として使用された後は、クラスターのコントロール パネル ソフトウェアを使用して、ハードウェアをシャットダウンする必要があります。

> [!NOTE]
> データの損失や破損を避けるには、必ずコントロール パネル ソフトウェアを使用して、Azure FXT Edge Filer をシャットダウンしてください。 Microsoft カスタマー サービスおよびサポートから指示されない限り、物理的な電源ボタンを使用してシャットダウンしないでください。
>
> 電気的非常時には、電源コードを外すか、データ センターの電気切断メカニズムを使用します。

## <a name="shut-down-a-node-from-the-control-panel"></a>コントロール パネルからノードをシャットダウンする

Azure FXT Edge Filer ノードの電源を安全に切るには、次の手順に従います。

1. クラスターのコントロール パネルにサインインします。 (「[[設定] ページを開く](cluster-create.md#open-the-settings-pages)」の手順)
1. **[設定]** タブをクリックし、 **[クラスター]**  >  **[FXT Nodes]\(FXT ノード\)** ページを読み込みます。
1. クラスター ノードの一覧で、シャットダウンするノードを見つけます。 **[アクション]** 列の **[Power down]\(電源切断\)** ボタンをクリックします。
1. 数分待ちます。 ノードがシャットダウンし、電源が切れます。

## <a name="next-steps"></a>次のステップ

* LED およびその他のインジケーターについては、「[Monitor Azure FXT Edge Filer hardware status (Azure FXT Edge Filer ハードウェアの状態の監視)](monitor.md)」を参照してください。
* Azure FXT Edge Filer の電源の詳細については、「[電源ケーブルを接続する](network-power.md#connect-power-cables)」を参照してください。
