<properties 
   pageTitle="StorSimple デバイスを監視する | Microsoft Azure"
	description="StorSimple Manager サービスを使用して I/O パフォーマンス、容量使用率、ネットワーク スループット、およびデバイスのパフォーマンスを監視する方法について説明します。"
	services="storsimple"
	documentationCenter="NA"
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="09/02/2015"
	ms.author="alkohli"/>

# StorSimple Manager サービスを使用した StorSimple デバイスの監視 

## 概要

StorSimple Manager サービスを使用して、StorSimple ソリューション内にある特定の StorSimple デバイスを監視できます。I/O パフォーマンス、容量使用率、ネットワーク スループット、およびデバイスのパフォーマンス メトリックに基づいて、カスタム グラフを作成できます。

特定のデバイスの監視情報を表示するには、Azure ポータルで、StorSimple Manager サービスを選択し、**[監視]** タブをクリックして、デバイスの一覧から選択します。**[監視]** ページには、次の情報が表示されます。

## I/O パフォーマンス 

**I/O パフォーマンス**は、ホスト サーバーの iSCSI イニシエーター インターフェイスとデバイスとの間またはデバイスとクラウドとの間の読み取り操作と書き込み操作の数に関連するメトリックを追跡します。このパフォーマンスは、特定のボリューム、特定のボリューム コンテナー、またはすべてのボリューム コンテナーについて測定できます。

次の表では、実稼働環境のデバイスの全ボリュームに対する、デバイスへのイニシエーター用 IO を示しています。プロットされたメトリックは、1 秒あたりの読み取りと書き込みバイト数、1 秒あたりの読み取りと書き込み IO 操作、読み取りと書き込み待機時間です。

![イニシエーターからデバイスへの IO パフォーマンス](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

同じデバイスの場合、すべてのボリューム コンテナーに対して、デバイスからクラウドのデータについて IO がプロットされます。このデバイスでは、データはリニア層のみなので、クラウドには何も書き込まれません。デバイスからクラウドへの読み取りと書き込み操作は実行されません。そのため、グラフのピークは 5 分間隔で発生し、デバイスとサービス間でハートビートが確認される頻度に対応します。

![デバイスからクラウドへの IO パフォーマンス](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


同じデバイスで、午後 2 時 00 分から開始してクラウド スナップショットがボリューム データに作成されています。これにより、デバイスからクラウドへのデータ フローが発生します。読み取りと書き込みはこの期間にクラウドで行われます。IO グラフは、スナップショット作成時に対応するさまざまなメトリックでのピークを示しています。

![クラウド スナップショット後のデバイスからクラウドへの IO パフォーマンス](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## 容量使用率 

**容量使用率**は、ボリューム、ボリューム コンテナー、デバイスで使用されるデータ ストレージ領域の容量に関連するメトリックを追跡します。プライマリ ストレージ、クラウド ストレージ、またはデバイス ストレージの容量の使用率に基づいて、レポートを作成できます。容量使用率は、特定のボリューム、特定のボリューム コンテナー、またはすべてのボリューム コンテナーについて測定できます。

プライマリ、クラウド、デバイス ストレージ容量は次のように説明できます。

- **プライマリ ストレージの容量使用率**は、StorSimple ボリュームに書き込まれたデータが重複除去されて圧縮される前のデータ量を示しています。次のグラフは、クラウド スナップショットが作成された前後における、StorSimple デバイスのプライマリ ストレージの容量使用率を示します。これは単にボリューム データのみであり、クラウド スナップショットによってプライマリ ストレージは変更されません。グラフに示すとおり、クラウド スナップショットを作成したことによるプライマリの容量使用率に違いは見られません。クラウド スナップショットはそのデバイスで午後 2 時 00 分頃に開始されたことにご注意ください。

	![クラウド スナップショット前のプライマリの容量使用率](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)
	
	![クラウド スナップショット後のプライマリの容量使用率](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)


- **クラウド ストレージの容量使用率**は、使用されているクラウド ストレージの容量を示しています。このデータは、重複除去されて圧縮されています。この容量に含まれるクラウド スナップショットには、過去の目的や必須の保有期間のために保持されており、どのプライマリ ボリュームにも反映されていないデータが含まれている可能性があります。プライマリ ストレージとクラウド ストレージの消費量の数値を比較して、データの削減率を把握できます。ただし、この数値は正確なものではありません。次のグラフは、クラウド スナップショットが作成された前後における、StorSimple デバイスのクラウド ストレージの容量使用率を示します。午後 2 時 00 分頃にクラウド スナップショットがそのデバイス上で開始され、同時にクラウドの容量使用率が 5.73 MB から 4.04 GB に急上昇したことがわかります。

	![クラウド スナップショット前のクラウドの容量使用率](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

	![クラウド スナップショット後のクラウドの容量使用率](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


- **デバイス ストレージの容量使用率**は、デバイスの使用率の合計を示しています。これには SSD リニア層が含まれているため、プライマリ ストレージの使用率よりも大きくなります。この層には、デバイスの他の層にあるデータの容量も含まれています。SSD リニア層の容量は、新しいデータが入ってくると古いデータが HDD 層に、その後クラウドに移動されて循環します (このときデータは重複除去されて圧縮されます)。

	時間が経つにつれて、プライマリの容量使用率とデバイスの容量使用率は、ほとんどの場合、共に増加していき、やがてデータがクラウドに積み上がり始めます。そうなると、デバイスの容量使用率が安定し始めることもありますが、さらに多くのデータが書き込まれると、プライマリの容量使用率が増大します。

	次のグラフは、クラウド スナップショットが作成された前後における、StorSimple デバイスのプライマリ ストレージの容量使用率を示します。クラウド スナップショットは午後 2 時 00 分に開始され、デバイスの容量使用率は、その時点から減少し始めます。デバイスの容量使用率は 11.58 GB から 7.48 GB まで減少します。ほとんどの場合、これは SSD リニア層の圧縮されていないデータが重複除去されて、圧縮され、HDD 層に移動したことを示しています。デバイスの SSD 層と HDD 層の両方に既に大量のデータがあると、この減少が発生しない場合があることにご注意ください。この例では、デバイスに少量のデータがあります。

	![クラウド スナップショット前のデバイスの容量使用率](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

	![クラウド スナップショット後のデバイスの容量使用率](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## ネットワーク スループット

**ネットワーク スループット**は、ホスト サーバーの iSCSI イニシエーター ネットワーク インターフェイスからデバイス、デバイスとクラウドとの間で転送されたデータ量に関連するメトリックを追跡します。デバイス上の iSCSI ネットワーク インターフェイスごとに、このメトリックを監視できます。

次のグラフでは、Data 0 と Data 4 のネットワーク スループットを示しており、どちらもデバイス上の 1 GbE ネットワーク インターフェイスです。この場合、Data 0 がクラウド対応データで、Data 4 は iSCSI 対応です。StorSimple デバイスの受信と送信トラフィックの両方を確認できます。午後 3 時 24 分から開始する平坦な線は、5 分間隔でのみデータを収集したことが原因であり、無視する必要があります。

![Data4 のネットワーク スループット](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Data4 のネットワーク スループット](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## デバイスのパフォーマンス 

**デバイスのパフォーマンス**は、デバイスのパフォーマンスに関連するメトリックを追跡します。次の表では、実稼働環境のデバイスに対する CPU 使用率の統計値を示しています。

![デバイスの CPU 使用率](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## 次のステップ

[StorSimple Manager サービスのデバイス ダッシュボードを使用する方法の詳細](storsimple-device-dashboard.md)

<!---HONumber=September15_HO1-->