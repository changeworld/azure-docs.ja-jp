---
title: Azure DDoS Rapid Response
description: アクティブな攻撃の間、特殊なサポートを受けるために、DDoS 専門家に問い合わせる方法について説明します。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 847cd81886a12d5a8d414181e2919b43aa607228
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732923"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS Rapid Response

攻撃を受けたとき、Azure DDoS Protection Standard の顧客は、DDoS Rapid Response (DRR) チームのサービスを受けられます。このチームは、攻撃時にその攻撃の調査を行ったり、攻撃後に分析を行ったりすることをサポートします。

## <a name="prerequisites"></a>前提条件

- このチュートリアルの手順を実行する前に、まず [Azure の DDoS 標準保護プラン](manage-ddos-protection.md)を作成する必要があります。

## <a name="when-to-engage-drr"></a>DRR に問い合わせるタイミング

次の場合にのみ、DRR に問い合わせる必要があります。 

- DDoS 攻撃中に、保護されたリソースのパフォーマンスが著しく低下したり、リソースが使用できなくなったことを検出した場合。 
- リソースが DDoS 攻撃を受けている一方で、DDoS Protection サービスが攻撃を効果的に軽減していないと思われる場合。
- ネットワーク トラフィックが大幅に増大するバイラル イベントを計画している場合。
- 攻撃が事業に大きな影響を与えている場合。

## <a name="engage-drr-during-an-active-attack"></a>アクティブな攻撃の間に DRR に問い合わせる

1. Azure portal で新しいサポート リクエストを作成する際、 **[問題の種類]** で [技術] を選択します。
2. **[サービス]** で **[DDOS 保護]** を選択します。
3. リソース ドロップダウン メニューでリソースを選択します。 _DRR に問い合わせるには DDoS Protection Standard によって保護されている仮想ネットワークにリンクしている DDoS プランを選択する必要があります。_

    ![リソースを選択する](./media/ddos-rapid-response/choose-resource.png)

4. 次の **[問題]** ページの **[重要度]** で [A - 重大な影響] を、 **[問題の種類]** で [攻撃中] を選択します。

    ![[重要度] と [問題の種類]](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 追加の詳細情報を入力して、サポート リクエストを送信します。

DRR は、Azure Rapid Response サポート モデルに従います。 Rapid Response の詳細については、「[サポート内容と応答性](https://azure.microsoft.com/support/plans/response/)」を参照してください。

詳細については、[Azure DDoS Protection Standard のドキュメント](./ddos-protection-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [シミュレーションを通じてテストする](test-through-simulations.md)方法を学習します。
- [DDoS 保護テレメトリを表示および構成する](telemetry.md)方法を学習します。
- [DDoS 診断ログを表示および構成する](diagnostic-logging.md)方法を学習します。
