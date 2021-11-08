---
title: Microsoft Defender for Cloud の強化されたセキュリティ機能
description: Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にする方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memildin
ms.openlocfilehash: 0d623789b1f99e6e7728fd5d3f80bb7ca2be7218
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441782"
---
# <a name="quickstart-set-up-microsoft-defender-for-cloud"></a>クイックスタート: Microsoft Defender for Cloud を設定する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud は、ハイブリッドおよびマルチクラウド ワークロード全体で統合されたセキュリティ管理と脅威の防止を実現します。 無料の機能で提供されるのは Azure リソースのみの制限付きセキュリティですが、強化されたセキュリティ機能ではこれらの機能がオンプレミスと他のクラウドに拡張されます。 Defender for Cloud は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 強化されたセキュリティ機能は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

このクイックスタート セクションでは、Microsoft Defender for Cloud と強化されたセキュリティ機能を有効にするための推奨手順について説明します。 このクイックスタートの手順をすべて完了すると、次のようになります。

> [!div class="checklist"]
> * Azure サブスクリプションで Defender for Cloud が有効になる
> * Azure サブスクリプションで[強化されたセキュリティ機能](enhanced-security-features-overview.md)が有効になる
> * 自動データ収集が設定される
> * セキュリティ アラートの[メール通知が設定](configure-email-notifications.md)される
> * ハイブリッドおよびマルチクラウド マシンが Azure に接続される

## <a name="prerequisites"></a>前提条件
Defender for Cloud を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

サブスクリプションで強化されたセキュリティ機能を有効にするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。

## <a name="enable-defender-for-cloud-on-your-azure-subscription"></a>Azure サブスクリプションで Defender for Cloud を有効にする

> [!TIP]
> 管理グループ内のすべてのサブスクリプションで Defender for Cloud を有効にするには、[複数の Azure サブスクリプションの Defender for Cloud を有効にする](onboard-management-group.md)方法に関するページを参照してください。

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

1. ポータルのメニューから **[Defender for Cloud]** を選択します。 

    Defender for Cloud の概要ページが開きます。

    :::image type="content" source="./media/get-started/overview.png" alt-text="Defender for Cloud の概要ダッシュボード" lightbox="./media/get-started/overview.png":::

    **Defender for Cloud の [概要]** では、ハイブリッド クラウド ワークロードのセキュリティ対策の統合ビューが表示され、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 詳細については、[Microsoft Defender for Cloud の概要ページ](overview-page.md)を参照してください。

    ご自身や他のサブスクリプション ユーザーによってまだオンボードされていない Azure サブスクリプションは、Defender for Cloud によって自動的に無料で有効になります。

**[サブスクリプション]** メニュー項目を選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 Defender for Cloud では、選択されたサブスクリプションのセキュリティの状態を反映するように表示が調整されます。 

Defender for Cloud を初めて起動してから数分以内に、以下が表示されます。

- 接続されているリソースのセキュリティを向上させる方法についての **推奨事項**。
- Defender for Cloud によって現在評価されているリソースとそれぞれのセキュリティの状態のインベントリ。

Defender for Cloud を最大限に活用するには、クイックスタート セクションの次のステップに進んでください。



## <a name="next-steps"></a>次の手順
このクイックスタートでは、Defender for Cloud を有効にしました。 次のステップでは、強化されたセキュリティ機能を有効にして、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と脅威の防止を実現します。

> [!div class="nextstepaction"]
> [クイックスタート: 強化されたセキュリティ機能を有効にする](enable-enhanced-security.md)
