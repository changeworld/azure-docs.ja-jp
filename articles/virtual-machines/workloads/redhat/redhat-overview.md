---
title: Azure 上の Red Hat ワークロードの概要 | Microsoft Docs
description: Azure で利用できる Red Hat 製品オファリングについて学習します
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454976"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上の Red Hat ワークロード
Azure では、さまざまなオファリングを通じて Red Hat ワークロードがサポートされます。 Red Hat Enterprise Linux (RHEL) イメージは、Red Hat Update Infrastructure (RHUI) と同様、RHEL ワークロードの中核にあります。

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux (RHEL) イメージ
Azure では、Azure 上の RHEL イメージについて幅広いオファリングを提供しています。 これらのイメージは、従量課金制 (PAYG) と Bring-Your-Own-Subscription (BYOS) の 2 つの異なるライセンス モデルを通じて利用できます。 Azure 上の新しい RHEL イメージは、新しい RHEL バージョンがリリースされたとき、および必要に応じてライフサイクル中に更新されたときに公開されます。

### <a name="pay-as-you-go-payg-images"></a>従量課金制 (PAYG) イメージ
Azure には、さまざまな RHEL PAYG イメージが用意されています。 これらのイメージは、RHEL の適切なエンタイトルメントを伴い、更新プログラムのソース (Red Hat Update Infrastructure) にアタッチされています。 これらのイメージでは、RHEL のエンタイトルメントと更新に対してプレミアム料金が請求されます。 RHEL PAYG イメージのバリエーションは次のとおりです。
* 標準 RHEL
* RHEL for SAP
* 高可用性および更新サービスを備えた RHEL for SAP。

適切な数のサブスクリプションに対する個別の支払いに悩みたくない場合は、PAYG イメージを使用することをお勧めします。

### <a name="bring-your-own-subscription-byos-images"></a>Bring-Your-Own-Subscription (BYOS) イメージ
Azure では、RHEL BYOS イメージも用意されています。 これらのイメージは、既存の Red Hat サブスクリプションを所有していて、Azure でそれらを使用する必要があるお客様に役立ちます。 既存のサブスクリプションを Azure で使用するには、事前に Cloud Access サブスクリプションに変換する必要があります。 これらのイメージへのアクセスは、お客様が十分な Cloud Access サブスクリプションを保有していることが Red Hat によって確認された場合にのみ許可されます。 これらのイメージを使用することで、お客様は、PAYG イメージを使用した場合に発生する可能性のある二重請求を回避できます。 BYOS イメージへのアクセスは、[こちら](https://aka.ms/rhel-byos)から要求できます。

> [!NOTE]
> 二重請求に関する注意:RHEL サブスクリプションに対してユーザーが 2 回支払いを行うと、二重請求が発生します。 これは通常、お客様が subscription-manager を使用して RHEL PAYG VM のエンタイトルメントをアタッチしている場合に発生します。 たとえば、subscription-manager を使用して RHEL PAYG イメージ上の SAP パッケージのエンタイトルメントをアタッチしているお客様は、RHEL の料金を 2 回 (PAYG プレミアム料金を通じて 1 回、SAP サブスクリプションを通じて 1 回) 支払うため、間接的に二重請求されます。 これは、BYOS イメージのユーザーには発生しません。

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat Update Infrastructure (RHUI)
Azure では、PAYG RHEL 仮想マシン (VM) に対してのみ Red Hat Update Infrastructure を提供します。 RHUI は、実質的には Red Hat CDN のミラーですが、Azure PAYG RHEL VM からのみアクセスできます。 デプロイした RHEL イメージに応じて、適切なパッケージにアクセスできます。 たとえば、RHEL for SAP イメージでは、ベース RHEL パッケージに加えて SAP パッケージにもアクセスできます。

### <a name="rhui-update-behavior"></a>RHUI の更新動作
RHUI に接続されている RHEL イメージは、既定で、`yum update` の実行時に RHEL の最新のマイナー バージョンに更新されます。 この動作は、`yum update` 操作が実行された場合に RHEL 7.4 VM が RHEL 7.7 にアップグレードされる可能性があることを意味します。 これは RHUI の仕様です。 ただし、このアップグレード動作は、通常の RHEL リポジトリから [Extended Update Support (EUS) リポジトリ](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)に切り替えることで緩和できます。

## <a name="next-steps"></a>次のステップ
* [Azure 上の RHEL イメージ](./redhat-images.md)の詳細を確認します
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します
* [RHEL BYOS プラン](./redhat-byos.md)の詳細を確認します