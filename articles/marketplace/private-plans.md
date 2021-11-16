---
title: Microsoft コマーシャル マーケットプレースでのプライベート プラン
description: アプリとサービスの公開元向けの Microsoft コマーシャル マーケットプレース (Azure Marketplace) のプライベート プランです。
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.reviewer: amhindma
ms.date: 11/10/2021
ms.openlocfilehash: d8f2435fe78465ee40e3cf6011db14d6daf7c04f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324652"
---
# <a name="private-plans-in-the-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースでのプライベート プラン

公開元は、対象となる顧客にのみ表示されるようにプライベート プランを使用します。 この記事では、プライベート プランのオプションと利点について説明します。

> [!TIP]
> コマーシャル マーケットプレースでのプライベート プランの顧客のビューを表示するには、「[Azure Marketplace でのプライベート プラン](/marketplace/private-plans)」と「[Microsoft AppSource でのプライベート プラン](/marketplace/appsource-private-plans)」を参照してください。

## <a name="unlock-enterprise-deals-with-private-plans"></a>プライベート プランを使用してエンタープライズ取引を開放する

プライベート プランを使用すると、公開元は、企業が必要とする機能を備えたプライベートでカスタマイズされたソリューションを、ターゲット顧客に提供できます。

- "*価格交渉*" によって、公開元は、一般公開されているオファーとは異なる割引や公表されない価格設定を展開できます。
- "*プライベートな取引条件*" によって、公開元は、特定の顧客に合うように取引条件を調整できます。
- "*構成の特化*" によって、公開元は、各自の仮想マシン、Azure アプリケーション、サービスとしてのソフトウェア (SaaS) を個々の顧客のニーズに合うように調整できます。 このオプションにより、公開元はすべての顧客に公開する前に、新しい製品機能に事前にアクセスすることもできます。

公開元は、プライベート プランを利用して、一般に公開されるマーケットプレースのグローバルな利用可用性を活かすことができるうえに、取引と構成をカスタマイズするために必要な交渉を柔軟に行って、それらを提供できます。 企業は、期待どおりに購入と販売を行うことができるようになりました。

## <a name="create-private-plans"></a>プライベート プランを作成する

公開元は、新しいプラン (以前は SKU と呼ばれていました) を作成し、それらをプライベートに設定することによって、"*プランを含む新規または既存のオファー*" のプライベート版を簡単に作成できます。 各オファーには、最大 45 個のプライベート プランを含めることができます。

<!--- [Private SKUs]() --->

プライベート プランは次のオファーの種類で利用できます。

- Azure Virtual Machine
- Azure アプリケーション (ソリューション テンプレートまたはマネージド アプリケーションとして実装されます)
- マネージド サービス
- SaaS オファー

プライベート プランはオファーのコンポーネントであり、ターゲット顧客のみが表示し、購入することができます。 プライベート プランはターゲット顧客のみが表示し、購入することができます。 プライベート プランは、Azure Global と Azure Government の両方の顧客が使用できます。

プライベート プランでは、基本イメージを再利用でき、パブリック プランに対して既に発行されているオファーのメタデータも再利用できます。 公開元は、このオプションを使用して、パブリック オファーのプライベート版を複数作成でき、同じ基本イメージとオファーのメタデータを複数のバージョンで公開する必要はありません。 Azure 仮想マシンと Azure アプリケーションを提供するオファーに限り、プライベート プランとパブリック プランで基本イメージを共有しているときに、オファーの基本イメージを変更すると、その基本イメージを使用しているすべてのパブリックとプライベートの各プランに変更が反映されます。

"*プライベート プランのみが含まれている新しいオファー*" の場合、公開元は、他のオファーと同じようにオファーを作成でき、作成後にプランをプライベートとしてマークします。 プライベート プランのみが含まれているオファーは、そのオファーに関連付けられていない顧客が [Azure portal](https://azure.microsoft.com/features/azure-portal/) または [Microsoft AppSource](https://appsource.microsoft.com/) で検出したりアクセスしたりすることはできません。

>[!NOTE]
>プライベート プランのみを含むオファーは、パブリック Azure Marketplace には表示されません。

## <a name="target-customers-with-private-plans"></a>プライベート プランを使用して顧客をターゲットに指定する

新規と既存のプライベート プランの両方で、公開元は、サブスクリプション ID を使用してターゲット顧客を指定できます。 Azure アプリケーションとマネージド サービスのオファーの場合、公開元は、プライベート プランの利用可用性を個々の Azure サブスクリプション ID に制限できます。 SaaS オファーの場合、公開元は、Azure Active Directory テナント ID を関連付け、プライベート プランの利用可用性を制限できます。 仮想マシン オファーの場合、公開元は、Azure テナント ID、サブスクリプション ID、または両方を使用し、プライベート プランにアクセスできる対象ユーザーを割り当てできます。 これらのオファーの種類の場合、最大 10 の ID を手動で追加するか、10 を超える ID が必要な場合は CSV スプレッドシートをインポートできます。

オファーが認定され、公開されたら、プライベート ユーザーの同期機能を使用することで、プランの顧客を更新したり、削除したりできます。 この機能により、公開元は、オファーを再び認定したり公開したりせずに、プライベート プランを提示する顧客の一覧を迅速かつ簡単に更新できます。

## <a name="deploying-a-private-plan"></a>プライベート プランをデプロイする

Azure portal にサインインすると、顧客は次の手順に従ってプライベート プランを選択できます。

1. [Azure portal](https://ms.portal.azure.com/) にサインインします。
1. **[Azure サービス]** で、 **[リソースの作成]** を選択します。
1. **[新規]** ページで、 **[Azure Marketplace]** の横にある **[すべて表示]** を選択します。 [Marketplace] ページが表示されます。
1. 左側のナビゲーションで **[Private products]\(プライベート製品\)** を選択します。

> [!NOTE]
> プライベート プランは [Azure portal](https://azure.microsoft.com/features/azure-portal/) または [Microsoft AppSource](https://appsource.microsoft.com/) でのみ検出できます。 [Azure Marketplace](https://azuremarketplace.microsoft.com) には表示されません。 さまざまな商業マーケットプレースのオンライン ストアへの公開に関する詳細については、「[登録情報オプションの概要](./determine-your-listing-type.md)」を参照してください。

プライベート プランは検索結果にも表示され、他のオファーと同じように、コマンド ラインと Azure Resource Manager テンプレートを使用してデプロイできます。

[![[検索結果にプライベート オファーが表示されています。]](media/marketplace-publishers-guide/private-product.png)](media/marketplace-publishers-guide/private-product.png#lightbox)

>[!Note]
>プライベート プランは、クラウド ソリューション プロバイダー (CSP) プログラムのリセラーを通じて確立されたサブスクリプションではサポートされていません。

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
