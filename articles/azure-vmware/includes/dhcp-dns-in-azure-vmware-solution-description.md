---
title: Azure VMware Solution の DHCP と DNS に関する説明
description: Azure VMware Solution の DHCP と DNS に関する説明。
ms.topic: include
ms.date: 05/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 98f2fd71cc4657a3837278c3b7a761678b8a1005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750094"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供するために、DHCP と DNS の適切なインフラストラクチャが必要です。 仮想マシンを構成して、ご自分のプライベート クラウド環境でこれらのサービスを提供することができます。  

ブロードキャスト DHCP トラフィックを WAN 経由でオンプレミスにルーティングして戻すのではなく、NSX に組み込まれている DHCP サービスを使用するか、プライベート クラウドのローカル DHCP サーバーを使用します。
