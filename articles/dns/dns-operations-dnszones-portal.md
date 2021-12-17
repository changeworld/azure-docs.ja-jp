---
title: Azure DNS での DNS ゾーンの管理 - Azure Portal | Microsoft Docs
description: Azure Portal を使って DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 58118480c16f2e318bab7435a79e27629880acc5
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203213"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure Portal で DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [ポータル](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure クラシック CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

この記事では、Azure Portal を使って DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-operations-dnszones-cli.md) または Azure [PowerShell](dns-operations-dnszones.md) を使って、管理することもできます。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 画面の左上で、 **[リソースの作成]** を選択し、**DNS ゾーン** を検索します。 **[作成]** を選択します。

    :::image type="content" source="./media/dns-operations-dnszones-portal/search-dns-zone.png" alt-text="DNS ゾーンのリソース検索の作成のスクリーンショット。":::

1. **[DNS ゾーンの作成]** ページで次の値を入力してから、 **[作成]** を選択します。

    | 設定 | 詳細 |
    | --- | --- |
    | **サブスクリプション** | DNS ゾーンを作成するサブスクリプションを選択します。|
    | **リソース グループ** | 新しいリソース グループを選択または作成します。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
    | **名前** | DNS ゾーンの名前を入力します。 たとえば、「**contoso.com**」と入力します。 |
    | **場所** | リソース グループの場所を選択します。 以前に作成したリソース グループを使用する場合は、その場所が既に選択されています。  |

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

Azure portal の上部にある検索バーで、**DNS ゾーン** を検索します。 各 DNS ゾーンはその独自のリソースです。 レコード セットの数やネーム サーバーなどの情報をこのページから表示できます。 **[ネーム サーバー]** 列は、既定のビューに含まれていません。 それを追加するには、 **[マネージド ビュー] > [列の編集] > [+ 列の追加]** の順に選択し、ドロップダウンから **[ネーム サーバー]** を選択します。 **[保存]** を選択して新しい列を適用します。

:::image type="content" source="./media/dns-operations-dnszones-portal/list-zones.png" alt-text="DNS ゾーン一覧ページのスクリーンショット。":::

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

Portal で DNS ゾーンに移動します。 選択した **DNS ゾーン** 概要ページで、 **[ゾーンの削除]** を選択します。 次に、DNS ゾーンを削除することを確認するように求められます。 DNS ゾーンを削除すると、そのゾーンに含まれているレコードもすべて削除されます。

:::image type="content" source="./media/dns-operations-dnszones-portal/delete-zone.png" alt-text="概要ページの [DNS ゾーンの削除] ボタンのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

「[Azure Portal で Azure DNS の使用を開始する](dns-getstarted-portal.md)」で、DNS ゾーンとレコードの使用方法を学習してください。
