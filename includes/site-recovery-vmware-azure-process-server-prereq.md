---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566304"
---
この記事では、以下のことを前提としています。

1. オンプレミスのネットワークと Azure Virtual Network の間に**サイト間 VPN** または **ExpressRoute** 接続が既に確立されている。
2. 仮想マシンがフェールオーバーした Azure サブスクリプションに新しい仮想マシンを作成するアクセス許可をユーザー アカウントが持っている。
3. サブスクリプションでは、新しいプロセス サーバー仮想マシンの起動に少なくとも 8 つのコアを使用できる。
4. 使用可能な**構成サーバーのパスフレーズ**を持っている。

> [!TIP]
> 仮想マシンがフェールオーバーした Azure Virtual Network から (オンプレミスで実行されている) 構成サーバーのポート 443 に接続できることを確認します。
