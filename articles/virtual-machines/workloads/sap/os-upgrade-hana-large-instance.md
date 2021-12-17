---
title: Azure SAP HANA のオペレーティング システムのアップグレード (ラージ インスタンス) | Microsoft Docs
description: Azure 上で SAP HANA 用のオペレーティング システム (ラージ インスタンス) のアップグレードを行う方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e980417c2c48455e43143fb2f870a63d6395f94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546208"
---
# <a name="operating-system-upgrade"></a>オペレーティング システムのアップグレード
この記事では、BareMetal Infrastructure とも呼ばれる、HANA Large Instances (HLI) のオペレーティング システム (OS) のアップグレードについて詳しく説明します。

>[!NOTE]
>OS をアップグレードすることはお客様の責任となります。 Microsoft の運用サポートはアップグレードの主要な領域についてご案内できますが、アップグレードを計画する際はオペレーティング システムのベンダーにもご相談ください。

HLI のプロビジョニング中に、Microsoft の運用チームによってオペレーティング システムがインストールされます。
お客様は、オペレーティング システムを保守する必要があります。 たとえば、HLI に対する修正プログラム、チューニング、アップグレードなどを実行する必要があります。 オペレーティング システムの大幅な変更 (SP1 から SP2 へのアップグレードなど) を行う前に、サポート チケットを開いて Microsoft の運用チームに連絡してください。 お客様と相談します。 アップグレードの少なくとも 1 週間前にはこのチケットを開くことをお勧めします。 

チケットに含める:

* HLI サブスクリプション ID。
* サーバー名。
* 適用を予定しているパッチ レベル。
* この変更を予定している日付。 

各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。

## <a name="known-issues"></a>既知の問題

アップグレードにはいくつかの既知の問題があります。
- タイプ II クラスの SKU で､OS のアップグレード中に SFS が削除されます｡ OS のアップグレードが完了した後に、互換性のある SFS を再インストールする必要があります。
- イーサネット カードのドライバー (ENIC および FNIC) が古いバージョンにロールバックされます｡ アップグレード後に互換性のあるドライバー バージョンを再インストールする必要があります。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA L インスタンス (タイプ I) の推奨構成

OS の構成では、時間の経過とともに推奨設定からのドリフトが生じることがあります。 このドリフトは、修正プログラムの適用、システムのアップグレード、およびその他の変更によって発生する可能性があります。 Microsoft では、HANA Large Instances を最適に構成して最良のパフォーマンスと回復性を実現するために必要な更新プログラムを特定しています。 次の手順では、ネットワーク パフォーマンス、システムの安定性、および最適な HANA パフォーマンスに関する推奨事項の概要を示します。

### <a name="compatible-enicfnic-driver-versions"></a>互換性のある eNIC/fNIC ドライバーのバージョン
  適切なネットワーク パフォーマンスとシステムの安定性を確保するために、次の互換性に関する表に従って OS 固有の適切なバージョンの eNIC および fNIC ドライバーがインストールされていることを確認してください。 サーバーは、互換性のあるバージョンの顧客に配信されます。 ただし、ドライバーは、OS またはカーネルの修正プログラムの適用中に、既定のバージョンにロールバックされる場合があります。 OS またはカーネルの修正プログラム適用後に、適切なドライバーのバージョンが実行されていることを確認してください。
       
      
  |  OS ベンダー    |  OS パッケージ バージョン     |  Firmware Version  |  eNIC ドライバー |  fNIC ドライバー | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>ドライバーのアップグレードおよび古い rpm パッケージをクリーニングするためのコマンド

#### <a name="command-to-check-existing-installed-drivers"></a>インストールされている既存のドライバーを確認するコマンド
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>既存の eNIC/fNIC rpm を削除する
```
rpm -e <old-rpm-package>
```
#### <a name="install-recommended-enicfnic-driver-packages"></a>推奨される eNIC/fNIC ドライバー パッケージをインストールする
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-installation"></a>インストールを確認するコマンド
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS のアップグレード中に eNIC/fNIC ドライバーをインストールする手順

* OS バージョンをアップグレードします
* 古い rpm パッケージを削除します
* インストールされている OS バージョンに合わせて、互換性のある eNIC/fNIC ドライバーをインストールします
* システムを再起動します
* 再起動後に、eNIC/fNIC のバージョンを確認します


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 更新エラー
アップグレード後、SAP on Azure HANA L インスタンス (タイプ I) は起動不可能な状態になることがあります。 次の手順で、この問題を修正します。

#### <a name="execution-steps"></a>実行ステップ

-   `multipath -ll` コマンドを実行します。
-   論理ユニット番号 (LUN) ID を取得するか、`fdisk -l | grep mapper` コマンドを使用します。
-   行 `/dev/mapper/<LUN ID>` を使用して `/etc/default/grub_installdevice` ファイルを更新します。 例: /dev/mapper/3600a09803830372f483f495242534a56

>[!NOTE]
>LUN ID はサーバーによって異なります。


### <a name="disable-error-detection-and-correction"></a>エラー検出と修正を無効にする 
   エラー検出と修正 (EDAC) モジュールは、メモリ エラーの検出と修正に役立ちます。 ただし、基になる HLI Type I ハードウェアによってメモリ エラーは既に検出されて修正されています。 ハードウェアと OS のレベルで同じ機能を有効にすると、競合が発生し、サーバーが予期せずシャットダウンする可能性があります。 OS から EDAC モジュールを無効にすることをお勧めします。

#### <a name="execution-steps"></a>実行ステップ

- EDAC モジュールが有効になっているかどうかを確認します。 次のコマンドから出力が返される場合、モジュールは有効になっています。

```
lsmod | grep -i edac 
```
- ファイル `/etc/modprobe.d/blacklist.conf` に次の行を追加してモジュールを無効にする
```
blacklist sb_edac
blacklist edac_core
```
変更を有効にするには、再起動が必要です。 再起動後、`lsmod` コマンドを再度実行し、モジュールが有効になっていないことを確認します。

### <a name="kernel-parameters"></a>カーネル パラメーター
`transparent_hugepage`、`numa_balancing`、`processor.max_cstate`、`ignore_ce`、および `intel_idle.max_cstate` の正しい設定が適用されていることを確認します。

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>実行ステップ

- これらのパラメーターを `/etc/default/grub` ファイルの `GRB_CMDLINE_LINUX` 行に追加します。

```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
- 新しい grub ファイルを作成します。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
- システムを再起動します。

## <a name="next-steps"></a>次のステップ

SUSE Linux 用 SMT サーバーの設定方法について説明します。

> [!div class="nextstepaction"]
> [SUSE Linux 用 SMT サーバーの設定](hana-setup-smt.md)