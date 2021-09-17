---
title: Azure portal を使用し、ゾーン指定された VM を作成する
description: Azure portal を使用して可用性ゾーン内に VM を作成する
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 5/10/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 59250d3e2ed69da44e1d5e9187a12c1abac69d25
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688846"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Azure portal を使用して可用性ゾーン内に仮想マシンを作成する

**適用対象:** :heavy_check_mark: Windows VM 

この記事では、Azure Portal を使用して Azure 可用性ゾーン内に仮想マシンを作成する手順を説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンを使用するには、[サポートされている Azure リージョン](../../availability-zones/az-region.md)に仮想マシンを作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

1. Azure Portal ( https://portal.azure.com ) にサインインします。

1. **[リソースの作成]**  >  **[コンピューティング]**  >  **[仮想マシン]** をクリックします。 

3. 仮想マシンの情報を入力します。 ユーザー名とパスワードが、仮想マシンへのサインインに使われます。 パスワードは 12 文字以上で、[定義された複雑さの要件](faq.yml#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。 

4. 米国東部 2 など、可用性ゾーンをサポートするリージョンを選びます。 

5. **[可用性オプション]** で、 **[可用性ゾーン]** ドロップダウンを選択します。 

1. **[可用性ゾーン]** で、ドロップダウン リストからゾーンを選択します。
        
4. VM のサイズを選択します。 推奨されるサイズを選ぶか、機能に基づいてフィルター処理します。 使うゾーンでサイズが使用可能であることを確認します。

6. VM に関する情報の入力を完了します。 完了したら、 **[確認および作成]** を選択します。

7. 情報が検証されたら、 **[作成]** を選択します。

1. VM が作成された後、VM のページの **[要点] セクション** で、一覧表示された可用性ゾーンを確認できます。

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>マネージド ディスクのゾーンと IP アドレスを確認する

VM が可用性ゾーンに展開されると、その VM のマネージド ディスクが同じ可用性ゾーンに作成されます。 既定では、パブリック IP アドレスもそのゾーンに作成されます。

これらのリソースに対するゾーンの設定は、ポータルで確認できます。  

1. 左側のメニューから **[ディスク]** を選択し、OS ディスクを選択します。 ディスクのページには、ディスクの場所と可用性ゾーンに関する詳細情報が含まれます。

1. VM のページに戻り、パブリック IP アドレスを選択します。 左側のメニューで **[プロパティ]** を選択します。 プロパティ ページには、パブリック IP アドレスの場所と可用性ゾーンに関する詳細情報が含まれます。

    
## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[可用性](../availability.md)の詳細を確認してください。
