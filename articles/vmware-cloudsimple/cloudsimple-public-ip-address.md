---
title: Azure VMware Solutions (AVS) - パブリック IP アドレス
description: Azure VMware Solutions (AVS) のパブリック IP アドレスとその利点について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024978"
---
# <a name="avs-public-ip-address-overview"></a>AVS のパブリック IP アドレスの概要

インターネット リソースは、パブリック IP アドレスにより、プライベート IP アドレスの AVS プライベート クラウド リソースとインバウンド通信できます。 プライベート IP アドレスとは、お使いの AVS プライベート クラウド vCenter 上の仮想マシンまたはソフトウェア ロード バランサーです。 パブリック IP アドレスにより、お使いの AVS プライベート クラウドで実行されているサービスをインターネットに公開できます。

パブリック IP アドレスは、割り当てを解除するまで、そのプライベート IP アドレス専用になります。 1 つのパブリック IP アドレスは、1 つのプライベート IP アドレスにのみ割り当てることができます。

パブリック IP アドレスに関連付けられたリソースでは、常に、インターネット アクセスに対してパブリック IP アドレスが使われます。 既定では、パブリック IP アドレスではアウトバウンドのインターネット アクセスのみが許可されます。  パブリック IP アドレスでの着信トラフィックは拒否されます。  インバウンド トラフィックを許可するには、特定のポートに対してそのパブリック IP アドレスのファイアウォール規則を作成します。

## <a name="benefits"></a>メリット

パブリック IP アドレスを使ってインバウンドの通信を行うと、以下のものが提供されます。

* 分散型サービス拒否 (DDoS) 攻撃に対する防御。 この保護は、パブリック IP アドレスに対して自動的に有効になります。
* トラフィックの常時監視と一般的なネットワーク レベル攻撃のリアルタイム軽減。 これらの防御は、Microsoft Online Services によって使われるものと同じ防御です。
* Azure グローバル ネットワークのスケール全体。 ネットワークを使って、リージョン間で攻撃トラフィックを分散および軽減できます。  

## <a name="next-steps"></a>次のステップ

* [パブリック IP アドレスを割り当てる](public-ips.md)方法を確認する