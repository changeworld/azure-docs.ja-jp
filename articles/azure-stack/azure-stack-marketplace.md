---
title: Azure Stack でのカスタム Marketplace アイテムの発行 (クラウド オペレーター) | Microsoft Docs
description: Azure Stack オペレーターとして、Azure Stack でカスタム Marketplace アイテムを発行する方法について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 7b1a6020fb8730aee7ed41d8c82358db0945e4ef
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257644"
---
# <a name="the-azure-stack-marketplace-overview"></a>Azure Stack Marketplace の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Marketplace は Azure Stack 向けにカスタマイズされたサービス、アプリケーション、リソースのコレクションです。 リソースには、ネットワーク、仮想マシン、ストレージなどが含まれます。 ユーザーはここで、新しいリソースを作成して、新しいアプリケーションをデプロイします。 ユーザーが使用する商品を参照し、選択できるショッピング カタログと考えてください。 ユーザーが Marketplace アイテムを使用するには、アイテムへのアクセスが許可されるサービスにサブスクライブする必要があります。

Azure Stack オペレーターとして、Marketplace に追加 (公開) するアイテムを決定します。 データベース、App Services などを公開できます。 公開すると、アイテムはすべてのユーザーに表示されます。 作成したカスタム アイテムを公開できます。 拡大を続ける [Azure Marketplace アイテムの一覧](azure-stack-marketplace-azure-items.md)からアイテムを公開することもできます。 Marketplace にアイテムを公開すると、5 分以内にユーザーがそれらを表示できます。

Marketplace を開くには、管理者コンソールで **[新規]** を選択します。

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace アイテム
Azure Stack Marketplace の項目は、ユーザーがダウンロードおよび使用できるサービス、アプリケーション、またはリソースです。 すべての Azure Stack Marketplace アイテムは、プランやサービスなどの管理アイテムを含め、すべてのユーザーに表示されます。 これらのアイテムを表示するためにサブスクリプションは必要ありませんが、ユーザーに対しては機能しません。

各 Marketplace アイテムには以下のものが含まれます。

* リソース プロビジョニングの Azure リソース マネージャー テンプレート
* 文字列、アイコン、その他のマーケティング資料などのメタデータ
* ポータルでアイテムを表示するための書式設定情報

Marketplace に公開されるすべてのアイテムは、Azure ギャラリー パッケージ (azpkg) 形式を使用します。 デプロイまたは実行時リソース (コード、ソフトウェアの zip ファイル、または仮想マシン イメージなど) を Marketplace アイテムの一部ではなく、Azure Stack に個別に追加します。 

Azure Stack バージョン 1803 以降では、Azure からダウンロードするときや、カスタム画像をアップロードするときに画像はスパース ファイルに変換されます。 このプロセスは画像を追加するときに時間がかかりますが、領域を節約し、画像の展開時間を短縮することができます。 変換は新しい画像にのみ適用されます。  既存の画像は変更されません。 

## <a name="next-steps"></a>次の手順
[Marketplace アイテムのダウンロード](azure-stack-download-azure-marketplace-item.md)  
[Marketplace アイテムの作成と発行](azure-stack-create-and-publish-marketplace-item.md)

