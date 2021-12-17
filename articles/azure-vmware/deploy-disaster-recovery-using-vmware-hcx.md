---
title: VMware HCX でディザスター リカバリーをデプロイする
description: VMware HCX Disaster Recovery を使用して、仮想マシン (VM) のディザスター リカバリーをデプロイする方法を説明します。 また、Azure VMware Solution を復旧用サイトおよび復旧データ保存先サイトとして使用する方法も説明します。
ms.topic: how-to
ms.date: 06/10/2021
ms.openlocfilehash: f08f1c1ccff0387c5750a355f83de02e1dcd8e09
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430990"
---
# <a name="deploy-disaster-recovery-using-vmware-hcx"></a>VMware HCX でディザスター リカバリーをデプロイする

この記事では、Azure VMware Solution プライベート クラウドを復旧用サイトおよび復旧データ保存先サイトとして使用し、VMware HCX によって仮想マシン (VM) のディザスター リカバリーをデプロイします。

>[!IMPORTANT]
>VMware HCX Disaster Recovery (DR) は HCX の一部ではありますが、大規模なデプロイはお勧めしません。 ディザスター リカバリーのオーケストレーションはすべて手動であり、Azure VMware Solution には現在、HCX DR の手動フェールオーバーを実行できる Runbook や機能がありません。 企業向けのディザスター リカバリーについては、VMware Site Recovery Manager (SRM) または VMware の事業継続とディザスター リカバリー (BCDR) ソリューションをご確認ください。

VMware HCX には、レプリケーション ポリシーのきめ細かい制御および細分性を提供するさまざまな操作が用意されています。 使用可能な操作は次のとおりです。

- **反転** - 障害が発生した後。 反転により、サイト B はソース サイトに、サイト A は保護されている VM が現在稼働している場所になります。

- **一時停止** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを一時停止します。

- **再開** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを再開します。

- **削除** - 選択した VM に関連付けられている現在のレプリケーション ポリシーを削除します。

- **今すぐ同期** - 保護されている VM に、境界を越えてソース VM を同期します。

このガイドでは、次のレプリケーション シナリオを対象にしています。

- 1 つの VM または VM のグループを保護する。

- 1 つの VM または VM のグループのテスト復旧を完了する。

- 1 つの VM または VM のグループを復旧させる。

- 1 つの VM または VM のグループの保護を反転させる。

## <a name="protect-vms"></a>VM の保護

1. ソース サイトで **vSphere クライアント** にログインし、**HCX プラグイン** にアクセスします。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="vSphere Web クライアントの HCX オプションを示すスクリーンショット。" border="true":::

1. **[ディザスター リカバリー]** 領域に入り、 **[VM の保護]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="vSphere Web クライアントの [ディザスター リカバリー] ダッシュボードを示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. ソースとリモートのサイトを選択します。 この場合、リモート サイトは、Azure VMware Solution プライベート クラウドにする必要があります。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="[HCX: Protected Virtual Machines]\(HCX 保護されている仮想マシン\) ウィンドウを示すスクリーンショット。" border="true":::

