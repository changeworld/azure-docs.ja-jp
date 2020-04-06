---
title: Azure NetApp Files を使用したソリューション アーキテクチャ | Microsoft Docs
description: Azure NetApp Files を使用したソリューション アーキテクチャのベスト プラクティスへの参照を提供します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: b-juche
ms.openlocfilehash: 8eae528c965e599e7adfb546a09b0d5879e7c54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369521"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files を使用したソリューション アーキテクチャ
この記事では、Azure NetApp Files を使用するためのソリューション アーキテクチャを理解するうえで役立つベスト プラクティスへの参照を提供します。  

## <a name="azure-high-performance-computing-hpc-solutions"></a>Azure ハイ パフォーマンス コンピューティング (HPC) ソリューション

* [Azure 上での貯留層シミュレーション ソフトウェアの実行](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)
* [Azure Batch と Azure NetApp Files で MPI ワークロードを実行する](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

## <a name="azure-kubernetes-service-aks-solutions"></a>Azure Kubernetes Service (AKS) ソリューション

* [AKS との Azure NetApp Files の統合](https://github.com/andyzhangx/demo/tree/master/linux/nfs)
* [Azure NetApp Files と Azure Kubernetes Service を統合する](https://docs.microsoft.com/azure/aks/azure-netapp-files)

## <a name="oracle-database-solutions"></a>Oracle データベース ソリューション

* [Azure NetApp Files を使用した Azure への Oracle デプロイのベスト プラクティス ガイド](https://www.netapp.com/us/media/tr-4780.pdf)
* [Microsoft Azure での Oracle VM イメージとそのデプロイ:共有ストレージの構成オプション](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)

## <a name="sap-application-solutions"></a>SAP アプリケーション ソリューション 

* [Azure NetApp Files を使用した Microsoft Azure 上の SAP アプリケーション](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [RedHat Enterprise Linux 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
* [Azure NetApp Files – SAP HANA のバックアップ (秒単位)](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – スナップショット バックアップから HANA データベースを復元する](https://blog.netapp.com/azure-netapp-files-backup-sap-hana/)
* [Azure NetApp Files – クラウド同期を使用した SAP HANA のオフロード バックアップ](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp Files を使用して SAP HANA システムのコピーを高速化する](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP と Azure NetApp Files:SAP HANA システムの移行が簡単に](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/) 

## <a name="global-file-caching-solutions"></a>グローバル ファイル キャッシュ ソリューション

* [Azure NetApp Files および Talon FAST™ とのグローバルに配布されたエンタープライズ ファイルの共有](https://www.talonstorage.com/products/azure-netapp-files)

## <a name="windows-virtual-desktopvdi-solutions"></a>Windows Virtual Desktop/VDI ソリューション

* [Azure NetApp Files を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

