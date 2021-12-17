---
title: 簡素化されたコンピューティング ノード通信を使用する
description: Batch プール インフラストラクチャの管理方法とオプトインまたはオプトアウトする方法を Azure Batch で簡素化する方法について説明します。
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 9bd53bcab9ccb9403d8d851300ba987a23eacc11
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270016"
---
# <a name="use-simplified-compute-node-communication"></a>簡素化されたコンピューティング ノード通信を使用する

Azure Batch プールには、Batch タスクの形式でユーザー指定のワークロードを実行する 1 つ以上の計算ノードが含まれています。 Batch の機能と Batch プール インフラストラクチャの管理を有効にするには、計算ノードが Azure Batch サービスと通信する必要があります。

このドキュメントでは、パブリック プレビュー期間中に新しい簡素化された計算ノード通信機能を使用して、Azure Batch サービスが Batch プール計算ノードと通信する方法、必要になる可能性のあるネットワーク構成の変更、Batch アカウントをオプトインまたはオプトアウトする方法に関する、今後の変更について説明します。

> [!IMPORTANT]
> 現在、Azure Batch での簡素化された計算ノード通信はパブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

現時点では、オプトインは必要ありません。 ただし、今後は、すべての Batch アカウントで、簡素化された計算ノード通信を使用する必要があります。 その時点で、正式な廃止の通知が提供されます。その前に Batch プールを移行する機会があります。

## <a name="compute-node-communication-changes"></a>計算ノードの通信の変更

Azure Batch サービスにより、ユーザーに代わって Batch プール インフラストラクチャを管理する方法が簡素化されます。 新しい通信方法によって、ベースライン操作に必要なインバウンドとアウトバウンドのネットワーク接続の複雑さとスコープが軽減されます。

簡素化された計算ノード通信にオプトインされていないアカウントの Batch プールでは、[仮想ネットワークにプールを作成する](batch-virtual-network.md)ときに、ネットワーク セキュリティ グループ (NSG)、ユーザー定義ルート (UDR)、ファイアウォールで次のネットワーク規則が必要です。

- 受信
  - BatchNodeManagement.*region* からの TCP に対する宛先ポート 29876、29877

- 送信
  - Storage.*region* への TCP に対する宛先ポート 443
  - ジョブ マネージャーのタスクなど、Batch サービスに戻る通信を必要とする特定のワークロード用の、BatchNodeManagement.*region* への TCP に対する宛先ポート 443

新しいモデルでは、簡素化された計算ノード通信を使用するアカウントの Batch プールでは、NSG、UDR、ファイアウォールで次のネットワーク規則が必要です。

- 受信
  - None

- 送信
  - BatchNodeManagement.*region* への TCP に対する宛先ポート 443

Batch アカウントのアウトバウンドの要件は、[List Outbound Network Dependencies Endpoints API](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) を使用して検出できます。 この API により、Batch アカウント プールの通信モデルに応じた、依存関係の基本セットが報告されます。 ユーザー固有のワークロードでは、他の Azure リソース (アプリケーション パッケージ用の Azure Storage、Azure Container Registry など) へのトラフィックを開くといった追加の規則や、仮想ファイル システム マウント機能用の Microsoft パッケージ リポジトリなどのエンドポイントが、必要になる場合があります。  

## <a name="benefits-of-the-new-model"></a>新しいモデルの利点

[新しい通信モデルにオプトインする](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) Azure Batch ユーザーには、ネットワーク接続と規則の簡素化からの利点があります。

簡素化された計算ノード通信を使用すると、インターネットからのインバウンド通信用にポートを開く必要がなくなり、セキュリティ リスクを軽減できます。 ベースライン操作には、既知のサービス タグへの 1 つのアウトバウンド規則だけが必要です。

また、新しいモデルでは、Storage.*region* へのアウトバウンド通信が必要なくなるので、いっそう細かい粒度でデータ流出を制御できるようになります。 ワークフローに必要な場合は、Azure Storage へのアウトバウンド通信を明示的にロックダウンできます (AppPackage ストレージ アカウント、リソース ファイルや出力ファイル用の他のストレージ アカウント、その他の同様のシナリオなど)。

