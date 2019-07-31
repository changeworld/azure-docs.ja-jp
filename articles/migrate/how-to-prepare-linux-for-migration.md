---
title: Azure Migrate Server Migration を使用して、Linux を実行している VMware VM の Azure への移行を準備する | Microsoft Docs
description: Azure Migrate Server Migration を使用して Linux VM を Azure に移行する準備を行う方法について説明します
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810218"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Azure への Linux VMware VM の移行を準備する 

この記事では、[Azure Migrate ](migrate-overview.md)を使用して Linux を実行している VMware VM を Azure に移行するときに、その準備を行う方法について説明します。 

> [!NOTE]
> Azure Migrate Server Migration は現在、パブリック プレビュー段階です。 既存の GA バージョンの Azure Migrate を使用して、移行対象の VM を検出して評価することができますが、実際の移行は、既存の GA バージョンではサポートされていません。

次のように Linux マシンを準備します。

1. Hyper-V Linux 統合サービスをインストールします。 Linux ディストリビューションの新しいバージョンには、既定でこれがインストールされている場合があります。
2. 必要な Hyper-V ドライバーが含まれるように、そして VM が Azure 内で起動するように (一部のディストリビューションで必須です)、Linux の init イメージをリビルドします。
3. トラブルシューティングのためにシリアル コンソールのログ記録を有効にします。 [詳細情報](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)。
4. 永続的なデバイス識別子を使用するよう、デバイス名とボリュームの関連付けが含まれているデバイスのマップ ファイルを更新します。
5. 永続的なボリューム識別子を使用するよう、fstab エントリを更新します。
6. MAC アドレスなどに基づいてインターフェイス名を予約する udev ルールを削除します。
7. DHCP IP アドレスを受け取るよう、ネットワーク インターフェイスを更新します。
8. ssh が有効になっていることを確認します。 再起動時に自動的に開始するよう sshd サービスが設定されていることを確認します。
9. 受信 SSH 接続要求が、オペレーティング システム ファイアウォールまたは IP テーブル ルールによってブロックされないようにします。

最も一般的な Linux ディストリビューションに対してこれらの変更を行う方法の[詳細を確認](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)します。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプト (prepare-for-azure.sh) には、Linux マシンを準備するためのサンプルが用意されています。 スクリプトは、すべてのディストリビューションと環境で動作するとは限りませんが、便利な開始点です。

スクリプトは、以下を行う方法を示します。 

- 必要に応じて、必要なドライバーを使用して Linux の init イメージを再生成します。
- 永続的なボリューム識別子を使用するよう、fstab エントリを更新します。
- シリアル ポートにコンソールのログをリダイレクトします。
- Azure シリアル アクセス コンソールを有効にします
- udev net ルールを削除します
- VM が起動するときに実行される起動スクリプトに実行を挿入します。 マシンが Azure 内で実行されているかどうかを確認します。 そうである場合は、VM 上のネットワーク構成が更新され、DHCP を使用して IP アドレスを取得するように最初のイーサネット インターフェイスが設定されます。

### <a name="before-you-start"></a>開始する前に

- サンプル スクリプトには、サンプルの手順が含まれています。 実稼働システム上では実行しないでください。 それが実行される VM が損傷または破損する可能性があります。
- テスト VM 上で実行することをお勧めします。 開始する前に、VM のバックアップまたはスナップショットを取得して、必要に応じて VM を復元できるようにします。 
- スクリプトは、VM で次のいずれかの Linux ディストリビューションが実行されている場合に機能します。
    - Red Hat Enterprise Linux 6.5+、7.1+
    - Cent OS 6.5+、7.1+
    - SUSE Linux Enterprise Server 12 SP1、SP2、SP3
    - Ubuntu 14.04、16.04、18.04
    - Debian 7、8

### <a name="run-the-script"></a>スクリプトを実行する

1. sftp または FileZilla や WinScp などの scp クライアントを使用して、Linux テスト VM にスクリプトをコピーします。
2. 管理者アカウントを使用して Linux マシンに SSH 接続します。
3. スクリプト ディレクトリに移動します。
4. スクリプトを実行可能ファイルにするには、**sudo chmod 777 prepare-for-azure.sh** を実行します。
5. **./prepare-for-azure.sh** を使用してスクリプトを実行します。

スクリプトの実行方法を次に示します。

![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script6.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script11.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script16.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script21.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux スクリプト](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>次の手順

- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
