---
title: Azure Marketplace で仮想マシン オファーを作成します。
description: Azure Marketplace で仮想マシン オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/08/2021
ms.openlocfilehash: 6565fff19d68b42aa102431c6e1afe7bb4b3b4d7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601068"
---
# <a name="create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace で仮想マシン オファーを作成する

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com/) 向けに Azure 仮想マシン オファーを作成する方法について説明します。 オペレーティング システム、仮想ハード ディスク (VHD)、最大 16 個のデータ ディスクが含まれる Windows ベースと Linux ベースの両方の仮想マシンに対応しています。

開始する前に、[パートナー センターでコマーシャル マーケットプレース アカウントを作成します](create-account.md)。 ご自分のアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="before-you-begin"></a>開始する前に

まだ行っていない場合は、[仮想マシン オファーのプラン](marketplace-virtual-machines.md)を確認してください。 仮想マシンの技術的な要件、およびオファーを作成するときに必要となる情報と資産について説明されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のペインで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. **[概要]** ページで、 **[+ 新しいオファー]** 、 **[Azure 仮想マシン]** の順に選択します。

    ![左側のペインのメニュー オプションと [新しいオファー] ボタンを示すスクリーンショット。](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 変更後は常にオファーを再発行するようにしてください。

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、Azure Marketplace オファーの Web アドレスと、Azure PowerShell および Azure CLI (該当する場合) で、顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 オファーのエイリアスは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。

**[作成]** を選択してオファーを生成し、続行します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="test-drive-optional"></a>体験版 (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「[体験版とは](./what-is-test-drive.md)」を参照してください。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

体験版を有効にするには、 **[体験版を有効にする]** チェック ボックスをオンにします。 体験版は後で構成します。 体験版では CRM を構成する必要があります (次のセクションを参照してください)。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次の手順

- [仮想マシン オファーのプロパティの構成](azure-vm-create-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
