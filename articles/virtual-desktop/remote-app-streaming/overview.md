---
title: Azure Virtual Desktop リモート アプリ ストリーミングとは - Azure
description: Azure Virtual Desktop リモート アプリ ストリーミングの概要。
author: Heidilohr
ms.topic: overview
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c13996fd5c8373ebe0897fa9caa57a2d94600c1b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399525"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>Azure Virtual Desktop リモート アプリ ストリーミングとは

Azure Virtual Desktop は、クラウド上で実行されるデスクトップおよびアプリ仮想化サービスであり、いつでもどこからでもリモート デスクトップにアクセスできます。 さらに、Azure Virtual Desktop をサービスとしてのプラットフォーム (PaaS) として使用して、組織のアプリをサービスとしてのソフトウェア (SaaS) として顧客に提供することもでき、このことはあまり知られていません。 Azure Virtual Desktop リモート アプリ ストリーミングを使用すると、Azure Virtual Desktop を使用して、仮想マシンを介してセキュリティで保護されたネットワーク経由で顧客にアプリを配信できます。

Azure Virtual Desktop に慣れていない (またはアプリの仮想化全般に慣れていない) 方のために、デプロイを開始して実行するのに役立つリソースをここにいくつか収集しました。

## <a name="requirements"></a>必要条件

開始する前に、Azure Virtual Desktop を実行するシステム要件の詳細な一覧について、[Azure Virtual Desktop の概要](../overview.md)に関するページを参照することをお勧めします。 このページから、Azure Virtual Desktop について IT の観点からサービスの詳細を記したドキュメントを参照できます。ほとんどの記事は、Azure Virtual Desktop のリモート アプリ ストリーミングにも適用されます。 基本を理解したら、リモート アプリ ストリーミングのドキュメントをより効果的に使用できます。

組織外の顧客が利用できるカスタム アプリ用に Azure Virtual Desktop デプロイを設定するには、次のことが必要です。

- カスタム アプリ。 Azure Virtual Desktop でサポートされるアプリの種類と、それらを顧客に提供する方法については、[Azure Virtual Desktop でカスタム アプリをホストする方法](custom-apps.md)に関する記事を参照してください。

- ドメイン参加資格情報。 Azure Virtual Desktop と互換性のある ID 管理システムをまだお持ちでない場合は、ホスト プールの ID 管理を設定する必要があります。 詳細については、「[マネージド ID を設定する](identities.md)」を参照してください。

- Azure サブスクリプション。 まだサブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="get-started"></a>作業の開始

これで準備ができたので、Azure Virtual Desktop のデプロイを設定する方法を見てみましょう。 成功できるように設定するための、2 つのオプションがあります。 手動または自動でデプロイを設定できます。 次の 2 つのセクションでは、これら 2 つの方法の違いについて説明します。

### <a name="set-up-azure-virtual-desktop-manually"></a>Azure Virtual Desktop の手動設定

デプロイは、次のチュートリアルに従って手動で設定できます。

1. [Azure portal を使用してホスト プールを作成する](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [アプリ グループを管理する](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [サービスの更新プログラムを検証するためのホスト プールを作成する](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [サービス アラートを設定する](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

### <a name="set-up-azure-virtual-desktop-automatically"></a>Azure Virtual Desktop の自動設定

自動プロセスを使用する場合は、作業の開始機能を使用して、デプロイを設定することができます。 詳細については、次の記事を参照してください。

- 「[作業の開始機能を使用して Azure Virtual Desktop をデプロイする](../getting-started-feature.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)」 (これらの手順に従うときは、「[Azure AD DS または AD DS が使用されているサブスクリプションの場合](../getting-started-feature.md#for-subscriptions-with-azure-ad-ds-or-ad-ds)」の手順に従ってください。 この方法を使用すると、ID 管理とアプリの互換性が向上し、ID 関連のインフラストラクチャ コストを微調整することもできます。 まだ Azure AD DS または AD DS がないサブスクリプション向けの方法では、これらの利点は得られません)。
- [作業の開始機能のトラブルシューティング](../troubleshoot-getting-started.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>Azure Virtual Desktop のカスタマイズと管理

Azure Virtual Desktop の設定後は、組織または顧客のニーズに合わせてデプロイをカスタマイズするために、多くの選択肢があります。 操作を開始するために次の記事が役立ちます。

- [Azure Virtual Desktop を使用してカスタム アプリをホストする方法](custom-apps.md)
- [ユーザーごとのアクセス価格でサブスクリプションを登録する](per-user-access-pricing.md)
- [Microsoft Azure Active Directory を使用する方法](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Intune での Windows 10 仮想マシンの使用](/mem/intune/fundamentals/windows-10-virtual-machines)
- [MSIX アプリ アタッチを使用してアプリをデプロイする方法](msix-app-attach.md)
- [Azure Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [事業継続とディザスター リカバリー プランを設定する](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Automation を使用してセッション ホストをスケーリングする](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [ユニバーサル印刷を設定する](/universal-print/fundamentals/universal-print-getting-started)
- [Start VM on Connect 機能を設定する](../start-virtual-machine-connect.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Virtual Desktop リソースにタグを付けてコストを管理する](../tag-virtual-desktop-resources.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>Azure Virtual Desktop のデプロイについての理解を深める

Azure Virtual Desktop デプロイの作成と管理に不可欠な概念を理解するには、次の記事を参照してください。

- [ライセンスとユーザーごとのアクセス価格について](licensing.md)
- [組織間アプリのセキュリティ ガイドライン](security.md)
- [Azure Virtual Desktop のセキュリティに関するベスト プラクティス](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Monitor for Azure Virtual Desktop の用語集](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [エンタープライズ向け Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [デプロイ コストの合計を見積もる](total-costs.md)
- [ユーザーごとのアプリ ストリーミング コストを見積もる](streaming-costs.md)
- [アーキテクチャに関する推奨事項](architecture-recs.md)
- [Start VM on Connect のよくあるご質問](../start-virtual-machine-connect-faq.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="next-steps"></a>次のステップ

デプロイの手動設定を開始する準備ができたら、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure portal を使用してホスト プールを作成する](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
