---
title: "Azure からオンプレミスへフェールバック中にエラーのトラブルシューティングし、その後、Azure に再度保護 |Microsoft ドキュメント"
description: "この記事に障害が発生した内部設置型に戻ると再保護中に Azure からの一般的なエラーをトラブルシューティングする方法を説明します"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>フェールバックの処理中に報告されたエラーをトラブルシューティングします。
フェールバックは、基本的に 2 つの主な手順です。 1 つは Azure からオンプレミスへの仮想マシンを再度保護する、次に実際には Azure から内部設置型へ、フェールバックの実行です。

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>フェールオーバー後に、オンプレミスで仮想マシンの再保護するエラーをトラブルシューティングします。
Azure への仮想マシンの再保護の実行中に次のエラーのいずれかが表示されます。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。


### <a name="error-code-95226"></a>エラー コード 95226

*Azure 仮想マシンがオンプレミスの構成サーバーにアクセスできなかったため、再保護に失敗しました。*

このエラーは次の場合に発生します。 
1. Azure 仮想マシンがオンプレミスの構成サーバーに到達できず、その結果、構成サーバーから検出されず、登録されなかった可能性があります。 
2. オンプレミスの構成サーバーと通信するために実行する必要がある Azure 仮想マシン上の InMage Scout Application サービスが、フェールオーバー後に実行していない可能性があります。

この問題を解決するには
1. Azure 仮想マシンがオンプレミスの構成サーバーと通信できるように Azure 仮想マシンのネットワークが構成されていることを確認する必要があります。 確認するには、オンプレミスのデータセンターに対してサイト間 VPN を設定するか、Azure 仮想マシンの仮想ネットワーク上でプライベート ピアリングを使用して ExpressRoute 接続を構成します。 
2. Azure 仮想マシンがオンプレミスの構成サーバーと通信できるように既にネットワークを構成している場合は、仮想マシンにログインし、'InMage Scout Application Service' を確認します。 InMage Scout Application Service が実行されていないことを確認する場合は、サービスを手動で開始し、サービス開始の種類が [自動] に設定されていることを確認します。

### <a name="error-code-78052"></a>エラー コード 78052
再保護が次のエラー メッセージで失敗します: *仮想マシンの保護を完了できませんでした。*

このエラーは、2 つの理由で発生する可能性があります。
1. 再保護する仮想マシンが Windows Server 2016 です。 現在このオペレーティング システムは、フェールバックはサポートされていませんが、間もなくでサポートされています。
2. フェールバック先のマスター ターゲット サーバーに同じ名前の仮想マシンが既に存在します。

この問題を解決するには、別のホスト上にある別のマスター ターゲット サーバーを選択して、再保護によって別のホストに仮想マシンを作成し、名前が競合しないようにします。 また、vMotion を使用して、名前の競合が発生しない別のホストにマスター ターゲットを移動することもできます。 既存の仮想マシンがはぐれたコンピューターである場合は、同じ ESXi ホストで新しい仮想マシンを作成できるように、はぐれたコンピューターの名前を変更することができます。

### <a name="error-code-78093"></a>エラー コード 78093

*VM が実行されていないは停止状態か、アクセスできません。*

フェールオーバーした仮想マシンをオンプレミスで再保護するには、Azure 仮想マシンが実行されている必要があります。 これは、モビリティ サービスがオンプレミスの構成サーバーに登録され、プロセス サーバーと通信することでレプリケートを開始できるようにするためです。 マシンが不適切なネットワーク上にある場合、または実行されていない場合 (停止状態またはシャットダウン)、構成サーバーは、再保護を開始する仮想マシンのモビリティ サービスに到達できません。 仮想マシンを再起動して、オンプレミスで通信を開始できるようにします。 Azure 仮想マシンを起動した後に、再保護ジョブを再起動します。

### <a name="error-code-8061"></a>エラー コード 8061

"*データ ストアには、ESXi ホストからアクセスできません。*"

[マスター ターゲットの前提条件](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)とフェールバック用に[サポートされているデータストア](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback)を参照してください。


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>内部設置型に Azure の仮想マシンのフェールバックを実行するときにエラーをトラブルシューティングします。
内部設置型に Azure の仮想マシンのフェールバックの実行中に次のエラーのいずれかが表示されます。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。

### <a name="error-code-8038"></a>エラー コード 8038

*エラーが原因で、オンプレミスの仮想マシンを呼び出せませんでした*

これは、プロビジョニングするのに十分なメモリがないホストに内部設置型の仮想マシンを起動する場合に発生します。

この問題を解決するには

1. ESXi ホストにさらに多くのメモリをプロビジョニングできます。
1. vMotion を使用して、仮想マシンを起動するために充分なメモリを搭載した別の ESXi ホストに VM を移動します。