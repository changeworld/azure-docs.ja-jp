---
title: "Azure で Linux VM の計画的メンテナンスのスケジュールを設定する方法 | Microsoft Docs"
description: "Azure の計画的なメンテナンスの概要と、それが Azure で実行されている Windows 仮想マシンに及ぼす影響について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: f5e52f4642b85a75f0c9f1892fbf0e565aa8b35b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Linux 仮想マシンの計画的メンテナンス 

Microsoft Azure は、世界各地で定期的に更新を行い、仮想マシンの基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 そうした更新は、ホスティング環境のソフトウェア コンポーネント (ホストに展開されている OS、ハイパーバイザー、各種エージェント) の修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止にいたるまで、さまざまな範囲に及んでいます。

これらの更新のほとんどは、ホストされている仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます。

ただし、更新がホストされた仮想マシンに影響を与える場合があります。

-   インプレース VM 移行を使用する VM 保持メンテナンスでは、メンテナンス中に仮想マシンを再起動しない更新クラスについて説明しています。

-   VM 再起動メンテナンスでは、ホストされた仮想マシンの再起動または再配置が必要です。

このページでは、Microsoft Azure による計画的なメンテナンスの実行について説明しています。 計画外のイベント (停止) の詳細については、「[Azure での Windows 仮想マシンの可用性の管理](../linux/manage-availability.md)」を参照してください。