現在はワークロードが変更の影響を受けていない場合でも (次のセクションで説明します)、[簡素化された計算ノード通信を使用するためのオプトイン](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication)が必要になる可能性があります。 これにより、Batch ワークロードは、このモデルによって将来実現される改善に対応できるようになります。

## <a name="scope-of-impact"></a>影響の範囲

多くの場合、この新しい通信モデルによって Batch ワークロードが直接影響を受けることはありません。 ただし、次の場合は、簡素化された計算ノード通信による影響があります。

- Batch プール作成の一部として仮想ネットワークを指定し、次のいずれか一方または両方を行うユーザー。
   - 簡素化された計算ノード通信と互換性のないアウトバウンド ネットワーク トラフィック規則を明示的に無効にする。
   - 簡素化された計算ノード通信と互換性のない UDR およびファイアウォール規則を使用する。
- 計算ノードでソフトウェア ファイアウォールを有効にし、簡素化された計算ノード通信と互換性のないソフトウェア ファイアウォール規則でアウトバウンド トラフィックを明示的に無効にするユーザー。

いずれかのケースが当てはまり、プレビューにオプトインする場合は、次のセクションで説明する手順に従って、Batch ワークロードが新しいモデルでも引き続き機能することを確認します。

### <a name="required-network-configuration-changes"></a>必要なネットワーク構成の変更

影響を受けるユーザーの場合、新しい通信モデルに移行するには、次の一連の手順が必要です。

1. Batch プールに該当するネットワーク構成に (NSG、UDR、ファイアウォールなど)、モデルを結合したもの (つまり、簡素化された計算ノード通信の前と後のネットワーク規則) が含まれることを確認します。 少なくとも、次の規則です。
   - 受信
     - BatchNodeManagement.*region* からの TCP に対する宛先ポート 29876、29877
   - 送信
     - Storage.*region* への TCP に対する宛先ポート 443
     - BatchNodeManagement.*region* への TCP に対する宛先ポート 443
