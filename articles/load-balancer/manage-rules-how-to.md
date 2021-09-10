---
title: Azure Load Balancer のルールを管理する - Azure portal
description: この記事では、Azure portal を使用して Azure Load Balancer のルールを管理する方法について説明します
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868724"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>Azure portal を使用して Azure Load Balancer のルールを管理する

Azure Load Balancer では、バックエンド プールへのトラフィックを構成する規則がサポートされています。  この記事では、Azure Load Balancer のルールを管理する方法について説明します。

以下の 4 種類の規則があります。

* **負荷分散規則** - ロード バランサーの規則は、バックエンド プール内の **すべて** のインスタンスに受信トラフィックを分散させる方法を定義するために使用されます。 負荷分散規則により、フロントエンドの特定の IP 構成およびポートがバックエンドの複数の IP アドレスおよびポートにマップされます。 例として、Web トラフィックの負荷分散を行うためにポート 80 に対して作成される規則が挙げられます。

* **高可用性ポート** - **protocol - all** と **port - 0** を指定して構成されるロード バランサーの規則です。 これらの規則では単一の規則が有効にされて、内部の標準ロード バランサーのすべてのポートに到着する、TCP と UDP のすべてのトラフィックが負荷分散されます。 HA ポート負荷分散規則は、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) の高可用性と拡張性のようなシナリオで役立ちます。 この機能は、数多くのポートを負荷分散する必要がある場合に役立ちます。

* **インバウンド NAT 規則** - インバウンド NAT 規則によって、フロントエンド IP アドレスとポートの組み合わせに送信された受信トラフィックが転送されます。 トラフィックは、バックエンド プール内の **特定** の仮想マシンまたはインスタンスに送信されます。 ポート フォワーディングも、負荷分散と同じハッシュベースの分散によって実行されます。

* **アウトバウンド規則** - **すべての** 仮想マシン、またはバックエンド プールによって識別されたインスタンスを対象とするアウトバウンド ネットワーク アドレス変換 (NAT) は、アウトバウンド規則によって構成します。 この規則により、バックエンド内のインスタンスがインターネットやその他のエンドポイントに通信 (送信) できるようになります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- サブスクリプション内の Standard パブリック ロード バランサー。 Azure Load Balancer の作成の詳細については、「[クイックスタート: Azure portal を使用して、VM の負荷分散を行うパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-portal.md)」を参照してください。 この記事の例で使用するロード バランサーの名前は、**myLoadBalancer** です。

- サブスクリプション内の Standard 内部ロード バランサー。 Azure Load Balancer の作成の詳細については、「[クイックスタート: Azure portal を使用して、VM の負荷分散を行う内部ロード バランサーを作成する](quickstart-load-balancer-standard-internal-portal.md)」を参照してください。 この記事の例で使用するロード バランサーの名前は、**myLoadBalancer** です。

## <a name="load-balancing-rules"></a>負荷分散規則

このセクションでは、負荷分散規則を追加および削除する方法について説明します。 この例では、パブリック ロード バランサーが使用されます。

### <a name="add-a-load-balancing-rule"></a>負荷分散規則の追加

この例では、ポート 80 の負荷分散を行う規則を作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[負荷分散規則]** を選択します。

5. **[負荷分散規則]** で **[+ 追加]** を選択して規則を追加します。

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Standard ロード バランサーでの、負荷分散規則のページのスクリーンショット。" border="true":::

