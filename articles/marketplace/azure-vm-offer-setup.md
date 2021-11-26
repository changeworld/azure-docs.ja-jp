---
title: Azure Marketplace で仮想マシン オファーを作成します。
description: Azure Marketplace で仮想マシン オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2021
ms.openlocfilehash: 9c6ec7350358b59e108e5fc26426d7623b0a716c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725401"
---
# <a name="create-a-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace で仮想マシン オファーを作成する

この記事では、<bpt id="p1">[</bpt>Azure Marketplace<ept id="p1">](https://azuremarketplace.microsoft.com/)</ept> 向けに Azure 仮想マシン オファーを作成する方法について説明します。 オペレーティング システム、仮想ハード ディスク (VHD)、最大 16 個のデータ ディスクが含まれる Windows ベースと Linux ベースの両方の仮想マシンに対応しています。

開始する前に、<bpt id="p1">[</bpt>パートナー センターでコマーシャル マーケットプレース アカウントを作成します<ept id="p1">](create-account.md)</ept>。 ご自分のアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="before-you-begin"></a>開始する前に

まだ行っていない場合は、<bpt id="p1">[</bpt>仮想マシン オファーのプラン<ept id="p1">](marketplace-virtual-machines.md)</ept>を確認してください。 仮想マシンの技術的な要件、およびオファーを作成するときに必要となる情報と資産について説明されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のプラン\) タイルの画像。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のプラン\) ページで、 **[+ 新しいプラン]**  >  **[Azure Virtual Machine]** を選択します。

    [ ![左ペイン メニューのオプションと [新しいプラン] ボタンを示すスクリーンショット。](./media/create-vm/new-offer-azure-virtual-machine-workspaces.png) ](./media/create-vm/new-offer-azure-virtual-machine-workspaces.png#lightbox)

> [!NOTE]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 変更後は常にオファーを再発行するようにしてください。

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、Azure Marketplace オファーの Web アドレスと、Azure PowerShell および Azure CLI (該当する場合) で、顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 オファーのエイリアスは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。

<bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択してオファーを生成し、続行します。 パートナー センターで <bpt id="p1">**</bpt>[オファーのセットアップ]<ept id="p1">**</ept> ページが開きます。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のペインで、 <bpt id="p1">**</bpt>[コマーシャル マーケットプレース]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> の順に選択します。
3. <bpt id="p1">**</bpt>[概要]<ept id="p1">**</ept> ページで、 <bpt id="p2">**</bpt>[+ 新しいオファー]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[Azure 仮想マシン]<ept id="p3">**</ept> の順に選択します。

    ![左側のペインのメニュー オプションと [新しいオファー] ボタンを示すスクリーンショット。](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 変更後は常にオファーを再発行するようにしてください。

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、Azure Marketplace オファーの Web アドレスと、Azure PowerShell および Azure CLI (該当する場合) で、顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 オファーのエイリアスは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。

<bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択してオファーを生成し、続行します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

---

## <a name="test-drive-optional"></a>体験版 (省略可能)

[!INCLUDE [Test drives section](includes/test-drives.md)]

VM オファーの場合は、Azure Resource Manager (ARM) デプロイが唯一の使用可能な体験版オプションです。 デプロイ テンプレートには、ソリューションを構成しているすべての Azure リソースが含まれている必要があります。

体験版を有効にするには、 **[体験版を有効にする]** チェックボックスをオンにします。これにより、左側のナビゲーション メニューで [体験版] タブが有効になります。 後で「[VM 体験版を構成する](azure-vm-test-drive.md)」で、そのタブを使用して、体験版のリストを構成して作成します。

体験版では、潜在顧客用の CRM を構成する必要があります (次のセクションを参照)。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Customer leads](includes/customer-leads.md)]

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次の手順

- <bpt id="p1">[</bpt>仮想マシン オファーのプロパティの構成<ept id="p1">](azure-vm-create-properties.md)</ept>
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
