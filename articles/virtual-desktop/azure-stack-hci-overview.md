---
title: Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) - Azure
description: Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) の簡単な概要。
author: Heidilohr
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5ba286a6023927d2c4d4821fb631a0df0b326b6c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704043"
---
# <a name="azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Stack HCI 用 Azure Virtual Desktop (プレビュー)

> [!IMPORTANT]
> Azure Stack HCI 用 Azure Virtual Desktop は現在プレビュー中です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) を使うと、オンプレミスの Azure Stack HCI インフラストラクチャで Azure Virtual Desktop セッション ホストをデプロイできます。 また、Azure Stack HCI 用 Azure Virtual Desktop を使用して、Azure portal からセッション ホストを管理することもできます。 既存のオンプレミス仮想デスクトップ インフラストラクチャ (VDI) のデプロイが既にある場合、Azure Stack HCI 用 Azure Virtual Desktop を使用すると、管理者とエンドユーザーのエクスペリエンスを向上させることができます。 クラウドで Azure Virtual Desktop を既に使用している場合は、パフォーマンスやデータの局所性のニーズを満たすために、オンプレミスのインフラストラクチャにデプロイを拡張できます。

Azure Stack HCI 用 Azure Virtual Desktop は現在パブリック プレビュー段階です。 Azure Stack HCI では現在、特定の重要な Azure Virtual Desktop 機能がサポートされていません。 これらの制限があるため、運用環境のワークロードでこの機能を使用することはまだお勧めしません。

## <a name="key-benefits"></a>主な利点

Azure Stack HCI 用 Azure Virtual Desktop の概要について説明しました。 しかし、これを使って何ができるのかという疑問が残っています。

Azure Stack HCI 用 Azure Virtual Desktop を使うと、次のことができます。

- Azure パブリック クラウドへの接続が不十分な地域の Azure Virtual Desktop ユーザーに、その場所に近いセッション ホストを提供してパフォーマンスを向上させる。

- アプリとユーザーのデータをオンプレミスに保持して、データの局所性の要件を満たす。  詳細については、「[Azure Virtual Desktop のデータの場所](data-locations.md)」を参照してください。

- 仮想デスクトップとアプリを同じ場所に保持して、従来のオンプレミス アプリとデータ ソースへのアクセスを向上させる。

- Windows 10 および Windows 11 Enterprise のマルチセッション仮想デスクトップを使用して、コストを削減し、ユーザー エクスペリエンスを向上させる。

- Azure portal を使用して、従来のオンプレミスの VDI ソリューションと比較して、VDI のデプロイと管理を簡素化する。

## <a name="pricing"></a>価格

Azure Stack HCI 用 Azure Virtual Desktop を実行するためにかかる費用は、次のものによる影響を受けます。
 - インフラストラクチャの費用。 Azure Stack HCI の月額サービス料金が請求されます。 詳細については、「[Azure Stack HCI の価格](https://azure.microsoft.com/pricing/details/azure-stack/hci/)」を参照してください。
 
- Azure Virtual Desktop のユーザー アクセス権。 クラウドの Azure Virtual Desktop へのアクセス権を付与するのと同じライセンスが Azure Stack HCI 用 Azure Virtual Desktop にも適用されます。 詳細については、「[Azure Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。

- Azure Virtual Desktop ハイブリッド サービス料金。 この料金は、Azure Stack HCI で実行している Azure Virtual Desktop セッション ホストのアクティブな仮想 CPU (vCPU) ごとに支払いが必要になります。 この料金は、プレビュー期間が終了するとアクティブになります。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

Azure Stack HCI 用 Azure Virtual Desktop のパブリック プレビュー バージョンに影響する次の問題が認識されています。

- Azure Stack HCI ホスト プールでは現在、Azure Virtual Desktop の次の機能がサポートされていません。
    
    - [Azure Monitor for Azure Virtual Desktop](azure-monitor.md)
    - [Azure Automation を使用したセッション ホストのスケーリング](set-up-scaling-script.md)
    - [自動スケーリング (プレビュー)](autoscale-scaling-plan.md)
    - [Start VM on Connect](start-virtual-machine-connect.md)
    - [マルチメディア リダイレクト (プレビュー)](multimedia-redirection.md)
    - [ユーザーごとのアクセス料金](./remote-app-streaming/licensing.md)

- Azure portal の [Azure Virtual Desktop] タブでは、Azure Stack HCI インフラストラクチャ上に新しい仮想マシンを直接作成することはできません。 代わりに、管理者はオンプレミスの仮想マシンを個別に作成し、それを Azure Virtual Desktop ホスト プールに登録する必要があります。

- Windows 10 または Windows 11 Enterprise のマルチセッション仮想デスクトップに接続すると、ユーザーに有効なライセンスがある場合でも、"Windows のライセンス認証" を促すデスクトップ ウォーターマークが表示されるなど、ライセンス認証の問題が発生することがあります。

- Azure Stack HCI 用 Azure Virtual Desktop では現在、クラウドとオンプレミスの両方のセッション ホストを含むホスト プールはサポートされていません。 デプロイの各ホスト プールには、1 種類のホスト プールのみが含まれている必要があります。

- Azure Stack HCI 上のセッション ホストでは、特定のクラウド専用の Azure サービスがサポートされていません。

- Azure Stack HCI では非常に多くの種類のハードウェアおよびオンプレミスのネットワーク機能がサポートされているため、Azure クラウドで実行されているセッション ホスト間でパフォーマンスとユーザー密度が大きく異なる可能性があります。 Azure Virtual Desktop の[仮想マシンのサイズ設定ガイドライン](/windows-server/remote/remote-desktop-services/virtual-machine-recs)は広範であるため、初期のパフォーマンスの予測のみに使用してください。

この一覧にない問題がプレビュー中に発生した場合は、ぜひご報告ください。

## <a name="next-steps"></a>次のステップ

Azure Stack HCI 用 Azure Virtual Desktop について理解できたので、この機能をデプロイする方法について「[Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) をセットアップする](azure-stack-hci.md)」を参照してください。