1. ワークフローでインバウンドまたはアウトバウンドの追加のシナリオが必要な場合は、規則にこれらの要件を確実に反映する必要があります。
1. 以下で説明するように、[簡素化された計算ノード通信にオプトインします](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication)。
1. 次のいずれかのオプションを使って、新しい通信モデルを使用するようにワークロードを更新します。 どの方法を使用する場合でも、パブリック IP アドレスのないプールは影響を受けず、現時点では簡素化された計算ノード通信を使用できないことに気を付けてください。 「[現在の制限](#current-limitations)」セクションをご覧ください。
   1. 新しいプールを作成し、新しいプールが正しく動作していることを確認します。 ワークロードを新しいプールに移行し、以前のプールを削除します。
   1. 既存のすべてのプールのサイズを 0 ノードに変更し、スケールアウトします。
1. 以前のすべてのプールが削除されるか、0 にスケールアウトされたことを確認した後、[List Outbound Network Dependencies Endpoints API](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) のクエリを実行して、リージョンの Azure Storage へのアウトバウンド規則が存在しないことを確認します (Batch アカウントにリンクされている場合は、autostorage アカウントを除きます)。
1. 該当するすべてのネットワーク構成を、簡素化された計算ノード通信の規則に変更します。少なくとも以下を行います (上で説明した必要な追加規則に注意してください)。
   - 受信
     - None
   - 送信
     - BatchNodeManagement.*region* への TCP に対する宛先ポート 443

これらの手順に従った後で、簡素化された計算ノード通信の使用を停止する場合は、次のようにする必要があります。

1. 以下で説明するように、[簡素化された計算ノード通信からオプトアウトします](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication)。
1. ワークロードを新しいプールに移行するか、既存のプールのサイズを変更してスケールアウトします (上のステップ 4 を参照)。
1. [List Outbound Network Dependencies Endpoints API](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) を使って、すべてのプールで簡素化された計算ノード通信が使用されていないことを確認します。 リージョンの Azure Storage へのアウトバウンド規則が表示される必要があります。(Batch アカウントにリンクされている autostorage アカウントとは別に)。

## <a name="opt-your-batch-account-in-or-out-of-simplified-compute-node-communication"></a>簡素化された計算ノード通信に Batch アカウントをオプトインまたはオプトアウトする

簡素化された計算ノード通信に Batch アカウントをオプトインまたはオプトアウトするには、[Azure portal で新しいサポート リクエストを作成](../azure-portal/supportability/how-to-create-azure-support-request.md)します。

> [!IMPORTANT]
> 簡素化された計算ノード通信にオプトイン (またはオプトアウト) した場合、変更は将来の動作にのみ影響します。 要求の前に作成された計算ノードの数が 0 以外の Batch プールは影響を受けず、要求の前にアクティブだったモデルが使用されます。 オプトインまたはオプトアウトの前に既存のプールを移行する方法について詳しくは、移行手順をご覧ください。

要求を作成するときは、次のオプションを使用します。

:::image type="content" source="media/simplified-compute-node-communication/support-request-opt-in.png" alt-text="簡素化された計算ノード通信へのオプトインのサポート リクエストのスクリーンショット。":::

1. **[問題の種類]** で、 **[技術]** を選択します。
1. **[サービスの種類]** で、 **[Batch サービス]** を選びます。
1. **[リソース]** で、この要求の Batch アカウントを選びます。
1. **[Summary]\(要約\)** について:
   - オプトインするには、「Enable simplified compute node communication」 (簡素化された計算ノード通信を有効にする) と入力します。
   - オプトアウトするには、「Disable simplified compute node communication」 (簡素化された計算ノード通信を無効にする) と入力します。
1. **[問題の種類]** で、 **[Batch アカウント]** を選びます。
1. **[問題のサブタイプ]** で、 **[Other issues with Batch Accounts]\(Batch アカウントに関するその他の問題\)** を選びます。
1. **[次へ]** を選び、もう一度 **[次へ]** を選んで、 **[その他の詳細]** ページに移動します。
1. **[追加の詳細]** では、必要に応じて、自分のサブスクリプション内または複数のサブスクリプション間ですべての Batch アカウントを有効にすることも指定できます。 これを行う場合は、必ずここにサブスクリプション ID を含める必要があります。
1. ページで他の必要な選択を行い、 **[次へ]** を選びます。
1. 要求の詳細を確認した後、 **[作成]** を選んでサポート リクエストを送信します。

要求を送信した後は、アカウントがオプトイン (またはオプトアウト) されると通知を受け取ります。

## <a name="current-limitations"></a>現在の制限

簡素化された計算ノード通信にオプトインするアカウントに関して、次の既知の制限事項があります。

- [パブリック IP アドレスを持たないプールの作成](batch-pool-no-public-ip-address.md)は、現在、オプトインしたアカウントではサポートされていません。
- 以前に作成したパブリック IP アドレスのないプールでは、Batch アカウントがオプトインしている場合でも、簡素化された計算ノード通信は使用されません。
- [プライベート Batch アカウント](private-connectivity.md)を簡素化された計算ノード通信にオプトインすることはできますが、そのような Batch アカウントによって作成された Batch プールで簡素化された計算ノード通信を使用するには、パブリック IP アドレスを持っている必要があります。
- クラウド サービス構成プールは、現在、簡素化された計算ノード通信でサポートされておらず、一般に非推奨とされます。 Batch プールには仮想マシン構成を使用することをお勧めします。 詳細については、「[Batch プールの構成を Cloud Services から仮想マシンに移行する](batch-pool-cloud-service-to-virtual-machine-configuration.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークでのプール](batch-virtual-network.md)の詳細を理解します。
- [パブリック IP アドレスを指定してプールを作成する](create-pool-public-ip.md)方法を理解します。
