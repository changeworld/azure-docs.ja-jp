---
title: "Azure Site Recovery を使用して Azure VM をオンプレミス VMware にフェールバックしているときのエラーのトラブルシューティング | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して Azure から VMware にフェールバックするときの一般的なフェールバック エラーと再保護エラーをトラブルシューティングする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2017
ms.author: rajanaki
ms.openlocfilehash: 9b1156884a78eb7d68dc9680765b3c1436c0606a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Azure から VMware へのフェールバックのトラブルシューティング

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure にフェールオーバーした後で、Azure VM をオンプレミスの VMware インフラストラクチャにフェールバックする際に発生する可能性がある問題のトラブルシューティング方法について説明します。

基本的に、フェールバックには主な手順が 2 つあります。 フェールオーバー後、Azure VM がレプリケートを開始するように、それらをオンプレミスで再保護する必要があります。 2 番目の手順は、Azure からフェールオーバーを実行して、オンプレミス サイトにフェールバックすることです。 

## <a name="troubleshoot-reprotection-errors"></a>再保護エラーのトラブルシューティング

このセクションでは、一般的な再保護エラーとその修正方法について説明します。

### <a name="error-code-95226"></a>エラー コード 95226

**Azure 仮想マシンがオンプレミスの構成サーバーにアクセスできなかったため、再保護に失敗しました。**

このエラーは、次の場合に発生します。

1. Azure VM がオンプレミスの構成サーバーに到達できません。 VM は検出されず、構成サーバーに登録されません。 
2. InMage Scout Application サービスが、フェールオーバー後に Azure VM で実行されていません。 このサービスは、オンプレミスの構成サーバーとの通信に必要です。

この問題を解決するには:

1. Azure VM ネットワークで、Azure VM がオンプレミスの構成サーバーと通信できるようになっていることを確認します。 確認するには、オンプレミスのデータセンターへのサイト間 VPN をセットアップするか、Azure VM の仮想ネットワーク上でプライベート ピアリングを使用して ExpressRoute 接続を構成します。 
2. VM がオンプレミスの構成サーバーと通信できる場合は、VM にログオンし、"InMage Scout Application Service" を確認します。 これが実行されていない場合は、サービスを手動で開始し、サービス開始の種類が [自動] に設定されていることを確認します。

### <a name="error-code-78052"></a>エラー コード 78052

***仮想マシンの保護を完了できませんでした。**

このエラーは、フェールバック先のマスター ターゲット サーバー上に同じ名前の VM が既に存在する場合に発生します。

この問題を解決するには、次の操作を行ってください。
1. 別のホスト上にある別のマスター ターゲット サーバーを選択して、再保護によって別のホストにマシンが作成され、名前が競合しないようにします。 
2. また、vMotion を使用して、名前の競合が発生しない別のホストにマスター ターゲットを移動することもできます。 既存の VM がはぐれたマシンである場合は、同じ ESXi ホストで新しい VM を作成できるように、はぐれたマシンの名前を変更します。

### <a name="error-code-78093"></a>エラー コード 78093

**VM が実行中でないか、応答を停止しているか、アクセスできない状態です。**

フェールオーバーされた VM を再保護するには、Azure VM が実行されている必要があります。 これは、モビリティ サービスがオンプレミスの構成サーバーに登録され、プロセス サーバーと通信することでレプリケートを開始できるようにするためです。 マシンが不適切なネットワーク上にある場合、または実行されていない場合 (停止状態またはシャットダウン)、構成サーバーは VM のモビリティ サービスに到達できず、再保護を開始することができません。 

1. VM を再起動して、オンプレミスで通信を開始できるようにします。
2. Azure 仮想マシンを起動した後に、再保護ジョブを再起動します。

### <a name="error-code-8061"></a>エラー コード 8061

"**データ ストアには、ESXi ホストからアクセスできません。**"

[マスター ターゲットの前提条件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)とフェールバック用に[サポートされているデータストア](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)を確認してください。


## <a name="troubleshoot-failback-errors"></a>フェールバック エラーのトラブルシューティング

このセクションでは、フェールバック中に発生することがある一般的なエラーについて説明します。

### <a name="error-code-8038"></a>エラー コード 8038

**エラーが原因で、オンプレミスの仮想マシンを呼び出せませんでした**

これが発生するのは、十分なメモリがプロビジョニングされていないホストでオンプレミスの VM が呼び出された場合です。 この問題を解決するには:

1. ESXi ホストに、より多くのメモリをプロビジョニングします。
2. また、vMotion を使用して、VM を起動するための充分なメモリがある別の ESXi ホストに VM を移動します。
