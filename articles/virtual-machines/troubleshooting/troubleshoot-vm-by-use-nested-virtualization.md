---
title: Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う | Microsoft Docs
description: Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う方法
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: 4ef8bc029c63aaf297462a7b53f6daba1a7c850b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028434"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う

この記事では、入れ子になった仮想化環境を Microsoft Azure に作成し、問題のある VM のディスクを Hyper-V ホスト (復旧 VM) にマウントしてトラブルシューティングを行う方法について説明します。

## <a name="prerequisites"></a>前提条件

問題のある VM をマウントするには、問題のある VM と同じタイプのストレージ アカウント (Standard または Premium) が復旧 VM に使用されている必要があります。

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>手順 1:復旧 VM を作成し、Hyper-V ロールをインストールする

1.  新しい復旧 VM を作成します。

    -  オペレーティング システム:Windows Server 2016 Datacenter

    -  サイズ:入れ子になった仮想化をサポートする少なくとも 2 つのコアを備えたすべての V3 シリーズ。 詳細については、「[Introducing the new Dv3 and Ev3 VM sizes (新しい VM サイズ Dv3 および Ev3 の発表)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)」を参照してください。

    -  問題のある VM と同じ場所、ストレージ アカウント、リソース グループ。

    -  問題のある VM と同じストレージの種類 (Standard または Premium) を選択。

2.  復旧 VM の作成後、その復旧 VM にリモート デスクトップで接続します。

3.  サーバー マネージャーで **[管理]**  >  **[役割と機能の追加]** の順に選択します。

4.  **[インストールの種類]** セクションで **[役割ベースまたは機能ベースのインストール]** を選択します。

5.  **[対象サーバーの選択]** セクションで、復旧 VM が選択されていることを確認します。

6.  **[Hyper-V ロール]**  >  **[機能の追加]** の順に選択します。

7.  **[機能]** セクションで **[次へ]** を選択します。

8.  仮想スイッチが利用可能な場合は、それを選択します。 それ以外の場合は、 **[次へ]** を選択します。

9.  **[移行]** セクションで **[次へ]** を選択します。

10. **[既定の保存場所]** セクションで **[次へ]** を選択します。

11. サーバーを自動的に再起動するためのチェック ボックスを必要に応じてオンにします。

12. **[インストール]** を選択します。

13. サーバーに Hyper-V ロールのインストールを許可します。 これには数分かかります。サーバーは自動的に再起動します。

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>手順 2:復旧 VM の Hyper-V サーバー上に問題のある VM を作成する

1.  問題のある VM の OS ディスクの[スナップショット ディスクを作成](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk)し、そのスナップショット ディスクを復旧 VM に接続します。

2.  復旧 VM にリモート デスクトップ接続します。

3.  ディスクの管理 (diskmgmt.msc) を開きます。 問題のある VM のディスクが **[オフライン]** に設定されていることを確認します。

4.  Hyper-V マネージャーを開き、 **[サーバー マネージャー]** で **Hyper-V ロール**を選択します。 そのサーバーを右クリックし、 **[Hyper-V マネージャー]** を選択します。

5.  Hyper-V マネージャーで、復旧 VM を右クリックし、 **[新規]**  >  **[仮想マシン]**  >  **[次へ]** の順に選択します。

6.  VM の名前を入力し、 **[次へ]** を選択します。

7.  **[第 1 世代]** を選択します。

8.  起動メモリを 1024 MB 以上に設定します。

9. 該当する場合、作成済みの Hyper-V ネットワーク スイッチを選択します。 それ以外の場合、次のページに進みます。

10. **[後で仮想ハード ディスクを接続する]** を選択します。

    ![[後で仮想ハード ディスクを接続する] オプションの画像](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. VM が作成されたら **[完了]** を選択します。

12. 作成した VM を右クリックし、 **[設定]** を選択します。

13. **[IDE コントローラー 0]** を選択し、 **[ハード ドライブ]** を選択して、 **[追加]** をクリックします。

    ![新しいハード ドライブの追加に関する画像](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. **[物理ハード ディスク]** で、Azure VM にアタッチされている問題のある VM のディスクを選択します。 ディスクが一覧にない場合は、[ディスクの管理] でそのディスクがオフラインに設定されているかどうかを確認してください。

    ![ディスクのマウントに関する画像](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。

16. VM をダブルクリックして起動します。

17. これでその VM をオンプレミス VM として使用することができます。 必要なトラブルシューティング手順を実施することができます。

## <a name="step-3-replace-the-os-disk-used-by-the-problem-vm"></a>手順 3:問題のある VM によって使用されている OS ディスクを交換する

1.  VM をオンライン状態に復帰させた後、その VM を Hyper-V マネージャーでシャットダウンします。

2.  [修復された OS ディスクのマウントを解除して切断](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
)します。
3.  [VM によって使用されている OS ディスクを修復された OS ディスクに交換](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
)します。

## <a name="next-steps"></a>次のステップ

VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。
