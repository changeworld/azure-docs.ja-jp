---
title: AWS やその他のプラットフォームから Azure のマネージド ディスクに移行する
description: AWS などの他のクラウドやその他の仮想化プラットフォームからアップロードされた VHD を使用して Azure で VM を作成し、Azure マネージド ディスクを使用します。
author: roygara
manager: twooley
ms.service: storage
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68aaf58230ebadd7283e62baf232b84743bba535
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692416"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>アマゾン ウェブ サービス (AWS) やその他のプラットフォームから Azure のマネージド ディスクに移行する

**適用対象:** :heavy_check_mark: Windows VM 

AWS やオンプレミスの仮想化ソリューションから Azure に VHD ファイルをアップロードして、マネージド ディスクを使用する仮想マシン (VM) を作成できます。 Azure マネージド ディスクを使用すると、Azure IaaS VM のストレージ アカウントを管理する必要がなくなります。 ユーザーは必要なディスクの種類とサイズを指定するだけでよく、ディスクの作成と管理は Azure によって行われます。 

一般化された VHD と特殊化された VHD のいずれもアップロードできます。 
- **一般化した VMD**: Sysprep を使用してすべての個人アカウント情報が削除されています。 
- **特殊化された VHD**: ユーザー アカウント、アプリケーション、その他の状態データが元の VM から保持されます。 

> [!IMPORTANT]
> VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md)」に従う必要があります
>
>


| シナリオ                                                                                                                         | ドキュメント                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| マネージド ディスクを使用して Azure VM に移行する既存の AWS EC2 インスタンスがある                              | [PowerShell を使用してアマゾン ウェブ サービス (AWS) から Azure に Windows VM を移行する](aws-to-azure.md)                           |
| 複数の Azure VM を作成するためのイメージとして使用する他の仮想化プラットフォームの VM がある。 | [汎用化した VHD をアップロードして Azure で新しい VM を作成する](upload-generalized-managed.md) |
| Azure で再作成する一意にカスタマイズされた VM がある。                                                      | [特殊化されたディスクからの Windows VM の作成](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>マネージド ディスクの概要

Azure マネージド ディスクは、ストレージ アカウントを管理する必要をなくして VM 管理をシンプルにします。 マネージド ディスクでは、可用性セット内の VM の信頼性の向上というメリットもあります。 単一障害点を避けるために、可用性セット内の異なる VM のディスクは相互に十分に分離されます。 可用性セット内の異なる VM のディスクは異なるストレージ スケール ユニット (スタンプ) に自動的に配置されるため、ハードウェアとソフトウェアの障害を原因とする単一のストレージ スケール ユニット障害の影響が限定されます。
ニーズに基づいて、4 種類のストレージ オプションから選ぶことができます。 使用できるディスクの種類の詳細については、[ディスクの種類の選択](../disks-types.md)に関する記事を参照してください。

## <a name="plan-for-the-migration-to-managed-disks"></a>マネージド ディスクへの移行の計画

このセクションでは、VM とディスクの種類に関する最適な決定を行います。

管理されていないディスクからマネージド ディスクへの移行を計画している場合は、[仮想マシンの共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールが割り当てられているユーザーは VM のサイズを変更できないことに注意してください (それらは事前に変換されている可能性があるためです)。 マネージド ディスクを持つ VM が OS ディスク上での Microsoft.Compute/disks/write 権限をユーザーに要求するのは、これが理由です。

### <a name="location"></a>場所

Azure マネージド ディスクを使用できる場所を選びます。 Premium SSD に移行する場合は、移行を計画しているリージョンで Premium Storage が使用可能であることも確認してください。 使用できる場所に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/#services)」をご覧ください。

### <a name="vm-sizes"></a>VM サイズ

Premium SSD に移行する場合は、VM が配置されているリージョンで利用できる Premium Storage 対応サイズに合うように VM のサイズを更新する必要があります。 Premium Storage で対応できる VM サイズをご確認ください。 Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](../sizes.md)」に記載されています。
Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM サイズを選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

### <a name="disk-sizes"></a>ディスク サイズ

使用可能なディスクの種類とサイズの詳細については、「[Azure で利用できるディスクの種類](../disks-types.md)」を参照してください

### <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー 

**Premium Managed Disks**

既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。

### <a name="pricing"></a>価格

[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)をご確認ください。


## <a name="next-steps"></a>次の手順

- VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md)」に従う必要があります