6. **[負荷分散規則の追加]** で、以下の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | **myHTTPRule** |
    | IP バージョン | **[IPv4]** または **[IPv6]** を選択します。 |
    | フロントエンド IP アドレス | ロード バランサーのフロントエンド IP アドレスを選択します。 <br> この例では **[myFrontendIP]** です。 |
    | Protocol | 既定値の **[TCP]** のままにします。 |
    | Port | 「**80**」と入力します。 |
    | バックエンド ポート | 「**80**」と入力します。 |
    | バックエンド プール | ロード バランサーのバックエンド プールを選択します。 </br> この例では **[myBackendPool]** です。 |
    | 正常性プローブ | **[新規作成]** を選択します。 </br> **[名前]** に、「**myHealthProbe**」と入力します。 </br> **[プロトコル]** で、 **[HTTP]** を選択します。 </br> 残りは既定値のままにするか、実際の要件に合わせて調整します。 </br> **[OK]** を選択します。 |
    | セッション永続化 | **[なし]** または必要な永続化を選択します。 </br> 分散モードの詳細については、[Azure Load Balancer の分散モード](load-balancer-distribution-mode.md)に関するページを参照してください。 | 
    | アイドル タイムアウト (分) | 既定値の **4** のままにするか、必要なアイドル タイムアウトまでスライダーを移動します。 |
    | TCP リセット | **[Enabled]** を選択します。 </br> TCP リセットの詳細については、「[Load Balancer の TCP リセットおよびアイドルのタイムアウト](load-balancer-tcp-reset.md)」を参照してください。 |
    | フローティング IP | 既定値の **[無効]** のままにするか、デプロイにフローティング IP が必要な場合は有効にします。 </br> フローティング IP については、「[Azure Load Balancer のフローティング IP の構成](load-balancer-floating-ip.md)」を参照してください。 |
    | アウトバウンド送信元ネットワーク アドレス変換 (SNAT) | 既定値の **[(推奨) アウトバウンド規則を使用して、バックエンド プールのメンバーがインターネットにアクセスできるようにします。]** のままにします。 </br> アウトバウンド規則と (SNAT) の詳細については、「[Azure Load Balancer のアウトバウンド規則](outbound-rules.md)」と「[アウトバウンド接続に送信元ネットワーク アドレス変換 (SNAT) を使用する](load-balancer-outbound-connections.md)」を参照してください。|

7. **[追加]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="[ロード バランサー規則の追加] ページのスクリーンショット。" border="true":::

### <a name="remove-a-load-balancing-rule"></a>負荷分散規則を削除する

この例では、負荷分散規則を削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[負荷分散規則]** を選択します。

5. 削除するルールの横にある 3 つのドットを選択します。

6. **[削除]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="負荷分散規則の削除のスクリーンショット。" border="true":::

## <a name="high-availability-ports"></a>高可用性ポート

このセクションでは、高可用性ポート規則を追加および削除する方法について説明します。 この例では、内部ロード バランサーを使用します。 

HA ポート規則は、Standard 内部ロード バランサーでサポートされています。

### <a name="add-high-availability-ports-rule"></a>高可用性ポート規則を追加する

この例では、高可用性ポート規則を作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[負荷分散規則]** を選択します。

5. **[負荷分散規則]** で **[+ 追加]** を選択して規則を追加します。

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Standard ロード バランサーでの、負荷分散規則のページのスクリーンショット。" border="true":::

6. **[負荷分散規則の追加]** で、以下の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「**myHARule**」と入力します。 |
    | IP バージョン | **[IPv4]** または **[IPv6]** を選択します。 |
    | フロントエンド IP アドレス | ロード バランサーのフロントエンド IP アドレスを選択します。 <br> この例では **[myFrontendIP]** です。 </br> **[HA ポート]** の横のボックスを選択します。 |
    | バックエンド プール | ロード バランサーのバックエンド プールを選択します。 </br> この例では **[myBackendPool]** です。 |
    | 正常性プローブ | **[新規作成]** を選択します。 </br> **[名前]** に、「**myHealthProbe**」と入力します。 </br> **[プロトコル]** で **[TCP]** を選択します。 </br> **[ポート]** に TCP ポートを入力します。 この例では、ポート **80** です。 要件を満たすポートを入力します。 </br> 残りは既定値のままにするか、実際の要件に合わせて調整します。 </br> **[OK]** を選択します。 |
    | セッション永続化 | **[なし]** または必要な永続化を選択します。 </br> 分散モードの詳細については、[Azure Load Balancer の分散モード](load-balancer-distribution-mode.md)に関するページを参照してください。 | 
    | アイドル タイムアウト (分) | 既定値の **4** のままにするか、必要なアイドル タイムアウトまでスライダーを移動します。 |
    | TCP リセット | **[Enabled]** を選択します。 </br> TCP リセットの詳細については、「[Load Balancer の TCP リセットおよびアイドルのタイムアウト](load-balancer-tcp-reset.md)」を参照してください。 |
    | フローティング IP | 既定値の **[無効]** のままにするか、デプロイにフローティング IP が必要な場合は有効にします。 </br> フローティング IP については、「[Azure Load Balancer のフローティング IP の構成](load-balancer-floating-ip.md)」を参照してください。 |

    HA ポート規則の構成の詳細については、「 **[高可用性ポートの概要](load-balancer-ha-ports-overview.md)** 」を参照してください。

