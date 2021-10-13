---
title: Azure VMware Solution の DHCP と DNS に関する説明
description: Azure VMware Solution の DHCP と DNS に関する説明。
ms.topic: include
ms.date: 05/28/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0e92259c2ece3b92b841d2be192684679d37a635
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638559"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供するために、DHCP と DNS の適切なインフラストラクチャが必要です。 仮想マシンを構成して、ご自分のプライベート クラウド環境でこれらのサービスを提供することができます。  

ブロードキャスト DHCP トラフィックを WAN 経由でオンプレミスにルーティングして戻すのではなく、NSX に組み込まれている DHCP サービスを使用するか、プライベート クラウドのローカル DHCP サーバーを使用します。
