---
title: Azure Stack Edge Pro デバイスの障害に備える
description: Azure Stack Edge Pro デバイスを交換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 0d36a09b67e110551470f3908800e77c35b1e3be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843666"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Azure Stack Edge Pro デバイスの障害に備える

この記事では、デバイスの障害に備えるための一助として、お使いの Azure Stack Edge Pro GPU デバイスのデバイス構成とデータを保存してバックアップする方法について説明します。 

この記事には、お使いの Azure Stack Edge Pro GPU デバイスにデプロイされた Kubernetes と IoT コンテナーをバックアップする手順は含まれていません。 

## <a name="understand-device-failures"></a>デバイスの障害について

Azure Stack Edge Pro GPU デバイスでは、2 種類のハードウェア障害が発生する可能性があります。

- 許容できる障害 (ハードウェア コンポーネントを交換する必要がある)。 このような障害が発生した場合、デバイスを機能低下状態で操作できます。 このような障害の例としては、1 つの電源装置 (PSU) の障害またはデバイス上の 1 つのディスク障害が挙げられます。 これらの各ケースでは、デバイスを引き続き動作させることができます。 障害が発生したコンポーネントを交換するために、できるだけ早く Microsoft サポートに連絡することをお勧めします。

- 許容できない障害 (デバイス全体の交換が必要になる)。 この障害の例としては、デバイス上の 2 つのディスクに障害が発生した場合などがあります。 このような場合は、Microsoft サポートに連絡し、デバイスの交換が必要であると判断されたら、Azure Stack Edge デバイスの交換の支援を受けます。

許容できない障害に備えるには、デバイスで次のバックアップを行う必要があります。

- デバイス構成に関する情報。
- Edge ローカル共有と Edge クラウド共有に格納されているデータ。
- デバイスで実行されている VM に関連付けられているファイルとフォルダー。


## <a name="back-up-device-configuration"></a>デバイス構成のバックアップ

デバイスの初期構成時には、[デプロイ チェックリスト](azure-stack-edge-gpu-deploy-checklist.md)に記載されているようなデバイス構成情報のコピーを取っておくことが重要です。 回復中に、この構成情報が使用されて新しい交換デバイスに適用されます。 

## <a name="protect-device-data"></a>デバイス データの保護

デバイス データは次のいずれかの種類です。

- Edge クラウド共有内のデータ
- ローカル共有内のデータ
- VM 上のファイルとフォルダー

以下のセクションでは、デバイス上のこれらの種類の各データを保護するための手順と推奨事項について説明します。

## <a name="protect-data-in-edge-cloud-shares"></a>Edge クラウド共有のデータを保護する

デバイスから Azure にデータを階層化する Edge クラウド共有を作成できます。 利用可能なネットワーク帯域幅によっては、許容できない障害が発生した場合のデータ損失を最小限に抑えるために、ご使用のデバイスに帯域幅テンプレートを構成します。

> [!IMPORTANT] 
> 許容できない障害がデバイスで発生した場合、デバイスから Azure に階層化されていないローカル データが失われる可能性があります。 

## <a name="protect-data-in-edge-local-shares"></a>Edge ローカル共有のデータを保護する

Kubernetes または IoT Edge をデプロイしている場合は、アプリケーション データをローカルのデバイスに保存し、Azure Storage とは同期しないように構成します。 データは、デバイス上の共有に格納されます。 これらの共有のデータをバックアップすることが重要な場合があります。

次のサードパーティのデータ保護ソリューションでは、ローカル SMB または NFS 共有内のデータのバックアップ ソリューションを提供できます。 

| サードパーティ製ソフトウェア           | ソリューションへの参照                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> 詳細については、Cohesity にお問い合わせください。          |
| Veritas                        | 詳細については、Veritas にお問い合わせください。   |


## <a name="protect-files-and-folders-on-vms"></a>VM 上のファイルとフォルダーの保護

Azure Stack Edge は、Azure Backup やその他のサードパーティのデータ保護ソリューションと連携して、デバイスにデプロイされた VM に含まれるデータを保護するバックアップ ソリューションを提供します。 次の表に、選択できる使用可能なソリューションへの参照を示します。


| バックアップ ソリューション        | サポート対象 OS   | 関連項目                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup 用の Microsoft Azure Recovery Services (MARS) エージェント | Windows        | [MARS エージェントについて](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows、Linux | [Microsoft Azure の統合、バックアップ、回復のソリューションの概要](https://www.cohesity.com/solution/cloud/azure) <br>詳細については、Cohesity にお問い合わせください。                          |
| Veritas                 | Windows、Linux | 詳細については、Veritas にお問い合わせください。                    |



## <a name="next-steps"></a>次のステップ

- 「[障害が発生した Azure Stack Edge Pro GPU デバイスから回復する](azure-stack-edge-gpu-recover-device-failure.md)」方法を確認します。
