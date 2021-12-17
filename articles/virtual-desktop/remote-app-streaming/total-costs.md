---
title: Azure Virtual Desktop のデプロイ コストの合計について - Azure
description: Azure Virtual Desktop デプロイの総コストを見積もる方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3daabe070d0745c95bc16582262897da9f4c5aae
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399506"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>Azure Virtual Desktop のデプロイ コストの合計について

Azure Virtual Desktop のコストは、基になる Azure リソースの消費とライセンスという 2 つのソースから取得されます。 Azure Virtual Desktop のコストは、デプロイ リソースにアクセスするエンドユーザーではなく、Azure Virtual Desktop デプロイを所有する組織に課金されます。 一部のライセンス料金は前払いされている必要があります。 その他のライセンスと基になるリソース消費料金は、使用状況を追跡するメーターに基づきます。

このアーティクルでは、Azure Pricing Calculator を使用して Azure Virtual Desktop をデプロイする前に、使用量とライセンスのコスト、およびサービス コストを見積もる方法について説明します。 この記事では、Azure Virtual Desktop をデプロイした後に Azure Cost Management を使用してコストを表示する方法についても説明します。

>[!NOTE]
>Azure Virtual Desktop のデプロイに支払いを行うお客様は、デプロイの有効期間のリソース管理とコストの処理を担当します。 所有者が Azure Virtual Desktop デプロイに接続されたリソースを必要としなくなった場合は、それらのリソースが適切に削除されていることを確認する必要があります。 詳細については、[Microsoft Azure portal を使用した Azure リソースの管理](../../azure-resource-manager/management/manage-resources-portal.md)を参照してください。

## <a name="consumption-costs"></a>従量課金コスト

従量課金コストは、Azure Virtual Desktop ホスト プールにアクセスしているユーザーのすべての Azure リソース使用料金の合計です。 これらの料金は、ホスト プール内のセッション ホスト仮想マシン (VM) から取得されます。これには、Azure の他の製品で共有されているリソースや、Active Directory Domain Services (AD DS) のドメイン コントローラーなど、サービスを利用するために追加のインフラを実行する必要がある ID 管理システムが含まれます。

### <a name="session-host-vm-costs"></a>セッションホスト VM のコスト

Azure Virtual Desktop では、セッション ホスト VM は次の 3 つの Azure サービスを使用します。

- 仮想マシン (コンピューティング)
- マネージド ディスクの Storage (VM ごとの OS ストレージと個人用デスクトップのすべてのデータ ディスクを含む)
- 帯域幅 (ネットワーク)

これらの料金は、ホスト プール固有のリソース (セッションホスト VM を含む) が割り当てられている Azure Resource Group レベルで確認できます。 1 つ以上のホスト プールが、有料の Log Analytics サービスを使用してオプションの Azure Virtual Desktop Insights 機能に VM データを送信するようにも構成されている場合、対応する Azure Resource Group の Log Analytics に対して課金されます。 詳細については、[Monitor Windows Virtual Desktop のコストと価格の見積もり](../azure-monitor-costs.md)を参照してください。

このセクションの冒頭に記載されている 3 つのプライマリ VM セッション ホストの使用コストのうち、通常はコンピューティング コストが最も高くなります。 コンピューティング コストを軽減し、可用性を使用してリソース需要を最適化するために、多くのお客様は[セッション ホストを自動的に拡張する](../set-up-scaling-script.md)ことを選択します。

### <a name="domain-controller-costs-for-active-directories"></a>Active Directory ドメイン コントローラーのコスト

ドメインコントローラー VM は、少なくとも次の 4 つの Azure サービスを使用します。

- 仮想マシン (コンピューティング)
- マネージド ディスクの Storage (VM ごとの OS ストレージと個人用デスクトップのすべてのデータ ディスクを含む)
- 帯域幅 (ネットワーク)
- 仮想ネットワーク

