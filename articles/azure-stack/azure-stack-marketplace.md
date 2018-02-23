---
title: "Azure Stack でのカスタム Marketplace アイテムの発行 (クラウド オペレーター) | Microsoft Docs"
description: "Azure Stack オペレーターとして、Azure Stack でカスタム Marketplace アイテムを発行する方法について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c791708e11b7e9e8bbe046f06233d948d4632c90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure Stack Marketplace の概要

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

Marketplace は、ネットワーク、仮想マシン、ストレージなどの Azure Stack 向けにカスタマイズされたサービス、アプリケーション、リソースのコレクションです。 ユーザーはここで、新しいリソースを作成して、新しいアプリケーションをデプロイします。 ユーザーが使用する商品を参照し、選択できるショッピング カタログと考えてください。 ユーザーが Marketplace アイテムを使用するには、アイテムへのアクセスが許可されるサービスにサブスクライブする必要があります。

Azure Stack オペレーターとして、Marketplace に追加 (公開) するアイテムを決定します。 データベース、App Services などを公開できます。 これにより、すべてのユーザーに対してそれらが表示されます。 作成したカスタム アイテムを公開できます。 拡大を続ける [Azure Marketplace アイテムの一覧](azure-stack-marketplace-azure-items.md)からアイテムを公開することもできます。 Marketplace にアイテムを公開すると、5 分以内にユーザーがそれらを表示できます。

Marketplace を開くには、 **[新規]**をクリックします。

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace アイテム
Azure Stack Marketplace の項目は、ユーザーがダウンロードおよび使用できるサービス、アプリケーション、またはリソースです。 すべての Azure Stack Marketplace のアイテムは、すべてのユーザーに表示されます。

各 Marketplace アイテムには以下のものが含まれます。

* リソース プロビジョニングの Azure リソース マネージャー テンプレート
* 文字列、アイコン、その他のマーケティング資料などのメタデータ
* ポータルでアイテムを表示するための書式設定情報

Marketplace に発行されるすべてのアイテムでは、Azure ギャラリー パッケージ (azpkg) と呼ばれる形式を使用します。 デプロイまたは実行時リソース (コード、ソフトウェアの zip ファイル、または仮想マシン イメージなど) を Marketplace アイテムの一部ではなく、Azure Stack に個別に追加します。 

## <a name="next-steps"></a>次の手順
[Marketplace アイテムの作成と発行](azure-stack-create-and-publish-marketplace-item.md)

