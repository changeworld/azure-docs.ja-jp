---
title: Network Function Manager に関する FAQ
titleSuffix: Azure Network Function Manager
description: Network Function Manager のよくあるご質問について説明します。
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5cf6b9bdc9982adf2da86956ce41e7cd5e7458ec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004942"
---
# <a name="azure-network-function-manager-faq"></a>Azure Network Function Manager FAQ

## <a name="faqs"></a>FAQ

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>当社は Network Function Manager パートナーです。ネットワーク機能マネージャーにオンボードする必要があります。 NFM で当社のネットワーク機能を提供するための操作方法を教えてください。

Microsoft の目標は、Azure Stack Edge で利用可能な中からお客様が選択するネットワーク機能の豊富なエコシステムをお客様に提供することです。 オンボードの要件については、 **aznfmpartner@microsoft.com** までメールでお問い合わせください。

### <a name="does-network-function-manager-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>Network Function Manager は、GPU 搭載の Azure Stack Edge Pro に加えて、他の Azure エッジ デバイスをサポートしますか。

現在、NFM は、一般に入手可能な GPU 搭載の Azure Stack Edge Pro でご利用いただけます。 Azure Stack Edge Pro は、モバイル パケット コアや SD-WAN エッジなどの特殊なネットワーク機能を実行するように設計された、サービスとしてのハードウェアです。 このデバイスは、ポート 5 および 6 でネットワーク アクセラレーションがサポートされている 6 つの物理ポートを搭載しています。 GPU 搭載の Azure Stack Edge Pro デバイスについては、「[ネットワーク インターフェイスの仕様](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications)」を参照してください。 ネットワーク機能パートナーは、SR-IOV および DPDK 機能を利用して、ネットワーク機能に優れたネットワーク パフォーマンスを実現できます。

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>GPU 搭載の Azure Stack Edge Pro では、ネットワーク機能の実行に加えて、どのような追加機能を使用できますか。

GPU 搭載の Azure Stack Edge (ASE) Pro と Azure Network Function Manager は、[Azure Private MEC](../private-multi-access-edge-compute-mec/index.yml) ソリューションに含まれています。 ASE デバイスでは、プライベート モバイル ネットワークと VM またはコンテナーベースのエッジ アプリケーションを実行できるようになりました。 これにより、重要なビジネス アプリケーションに対して予測可能な SLA を提供する革新的なソリューションを構築できます。 Azure Stack Edge Pro には、ビデオ推論や機械学習などのシナリオをエッジで利用できるようにする 1 つまたは 2 つの [GPU](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications) も装備されています。

### <a name="what-is-the-pricing-for-network-function-manager"></a>Network Function Manager の価格設定はどうなっていますか。

Azure Network Function Manager は、Azure Stack Edge Pro デバイスで追加料金なしで提供されます。 ネットワーク機能パートナーは、NFM サービスを使用してネットワーク機能を提供するために、別途料金を設ける可能性があります。 料金の詳細については、ネットワーク機能パートナーにお問い合わせください。

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>Azure Stack Edge Pro デバイスが切断モードまたは部分的に接続されたモードである場合、既にデプロイされているネットワーク機能は動作しなくなりますか。

Network Function Manager サービスでは、ネットワーク機能の作成または削除、デバイスで実行されているネットワーク機能の監視およびトラブルシューティングを行う目的での管理操作のために、ASE デバイスへのネットワーク接続が必要です。 ネットワーク機能が ASE デバイスにデプロイされていて、デバイスが切断されているか、基になるネットワーク機能が部分的に接続されている場合、仮想マシンは中断されずに動作を継続するはずです。 これらの仮想マシンに展開されたネットワーク機能には、構成管理に基づいて異なる要件がある可能性があり、ネットワーク機能パートナーからの追加のネットワーク接続要件がある場合があります。 ネットワーク接続の要件と操作モードについては、パートナーにお問い合わせください。

### <a name="which-regions-are-supported-for-nfm-will-you-add-support-for-additional-azure-regions"></a>NFM はどのリージョンでサポートされますか。 さらに Azure リージョンのサポートを追加する予定はありますか。

Network Function Manager は、次のリージョンで使用できます。

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

GPU 搭載の Azure Stack Edge Pro は、いくつかの国で入手可能であり、選択したネットワーク機能をデプロイして実行できます。 Azure Stack Edge Pro GPU デバイスが使用可能なすべての国またはリージョンの一覧については、[Azure Stack Edge Pro GPU の価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)に関するページにアクセスしてください。 **[Azure Stack Edge Pro]** タブの  **[可用性]**   セクションに記載されています。

Azure Stack Edge デバイスと Network Function Manager リソースは、規制およびデータ主権要件に基づいて登録できます。 Network Function Manager のリソースに関連付けられている Azure リージョンは、クラウド サービスから物理デバイスへの管理操作をガイドするために使用されます。

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>Azure Stack Edge で実行されているネットワーク機能のマネージド アプリケーションを削除すると、ネットワーク機能の課金は自動的に停止しますか。

Network Function Manager を使用してデプロイされたネットワーク機能の請求サイクルについては、Network Function Manager パートナーにお問い合わせください。 各パートナーは、パートナーのネットワーク機能オファリングについてそれぞれの課金ポリシーを持ちます。

## <a name="next-steps"></a>次のステップ

詳細については、[概要](overview.md)に関するページを参照してください。
