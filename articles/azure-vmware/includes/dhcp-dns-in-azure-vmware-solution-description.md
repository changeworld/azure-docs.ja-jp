---
title: Azure VMware Solution の DHCP と DNS に関する説明
description: Azure VMware Solution の DHCP と DNS に関する説明。
ms.topic: include
ms.date: 05/28/2021
ms.openlocfilehash: 585f195c36dd02e28cb744142080bee7dab91f6a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110788096"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供するために、DHCP と DNS の適切なインフラストラクチャが必要です。 仮想マシンを構成して、ご自分のプライベート クラウド環境でこれらのサービスを提供することができます。  

ブロードキャスト DHCP トラフィックを WAN 経由でオンプレミスにルーティングして戻すのではなく、NSX に組み込まれている DHCP サービスを使用するか、プライベート クラウドのローカル DHCP サーバーを使用します。