Azure Virtual Desktop のデプロイが Active Directory を実行するためにドメイン コントローラーに依存している場合は、それを Azure Virtual Desktop のデプロイ コストの合計に含める必要があります。 標準的な Azure VM は、Active Directory の ID も利用可能にしておく必要があるため、Azure でホストされるドメイン コントローラーは、[セッションホスト VM のコスト](#session-host-vm-costs)で説明したセッション ホスト VM 用の 3 つの Azure サービスも共有することになります。

ただし、ドメイン コントローラーは、セッション ホスト VM とはいくつかの重要な違いがあります。

- ドメイン コントローラーは、デプロイの外部にある他のサービスと通信する必要があるため、追加の仮想ネットワーク料金が発生します。
- ドメイン コントローラーの可用性を拡張すると問題が発生します。なぜなら、デプロイではドメイン コントローラーが常に利用可能である必要があるからです。

### <a name="shared-service-costs"></a>共有サービスのコスト

Azure Virtual Desktop のデプロイで使用されている機能によっては、次のオプション機能の組み合わせでも Azure ストレージの料金が必要になる場合もあります。

- [MSIX アプリのアタッチ](../what-is-app-attach.md)
- [カスタム OS イメージ](../set-up-customize-master-image.md)
- [FSLogix プロファイル](../fslogix-containers-azure-files.md)

これらの機能では [Azure Files](../../storage/files/storage-files-introduction.md) や [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-introduction.md)などの Azure ストレージ オプションが使用されるため、Azure Virtual Desktop 以外の他の Azure サービスとストレージを共有できます。 Azure Virtual Desktop 機能のために購入したストレージについては、そのコストと使用している他の Azure サービスのコストとの違いを明確にするために、別のストレージ アカウントを作成することを推奨します。

### <a name="user-access-costs"></a>ユーザー アクセス コスト

Azure Virtual Desktop のデプロイでアプリまたはデスクトップに接続するユーザーごとに、ユーザーのアクセス コストが毎月請求されます。 Azure Virtual Desktop のユーザー単位のアクセス料金の詳細については、[ライセンスとユーザー単位のアクセス料金について](licensing.md)を参照してください。

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>Azure Virtual Desktop をデプロイする前にコストを予測する

基本について理解したところで、見積もりを始めましょう。 そのためには、消費コストとユーザー アクセス コストの両方を見積もる必要があります。

### <a name="predicting-consumption-costs"></a>従量課金コストの予測

[Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) を使用すると、デプロイを作成する前に Azure Virtual Desktop の従量課金コストを見積もることができます。 従量課金コストを予測する方法を次に示します。

1. 料金計算ツールで、 **[コンピューティング]** タブを選択して、Azure Pricing Calculator のコンピューティング オプションを表示します。

2. **[Azure Virtual Desktop]** を選択します。 Azure Virtual Desktop の計算モジュールが表示されます。

3. デプロイの値をフィールドに入力して、予想されるコンピューティング、ストレージ、およびネットワークの使用率に基づいて、毎月の Azure の請求額を見積もります。

>[!NOTE]
>現時点では、Azure Pricing Calculator の Azure Virtual Desktop モジュールは、セッション ホスト VM の消費コストと、ストレージを必要とするオプションの Azure Virtual Desktop 機能の追加ストレージの総量のみを見積もることができます (デプロイを選択した場合)。 ただし、他の Azure Virtual Desktop 機能に対する見積もりを同じ Azure Pricing Calculator ページ内の個別のモジュールに追加すると、より完全またはモジュール式のコストを見積もることができます。
>
>Azure Pricing Calculator モジュールを追加して、デプロイの他のコンポーネントのコストへの影響を見積もることができます。これには以下が含まれます。
>
>- ドメイン コントローラー
>- その他のストレージに依存する機能 (カスタム OS イメージ、MSIX アプリのアタッチ、Azure Monitor など)

### <a name="predicting-user-access-costs"></a>ユーザー アクセス コストの予測

ユーザー アクセス コストは、毎月デプロイに接続するユーザーの数によって異なります。 予想されるユーザー アクセスの合計コストを見積もる方法については、[Azure Virtual Desktop のユーザーごとのアプリケーション ストリーミング コストを見積もる](streaming-costs.md)を参照してください。

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>Azure Virtual Desktop をデプロイした後のコスト表示

Azure Virtual Desktop をデプロイした後、[Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) を使用して請求書を表示できます。 このセクションでは、現在のサービスの価格を確認する方法について説明します。

### <a name="viewing-bills-for-consumption-costs"></a>従量課金コスト請求書の表示

適切な [AZURE RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md) アクセス許可を持つ組織内のユーザーは、課金管理者と同様に、[コスト分析ツール](../../cost-management-billing/costs/cost-analysis-common-uses.md)を使用して [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) で Azure 課金請求書を検索し、Azure サブスクリプションまたはサブスクリプションでの月単位の Azure Virtual Desktop 従量課金コストを追跡できます。

### <a name="viewing-bills-for-user-access-costs"></a>ユーザー アクセス コストの請求を表示する

ユーザー アクセス コストは、登録されているサブスクリプションについて、Azure 課金請求書の各請求サイクルに、従量課金コストとその他の Azure 料金と共に表示されます。

## <a name="next-steps"></a>次の手順

デプロイに具体的にどのくらいのコストがかかるのかを知りたい方は、以下のアーティクルを参考にしてください。

- [ライセンスとユーザーごとのアクセス価格について](licensing.md)
- [Azure Virtual Desktop のユーザーごとのアプリ ストリーミング料金の見積もり](streaming-costs.md)
- [Azure Virtual Desktop リソースにタグを付けてコストを管理する](../tag-virtual-desktop-resources.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)