1. 必要に応じて、**既定のレプリケーション** オプションを選択します。

   - **Enable Compression (圧縮を有効にする):** 低スループットのシナリオにお勧めします。

   - **Enable Quiescence (休止を有効にする):** 一貫性のあるコピーがリモート サイトに同期されるように、VM を一時停止します。

   - **同期先ストレージ:** 保護されている VM のリモート データストアです。Azure VMware Solution のプライベート クラウドでは、これは vSAN データストアである必要があります。

   - **コンピューティング コンテナー:** リモートの vSphere クラスターまたはリソース プールです。

   - **同期先フォルダー:** リモートの同期先フォルダーです。これはオプションであり、フォルダーが選択されていない場合、VM は選択したクラスターの直下に配置されます。

   - **RPO:** ソース VM と保護されている VM の間の同期間隔。 5 分から 24 時間の間で、任意の値を指定できます。

   - **スナップショットの間隔:** スナップショット間の間隔です。

   - **スナップショットの数:** 構成されたスナップショット間隔内のスナップショットの合計数です。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="[Protect Virtual Machines]\(仮想マシンのレプリケーションの保護\) のレプリケーション オプションを示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 一覧から VM を 1 つ以上選択し、必要に応じてレプリケーション オプションを構成します。

   既定では、VM は、[既定のレプリケーション オプション] で構成されているグローバル設定ポリシーを継承します。 選択した VM の各ネットワーク インターフェイスに対して、リモート **ネットワーク ポート グループ** を構成し、 **[完了]** を選択して、保護プロセスを開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="[Protect Virtual Machines]\(仮想マシンの保護\) のネットワーク インターフェイス オプションを示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 同じディザスター リカバリー領域で、選択した VM それぞれについて、プロセスを監視します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="[Protect Virtual Machines]\(仮想マシンの保護\) の保護の監視の進行状況を示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. VM が保護された後は、 **[スナップショット]** タブで、さまざまなスナップショットを参照できます。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="[Protect Virtual Machines]\(仮想マシンの保護\) のスナップショット一覧を示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   黄色の三角形は、スナップショットと仮想マシンがテスト復旧操作でテストされていないことを意味します。

   電源がオフになっている VM と電源が入っている VM には、重要な違いがあります。 図は、電源が入っている VM の同期プロセスを示しています。 そこでは、同期プロセスが開始された後、VM の完全なコピーである最初のスナップショットが終えられます。その後、構成されている間隔で次のものが完了されます。 電源がオフになっている VM のコピーが同期された後、その VM が非アクティブとして表示され、保護操作が完了済みと表示されます。  VM の電源が入っているときには、リモート サイトに対する同期プロセスが開始されます。

## <a name="complete-a-test-recover-of-vms"></a>VM のテスト復旧の完了

1. Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント** にログインします。 
1. **HCX プラグイン** 内の [ディザスター リカバリー] 領域で、任意の VM の縦方向の省略記号を選択して、操作メニューを表示してから、 **[VM のテスト復旧]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="[Test Recovery VM]\(VM のテスト復旧\) メニュー オプションを示すスクリーンショット。" border="true":::

1. テストのオプションと、VM のさまざまな状態をテストするために使用するスナップショットを選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="テストする [Replica Snapshot instance]\(レプリカ スナップショット インスタンス\) を示すスクリーンショット。" border="true":::

1. **テスト** の選択後、復旧操作が開始されます。

1. 終了したら、Azure VMware Solution プライベートクラウドの vCenter で、新しい VM を確認できます。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="復旧操作の確認の概要を示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. VM やそこで実行されている任意のアプリケーションに対してテストを行った後は、クリーンアップを実行して、テスト インスタンスを削除します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="クリーンアップ テスト インスタンスを示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>VM の復旧

1. Azure VMware Solution のプライベート クラウドであるリモート サイトで **vSphere クライアント** にログインし、**HCX プラグイン** にアクセスします。

   復旧シナリオでは、VM のグループがこの例に使用されます。

1. 復旧する VM を一覧から選択し、 **[アクション]** メニューを開いて、 **[VM を復旧する]** を選択します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="[Recover VMs]\(VM を復旧する\) メニュー オプションを示すスクリーンショット。" border="true":::

1. 各インスタンスの復旧オプションを構成し、 **[復旧]** を選択して復旧操作を開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="ターゲット サイトへの VM の復旧の確認を示すスクリーンショット。" border="true":::

1. 復旧操作が完了すると、新しい VM がリモートの vCenter Server インベントリに表示されます。

## <a name="complete-a-reverse-replication-on-vms"></a>VM でのレプリケーションの反転の完了

1. Azure VMware Solution のプライベート クラウドで **vSphere クライアント** にログインし、**HCX プラグイン** にアクセスします。
   
   >[!NOTE]
   > レプリケーションの反転を開始する前に、ソース サイトで元の VM の電源がオフになっていることを確認します。 VM の電源がオフになっていないと操作は失敗します。

1. 一覧から、ソース サイトにレプリケートする VM を選択し、 **[アクション]** メニューを開いて **[反転]** を選択します。 
1. **[反転]** を選択してレプリケーションを開始します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="[Reverse]\(反転\) メニュー オプションを示すスクリーンショット。" border="true":::

1. 各 VM の詳細セクションで監視します。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="反転アクションの結果を示すスクリーンショット。" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

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

これらの API を使用して、ディザスター リカバリー プランの作成と実行を自動化するためのカスタム メカニズムを構築できます。
