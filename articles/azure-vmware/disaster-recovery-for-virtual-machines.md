---
title: 仮想マシンのディザスター リカバリーを完了する
description: この記事では、Azure VMware Solution を使用して仮想マシンのディザスター リカバリーを完了する方法について説明します
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579287"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Azure VMware Solution を使用して仮想マシンのディザスター リカバリーを完了する

この記事では、VMware HCX ソリューションと、復旧またはターゲット用サイトとしての Azure VMware Solution プライベート クラウドを使用して、仮想マシン (VM) のディザスター リカバリーを完了するプロセスについて説明します。

VMware HCX には、レプリケーション ポリシーのきめ細かい制御および細分性を提供するさまざまな操作が用意されています。 使用可能な操作は次のとおりです。

- **反転** - 障害が発生した後。 反転により、サイト B はソース サイトに、サイト A は保護されている VM が現在稼働している場所になります。

- **一時停止** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを一時停止します。

- **再開** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを一時停止します。

- **削除** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを削除します。

- **今すぐ同期** - 保護されている VM に、境界を越えてソース VM を同期します。

このガイドでは、次のレプリケーション シナリオを対象にしています。

- 1 つの VM または VM のグループを保護する。

- 1 つの VM または VM のグループのテスト復旧を完了する。

- 1 つの VM または VM のグループを復旧させる。

- 1 つの VM または VM のグループの保護を反転させる。

## <a name="protect-vms"></a>VM の保護

1. ソース サイトで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="vSphere の HCX オプション" border="true":::

1. **[ディザスター リカバリー]** 領域に入り、 **[VM の保護]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. ソースとリモートのサイトを選択します。 この場合、リモート サイトは、Azure VMware Solution プライベート クラウドにする必要があります。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="vSphere の HCX オプション" border="true":::

1. 必要に応じて、**既定のレプリケーション** オプションを選択します。

   - **Enable Compression (圧縮を有効にする):** 低スループットのシナリオにお勧めします。

   - **Enable Quiescence (休止を有効にする):** 一貫性のあるコピーがリモート サイトに同期されるように、VM を一時停止します。

   - **同期先ストレージ:** 保護されている VM のリモート データストアです。Azure VMware Solution のプライベート クラウドでは、これは vSAN データストアである必要があります。

   - **コンピューティング コンテナー:** リモートの vSphere クラスターまたはリソース プールです。

   - **同期先フォルダー:** リモートの同期先フォルダーです。これはオプションであり、フォルダーが選択されていない場合、VM は選択したクラスターの直下に配置されます。

   - **RPO:** ソース VM と、保護されている VM との間の同期間隔です。5 分から 24 時間までの任意の間隔にできます。

   - **スナップショットの間隔:** スナップショット間の間隔です。

   - **スナップショットの数:** 構成されたスナップショット間隔内のスナップショットの合計数です。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 一覧から VM を 1 つ以上選択し、必要に応じてレプリケーション オプションを構成します。

   既定では、VM は、[既定のレプリケーション オプション] で構成されているグローバル設定ポリシーを継承します。 選択した VM の各ネットワーク インターフェイスに対して、リモート **ネットワーク ポート グループ**を構成し、 **[完了]** を選択して、保護プロセスを開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 同じディザスター リカバリー領域で、選択した VM それぞれについて、プロセスを監視します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM が保護された後は、 **[スナップショット]** タブで、さまざまなスナップショットを参照できます。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   黄色の三角形は、スナップショットと仮想マシンがテスト復旧操作でテストされていないことを意味します。

   電源がオフになっている VM と電源が入っている VM には、重要な違いがあります。 このイメージは、電源が入っている VM の同期プロセスを示しています。 そこでは、同期プロセスが開始された後、VM の完全なコピーである最初のスナップショットが終えられます。その後、構成されている間隔で次のものが完了されます。 電源がオフになっている VM の場合は、コピーが同期されてから VM が非アクティブと表示され、保護操作が完了済みと表示されます。  VM の電源が入っているときには、リモート サイトに対する同期プロセスが開始されます。

## <a name="complete-a-test-recover-of-vms"></a>VM のテスト復旧の完了

1. Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント**にログインします。 
1. **HCX プラグイン**内の [ディザスター リカバリー] 領域で、任意の VM の縦方向の省略記号を選択して、操作メニューを表示してから、 **[VM のテスト復旧]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="vSphere の HCX オプション" border="true":::

1. テストのオプションと、VM のさまざまな状態をテストするために使用するスナップショットを選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="vSphere の HCX オプション" border="true":::

1. **テスト**の選択後、復旧操作が開始されます。

1. 終了したら、Azure VMware Solution プライベートクラウドの vCenter で、新しい VM を確認できます。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. VM やそこで実行されている任意のアプリケーションに対してテストを行った後は、クリーンアップを実行して、テスト インスタンスを削除します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>VM の復旧

1. Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。

   復旧シナリオでは、VM のグループがこの例に使用されます。

1. 復旧する VM を一覧から選択し、 **[アクション]** メニューを開いて、 **[VM を復旧する]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="vSphere の HCX オプション" border="true":::

1. 各インスタンスの復旧オプションを構成し、 **[復旧]** を選択して復旧操作を開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="vSphere の HCX オプション" border="true":::

1. 復旧操作が完了すると、新しい VM がリモートの vCenter Server インベントリに表示されます。

## <a name="complete-a-reverse-replication-on-vms"></a>VM でのレプリケーションの反転の完了

1. Azure VMware Solution のプライベート クラウドで **vSphere クライアント**にログインし、**HCX プラグイン**にアクセスします。
   
   >[!NOTE]
   >レプリケーションの反転を開始する前に、ソース サイトで元の VM の電源がオフになっていることを確認します。 VM の電源がオフになっていないと操作は失敗します。

1. 一覧から、ソース サイトにレプリケートする VM を選択し、 **[アクション]** メニューを開いて **[反転]** を選択します。 
1. **[反転]** を選択してレプリケーションを開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="vSphere の HCX オプション" border="true":::

1. 各 VM の詳細セクションで監視します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="vSphere の HCX オプション" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>ディザスター リカバリー プランの自動化

現在、VMware HCX には、ディザスター リカバリー プランを作成して自動化するための組み込みメカニズムがありません。 ただし、VMware HCX には、ディザスター リカバリー操作用の API を含む REST API のセットが用意されています。 API 仕様には、VMware HCX Manager 内から URL でアクセスできます。

これらの API は、ディザスター リカバリーの次の操作を対象としています。

- 保護

- 復旧

- テスト復旧

- 計画された復旧

- Reverse

- クエリ

- テストのクリーンアップ

- Pause

- Resume

- 保護の削除

- 再構成

JSON での復旧操作ペイロードの例を次に示します。

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

これらの API を使用すると、お客様はカスタム メカニズムを構築して、ディザスター リカバリー プランの作成と実行を自動化できます。
