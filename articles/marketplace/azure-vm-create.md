---
title: Azure Marketplace で仮想マシン オファーを作成します。
description: Microsoft コマーシャル マーケットプレースで仮想マシン オファーを作成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 5964292d7037465456efe22283a5552a2bfc7ad8
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331262"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace で仮想マシン オファーを作成する方法

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com/) 向けに Azure 仮想マシン オファーを作成する方法について説明します。 オペレーティング システム、仮想ハード ディスク (VHD)、最大 16 個のデータ ディスクが含まれる Windows ベースと Linux ベースの両方の仮想マシンに対応しています。

開始する前に、[パートナー センターでコマーシャル マーケットプレース アカウントを作成します](partner-center-portal/create-account.md)。 ご自分のアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="before-you-begin"></a>開始する前に

まだ行っていない場合は、[仮想マシン オファーのプラン](marketplace-virtual-machines.md)を確認してください。 仮想マシンの技術的な要件、およびオファーを作成するときに必要となる情報と資産について説明されています。 

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のペインで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. **[概要]** ページで、 **[新しいオファー]**  >  **[Azure 仮想マシン]** の順に選択します。

    ![左側のペインのメニュー オプションと [新しいオファー] ボタンを示すスクリーンショット。](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> ご自分のオファーを発行した後にパートナー センターで行った編集は、そのオファーを再発行しないと Azure Marketplace に反映されません。 変更後は常にオファーを再発行するようにしてください。

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、Azure Marketplace オファーの Web アドレスと、Azure PowerShell および Azure CLI (該当する場合) で、顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「 **test-offer-1** 」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 オファーのエイリアスは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。

## <a name="enable-a-test-drive-optional"></a>体験版を有効にする (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「[体験版とは](partner-center-portal/test-drive.md)」を参照してください。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

**体験を有効にするには**
1.  **[体験版]** で、 **[体験版を有効にする]** チェック ボックスをオンにします。
1.  表示される一覧から、体験版の種類を選択します。

## <a name="configure-lead-management"></a>リード管理の構成

パートナー センターを使用してオファーをコマーシャル マーケットプレースに発行する場合、それを顧客関係管理 (CRM) システムに接続します。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 体験版を有効にする場合 (前のセクションを参照してください)、CRM への接続は必須です。 そうでない場合は、CRM への接続は任意です。

1. **[潜在顧客]** で、 **[接続]** リンクを選択します。
1. **[接続の詳細]** ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
1. 表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

   - [リードを Azure テーブルに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
   - [潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [リードを Marketo に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [リードを Salesforce に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 指定した構成を検証するには、 **[検証]** リンクを選択します。
1. ダイアログ ボックスを閉じるには、 **[OK]** を選択します。

## <a name="resell-through-csps"></a>CSP を通して再販する

[クラウド ソリューション プロバイダー (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) プログラムでパートナーがオファーを利用できるようにすることで、オファーのリーチを拡大します。 すべてのライセンス持ち込み (BYOL) プランは、自動的にこのプログラムにオプトインされます。 BYOL 以外のプランをオプトインすることもできます。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="next-steps"></a>次の手順

- [仮想マシン オファーのプロパティの構成](azure-vm-create-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)