7. **[追加]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="ロード バランサーの HA ポート規則を追加するページのスクリーンショット。" border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>高可用性ポート規則を削除する

この例では、負荷分散規則を削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[負荷分散規則]** を選択します。

5. 削除するルールの横にある 3 つのドットを選択します。

6. **[削除]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="HA ポート負荷分散規則の削除のスクリーンショット。" border="true":::

## <a name="inbound-nat-rule"></a>受信 NAT 規則

インバウンド NAT 規則は、バックエンド プール内の特定の VM への接続をルーティングするために使用されます。 インバウンド NAT 規則の構成とテストに関する詳細と、詳しいチュートリアルについては、[チュートリアルの、ポータルを使用して Azure Load Balancer でのポート転送を構成すること](tutorial-load-balancer-port-forwarding-portal.md)に関するページを参照してください。

## <a name="outbound-rule"></a>送信規則

このセクションでは、アウトバウンド規則を追加および削除する方法について説明します。 この例では、パブリック ロード バランサーを使用します。 

アウトバウンド規則は、Standard パブリック ロード バランサーでサポートされています。

### <a name="add-outbound-rule"></a>アウトバウンド規則を追加する

この例では、アウトバウンド規則を作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[アウトバウンド規則]** を選択します。

5. **[アウトバウンド規則]** で **[+ 追加]** を選択して規則を追加します。

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="Standard ロード バランサーでの、アウトバウンド規則のページのスクリーンショット。" border="true":::

6. **[アウトバウンド規則の追加]** で、以下の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「**myOutboundRule**」と入力します。 |
    | フロントエンド IP アドレス | ロード バランサーのフロントエンド IP アドレスを選択します。 <br> この例では **[myFrontendIP]** です。 | 
    | Protocol | 既定値の **[すべて]** をそのまま使用します。 |
    | アイドル タイムアウト (分) | 既定値の **4** のままにするか、要件が満たされるまでスライダーを移動します。 |
    | TCP リセット | 既定値の **[有効]** のままにします。 |
    | バックエンド プール | ロード バランサーのバックエンド プールを選択します。 </br> この例では **[myBackendPool]** です。 |
    | **ポートの割り当て** |   |
    | ポートの割り当て | **[送信ポートの数を手動で選択する]** を選択します。 |
    | **送信ポート** |  |
    | 選択基準 | **[インスタンスごとのポート]** を選択します。 |
    | インスタンスあたりのポート数 | 「**10000**」と入力します。 |

7. **[追加]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="[アウトバウンド規則の追加] ページのスクリーンショット。" border="true":::

### <a name="remove-an-outbound-rule"></a>アウトバウンド規則を削除する

この例では、アウトバウンド規則を削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

3. **myLoadBalancer** またはお使いのロード バランサーを選択します。

4. ロード バランサーのページで、 **[設定]** の **[アウトバウンド規則]** を選択します。

5. 削除するルールの横にある 3 つのドットを選択します。

6. **[削除]** を選択します。

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="アウトバウンド規則の削除のスクリーンショット。" border="true":::

## <a name="next-steps"></a>次の手順

この記事では、Azure Load Balancer の負荷分散規則を管理する方法について学習しました。

Azure Load Balancer の詳細については、以下を参照してください。
- [Azure Load Balancer の概要](load-balancer-overview.md)
- [よく寄せられる質問 - Azure Load Balancer](load-balancer-faqs.yml)
