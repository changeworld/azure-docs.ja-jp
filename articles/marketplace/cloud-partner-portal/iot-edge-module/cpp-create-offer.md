---
title: Azure IoT Edge モジュールのオファーを作成する | Azure Marketplace
description: Marketplace で新規 IoT Edge モジュールを公開する方法。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ab40a0e90bee8f034d811aac2781192359cbc109
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981751"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Cloud パートナー ポータルで新しい IoT Edge モジュールのオファーを作成する

>[!Important]
>2020 年 4 月 13 日以降、IoT Edge モジュールのパートナー センターへの移行管理が開始されます。 移行後は、パートナー センターでオファーを作成および管理します。 「[IoT Edge モジュール オファーを作成する](https://aka.ms/AzureCreateIoT)」の手順に従い、移行後のオファーを管理します。

この記事では、Azure Marketplace 向けの IoT Edge モジュール オファー エントリを作成して発行する方法について説明します。 各オファーは、Azure Marketplace では専用のエンティティとして表示され、1 つ以上の SKU に関連付けられます。  IoT Edge モジュールのオファーは、以下のグループの資産およびサポート サービスで構成されます。

|  **資産グループ**   |  **説明**  |
|  ---------------   |  ---------------  |
|    SKU            |  オファーの展開可能な最小単位。 1 つのオファー (製品クラス) に、複数の SKU を関連付けることができます。 SKU を使用して、サポートされる機能および課金モデルを区別することができます。 |
|  マーケットプレース       | マーケティング、法律、およびリード管理の資産と仕様が含まれます。  <ul><li> マーケティング資産には、オファーの名前、説明、およびロゴが含まれます</li> <li> 法的資産には、プライバシー ポリシー、使用条件、その他の法的文書が含まれます</li>  <li> リード管理ポリシーにより、Azure Marketplace エンド ユーザー ポータルからリードを処理する方法を指定できます。</li> </ul> |
| サポート            | サポート連絡先およびポリシー情報が含まれます |


## <a name="new-offer-form"></a>[新しいプラン] フォーム 

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインし、左側のメニュー バーで **[+ 新しいプラン]** を選択します。 [新しいプラン] メニューで、 **[IoT Edge モジュール]** を選択して **[新しいオファー]** フォームを表示し、新しい ioT Edge モジュール オファーの資産を定義するプロセスを開始します。 

![新しい IoT Edge モジュール オファーのユーザー インターフェイスの選択](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>次のステップ

IoT Edge モジュール オファーの種類に対する **[新しいプラン]** ページで提供されている一連のタブとフォーム フィールドを使用して、新しいオファーを作成できます。 以下の各記事では、タブを使用して新しい IoT Edge モジュール オファーの資産グループとサポート サービスを定義する方法が説明されています。

- [[プランの設定] タブ](./cpp-offer-settings-tab.md)
- [[SKU] タブ](./cpp-skus-tab.md)
- [[Marketplace] タブ](./cpp-marketplace-tab.md)
- [[サポート] タブ](./cpp-support-tab.md)
