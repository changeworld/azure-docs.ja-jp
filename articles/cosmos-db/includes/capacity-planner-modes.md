---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/21/2021
ms.author: sngun
ms.openlocfilehash: cebf848f673b9e2651e6598d64aefff0cd371553
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064734"
---
## <a name="capacity-planner-modes"></a>キャパシティ プランナーのモード


|**モード**  |**説明**  |
|---------|---------|
|Basic|迅速で大まかな RU/秒とコストの見積もりを提供します。 このモードは、インデックス作成ポリシー、一貫性、およびその他のパラメーターに対して既定の Azure Cosmos DB 設定を前提としています。 <br/><br/>Azure Cosmos DB で実行する可能性のあるワークロードを評価するときは、迅速で大まかな見積もりを得るために基本モードを使用します。 詳しくは、[基本モードでコストを見積もる](#basic-mode)方法に関するセクションをご覧ください。|
|上級|詳細な RU/s および費用の見積もりを確認できます。これは、インデックス作成ポリシーや整合性レベルなど、費用とスループットに影響するより多くのパラメーターを調整するのに役立ちます。 <br/><br/>新しいプロジェクトの RU/秒の見積もりを作成する場合や、より詳細な見積もりが必要な場合は、詳細モードを使用します。 詳しくは、[詳細モードでコストを見積もる](#advanced-mode)方法に関するセクションをご覧ください。|
