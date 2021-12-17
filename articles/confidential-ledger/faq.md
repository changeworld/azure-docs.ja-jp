---
title: Azure Confidential Ledger のよくある質問
description: Azure Confidential Ledger のよくある質問
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2c5d6aab297fb357d94066a05dd7f6ff766b31df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458116"
---
# <a name="frequently-asked-questions-for-azure-confidential-ledger"></a>Azure Confidential Ledger のよくある質問

## <a name="how-can-i-tell-if-the-acc-ledger-service-would-be-useful-to-my-organization"></a>ACC Ledger サービスが自分の組織で役立つかどうか、どうすれば判断できますか。

Azure Confidential Ledger が最も役に立つのは、記録や保存処理を実行しているシステムに対して攻撃者が意欲的に侵入を試みる価値のある記録が存在する組織です。こうした攻撃には、以前の記録を捏造、改変、削除しようとする悪意のある従業員、すなわち "内部犯" によるものも含まれます。

## <a name="what-makes-acc-ledger-much-more-secure"></a>ACC Ledger の安全性が他のサービスよりも大幅に高いのはなぜですか。

名前が示すように、この Ledger では [Azure Confidential Computing プラットフォーム](../confidential-computing/index.yml)を使用します。 1 つの Ledger は 3 つ以上のまったく同じインスタンスにまたがって存在し、それらの各インスタンスは、ハードウェアにより実装され、信頼性を完全に保証された、専用のエンクレーブで実行されます。 Ledger の整合性はコンセンサス ベースのブロックチェーンによって保たれます。

## <a name="when-writing-to-the-acc-ledger-do-i-need-to-store-write-receipts"></a>ACC Ledger に書き込んだときは、書き込みのレシートを保存する必要がありますか。

必ずしもその必要はありません。 現在提供されているソリューションの中には、将来行うログの有効性確認のために、書き込みのレシートの保管をユーザーに求めるものがあります。 安全な保存設備でレシートを管理する必要がある場合、その分だけ負担が増えます。 Ledger では、マークル木を利用した手法によりこの問題を解消します。この手法では、署名済みの信頼の基点 (root-of-trust) へのツリー内の完全な経路が、書き込みのレシートに記載されます。 ユーザーは Ledger のデータを一切保存、管理せずに、トランザクションの有効性を確認できます。

## <a name="how-do-i-verify-ledgers-authenticity"></a>Ledger の信頼性はどうすれば確認できますか。

クライアントの通信先である Ledger サーバー ノードに信頼性があることを確認できます。 詳しくは、[Confidential Ledger Nodes の認証](authenticate-ledger-nodes.md)に関する記事をご覧ください。



## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)