---
title: Azure からオンプレミスへのフェールバックとその後の Azure への再保護中のエラーのトラブルシューティング | Microsoft Docs
description: この記事では、Azure からオンプレミスへのフェールバック中と再保護中の一般的なエラーのトラブルシューティング方法について説明します。
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677684"
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>フェールバックの処理中に報告されたエラーのトラブルシューティング
基本的に、フェールバックには主な手順が 2 つあります。 1 つは Azure からオンプレミスへの仮想マシンを再保護すること、もう 1 つは実際に Azure からオンプレミスへのフェールバックを実行することです。

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>フェールオーバー後に仮想マシンをオンプレミスに再保護する場合のエラーのトラブルシューティング
Azure に対する仮想マシンの再保護を実行中に、次のエラーのいずれかが発生する場合があります。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。


### <a name="error-code-95226"></a>エラー コード 95226

*Azure 仮想マシンがオンプレミスの構成サーバーにアクセスできなかったため、再保護に失敗しました。*

このエラーは次の場合に発生します。 
1. Azure 仮想マシンがオンプレミスの構成サーバーに到達できず、その結果、構成サーバーから検出されず、登録されなかった可能性があります。 
2. オンプレミスの構成サーバーと通信するために実行する必要がある Azure 仮想マシン上の InMage Scout Application サービスが、フェールオーバー後に実行していない可能性があります。

この問題を解決するには
1. Azure 仮想マシンがオンプレミスの構成サーバーと通信できるように Azure 仮想マシンのネットワークが構成されていることを確認する必要があります。 確認するには、オンプレミスのデータセンターに対してサイト間 VPN を設定するか、Azure 仮想マシンの仮想ネットワーク上でプライベート ピアリングを使用して ExpressRoute 接続を構成します。 
2. Azure 仮想マシンがオンプレミスの構成サーバーと通信できるように既にネットワークを構成している場合は、仮想マシンにログインし、'InMage Scout Application Service' を確認します。 InMage Scout Application Service が実行中ではない場合は、サービスを手動で開始し、サービス開始の種類が [自動] に設定されていることを確認します。

### <a name="error-code-78052"></a>エラー コード 78052
再保護が次のエラー メッセージで失敗します: *仮想マシンの保護を完了できませんでした。*

このエラーは、2 つの理由で発生する可能性があります。
1. 再保護する仮想マシンが Windows Server 2016 です。 現在、このオペレーティング システムではフェールバックがサポートされていませんが、間もなくサポートされる予定です。
2. フェールバック先のマスター ターゲット サーバーに同じ名前の仮想マシンが既に存在します。

この問題を解決するには、別のホスト上にある別のマスター ターゲット サーバーを選択して、再保護によって別のホストに仮想マシンを作成し、名前が競合しないようにします。 また、vMotion を使用して、名前の競合が発生しない別のホストにマスター ターゲットを移動することもできます。 既存の仮想マシンがはぐれたコンピューターである場合は、同じ ESXi ホストで新しい仮想マシンを作成できるように、はぐれたコンピューターの名前を変更することができます。

### <a name="error-code-78093"></a>エラー コード 78093

*VM が実行中でないか、応答を停止しているか、アクセスできない状態です。*

フェールオーバーした仮想マシンをオンプレミスで再保護するには、Azure 仮想マシンが実行されている必要があります。 これは、モビリティ サービスがオンプレミスの構成サーバーに登録され、プロセス サーバーと通信することでレプリケートを開始できるようにするためです。 マシンが不適切なネットワーク上にある場合、または実行されていない場合 (停止状態またはシャットダウン)、構成サーバーは、再保護を開始する仮想マシンのモビリティ サービスに到達できません。 仮想マシンを再起動して、オンプレミスで通信を開始できるようにします。 Azure 仮想マシンを起動した後に、再保護ジョブを再起動します。

### <a name="error-code-8061"></a>エラー コード 8061

"*データ ストアには、ESXi ホストからアクセスできません。*"

[マスター ターゲットの前提条件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)とフェールバック用に[サポートされているデータストア](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)を参照してください。


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>オンプレミスへの Azure 仮想マシンのフェールバックを実行する場合のエラーのトラブルシューティング
オンプレミスへの Azure 仮想マシンのフェールバックを実行している間に、次のエラーのいずれかが発生する場合があります。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。

### <a name="error-code-8038"></a>エラー コード 8038

*エラーが原因で、オンプレミスの仮想マシンを呼び出せませんでした*

これが発生するのは、十分なメモリがプロビジョニングされていないホストでオンプレミスの仮想マシンが呼び出された場合です。

この問題を解決するには

1. ESXi ホストにさらに多くのメモリをプロビジョニングできます。
1. vMotion を使用して、仮想マシンを起動するために充分なメモリを搭載した別の ESXi ホストに VM を移動します。