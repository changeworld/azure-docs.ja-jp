---
title: Azure Containers オファーを作成する | Azure Marketplace
description: Marketplace 用の新しいコンテナー オファーを発行する方法。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 25d678ad7b8c2b786239b294de873bc1d98fa5cb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270334"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Cloud パートナー ポータルで新しいコンテナー オファーを作成する

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure Container オファーを作成する](https://aka.ms/CreateContainerOffer)」の手順に従って、移行されたオファーを管理します。

この記事では、Azure Marketplace 用のコンテナー オファー エントリを作成して発行する方法について説明します。 各オファーは、Azure Marketplace では専用のエンティティとして表示され、1 つ以上の SKU に関連付けられます。  コンテナー オファーは、以下のグループの資産とサポート サービスで構成されます。

|  **資産グループ**   |  **説明**  |
|  ---------------   |  ---------------  |
|    SKU            |  オファーの展開可能な最小単位。 1 つのオファー (製品クラス) に、複数の SKU を関連付けることができます。 SKU を使用して、サポートされる機能および課金モデルを区別することができます。 |
|  マーケットプレース       | マーケティング、法律、およびリード管理の資産と仕様が含まれます。  <ul><li> マーケティング資産には、オファーの名前、説明、およびロゴが含まれます</li> <li> 法的資産には、プライバシー ポリシー、使用条件、その他の法的文書が含まれます</li>  <li> リード管理ポリシーにより、Azure Marketplace エンド ユーザー ポータルからリードを処理する方法を指定できます。</li> </ul> |
| サポート            | サポート連絡先およびポリシー情報が含まれます |


## <a name="new-offer-form"></a>[新しいプラン] フォーム 

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインし、左側のメニュー バーで **[+ 新しいオファー]** を選択します。 [New Offer]\(新しいオファー) メニューで **[コンテナー]** を選択して **[New Offer]\(新しいオファー)** フォームを表示し、新しいコンテナー オファーの資産を定義するプロセスを開始します。

![新しいオファーのコンテナー オプションを選択](./media/azure-container-offer.png)

## <a name="next-steps"></a>次のステップ

コンテナー オファーの種類に対応する **[New Offer]\(新しいオファー)** ページには、新しいオファーを作成するのに使用する一連のタブとフォーム フィールドが用意されています。 以下の各記事では、タブを使用して新しいコンテナー オファーの資産グループとサポート サービスを定義する方法が説明されています。

- [[プランの設定] タブ](./cpp-offer-settings-tab.md)
- [[SKU] タブ](./cpp-skus-tab.md)
- [[Marketplace] タブ](./cpp-marketplace-tab.md)
- [[サポート] タブ](./cpp-support-tab.md)
