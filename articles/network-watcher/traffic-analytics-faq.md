---
title: Azure トラフィック分析についてよく寄せられる質問 | Microsoft Docs
description: トラフィック分析についてよく寄せられる質問の回答を確認します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 01d5150bff8642a1a3fe9b7ac063923916f191c0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>トラフィック分析についてよく寄せられる質問

1.  トラフィック分析を使用する前提条件は何ですか?

    Traffic Analytics の前提条件は次のとおりです。

    - Network Watcher 対応のサブスクリプション
    - 監視対象の NSG に対して有効になっている NSG フロー ログ
    - 未処理フロー ログを格納するための Azure Storage アカウント
    - 読み取りと書き込みのアクセスがある Log Analytics (OMS) ワークスペース
    - 自分のアカウントには、Microsoft.Network プロバイダーで次の操作を割り当てる必要があります。

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  トラフィック分析が使用可能な Azure リージョンはどこですか?

    プレビュー リリースでは、米国中西部、米国東部、米国東部 2、米国中北部、米国中南部、米国中部、米国西部、米国西部 2、西ヨーロッパ、北ヨーロッパ、英国西部、英国南部、オーストラリア東部、オーストラリア南東部のいずれかの**サポート リージョン**の NSG にトラフィック分析を使用できます。 Log Analytics ワークスペースは、米国中西部、米国東部、西ヨーロッパ、オーストラリア南東部、または英国南部リージョンに存在する必要があります。

3.  フロー ログを有効化する NSG が、自分の OMS ワークスペースとは別のリージョンに存在できますか?

    [はい]

4.  1 つのワークスペース内に複数の NSG を構成できますか?

    [はい]

5.  既存の OMS ワークスペースを使用できますか?

    はい。既存のワークスペースを選択する場合は、それが新しいクエリ言語に移行されていることを確認してください。 そのワークスペースのアップグレードを望まない場合は、新しいワークスペースを作成する必要があります。 新しいクエリ言語の詳細については、「[新しいログ検索への Azure Log Analytics のアップグレード](../log-analytics/log-analytics-log-search-upgrade.md)」をご覧ください。

6.  自分の Azure Storage アカウントと自分の OMS ワークスペースをそれぞれ別のサブスクリプションに置くことができますか?

    [はい]

7.  未処理ログをさまざまなサブスクリプションのさまざまなストレージ アカウントに格納できますか?

    いいえ。 未処理ログは、フロー ログに対して NSG が有効になっているすべてのストレージ アカウントに格納できます。ただし、ストレージ アカウントと未処理ログの両方を、同じサブスクリプションとリージョンに置く必要があります。

8.  トラフィック分析の NSG を構成中に [見つかりません] エラーを受け取った場合、どのように解決すればよいでしょうか?

    質問 2 に記載したサポート リージョンを選択してください。 サポートされていないリージョンを選択すると、[見つかりません] エラーが表示されます。

9.  NSG フロー ログで NSG の状態が [Failed to load]\(読み込み失敗\) になります。この後、どうすればよいですか?

    フロー ログが正常に機能するためには、Microsoft.Insights プロバイダーが登録されている必要があります。 サブスクリプションで Microsoft.Insights プロバイダーが登録されているかどうかが不明な場合は、次のコマンドの *xxxxx-xxxxx-xxxxxx-xxxx* を置き換えて PowerShell で実行してください。

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. ソリューションを構成しました。 ダッシュボードに何も表示されないのはなぜですか?

    ダッシュボードを最初に表示する際は最大で 30 分かかることがあります。 ソリューションで意味がある分析情報が導出されるには、まず十分なデータを集計する必要があります。その後でレポートが生成されます。 

11.  We could not find any data in this workspace for selected time interval. Try changing the time interval or select a different workspace\(選択した期間についてこのワークスペースにはデータが見つかりませんでした。期間を変更するか別のワークスペースを選択してください\) というメッセージを受け取った場合、どのように解決すればよいですか?

        次の方法を試してください。
        - 上部のバーで期間を変更してみます。
        - 上部のバーで別の Log Analytics ワークスペースを選択します。
        - 30 分後に Traffic Analytics にアクセスしてみます (最近有効にした場合)。
    
        解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

12.  1) Analyzing your NSG flow logs for the first time. This process may take 20-30 minutes to complete. Check back after some time. 2) If the above step doesn’t work and your workspace is under the free SKU, then check your workspace usage here to validate over quota, else refer to FAQs for further information\(1) NSG フロー ログを初めて分析しています。このプロセスが完了するまで 20 -30 分かかることがあります。しばらくしてから確認してください。2) 上記のステップが機能せず、ワークスペースが無料 SKU にある場合は、ここでワークスペースの使用状況をチェックして超過クォータを検証します。該当しない場合は、詳細について FAQ を参照してください\) というメッセージを受け取った場合、どのように解決すればよいですか?

        このエラーは次の理由で生成される可能性があります。
        - トラフィック分析が最近有効化され、意味のある分析情報を導出できる十分なデータを集計していたために、レポートを生成できませんでした。 この場合は、30 分後に再試行してください。
        - OMS ワークスペースが無料 SKU にあり、クォータ制限に違反しました。 この場合は、容量が大きな SKU でワークスペースを使用する必要があります。
    
        解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。
    
13.  Looks like we have resources data (Topology) and no flows information. Meanwhile, click here to see resources data and refer to FAQs for further information\(リソース データ (トポロジ) はありますがフロー情報がないようです。まず、ここをクリックしてリソース データを確認し、詳細については FAQ を参照してください\) というメッセージを受け取った場合、どのように解決すればよいですか?

        ダッシュボードにはリソース情報が表示されていますが、フロー関連の統計がありません。 リソース間の通信フローがないためにデータが示されない可能性があります。 60 分間待ってから、状態を再確認します。 リソース間の通信フローが存在することが確実な場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

14.  トラフィック分析はどのように課金されますか?

        トラフィック分析は、削減されたログを拡張することと、拡張ログを Log Analytics ワークスペースに格納することについて測定されます。 プレビュー中、トラフィック分析は削減されたログの拡張については課金されませんが、ワークスペースでのデータの保持は、通常料金での課金の対象となります。 この回答は、トラフィック分析の価格が発表されたときに更新されます。

15.  geo マップ ビューでキーボードを使用してどのようにナビゲートできますか?

        geo マップ ページには主に次の 2 つのセクションがあります。
    
        - **バナー**: geo マップの一番上にあるバナーでは、[デプロイメント]/[No Deployment]\(デプロイメントなし\)/[アクティブ]/[非アクティブ]/[Traffic Analytics 有効]/[Traffic Analytics 無効]/[Traffic from countries]\(各国のトラフィック\)/[影響なし]/[悪意あり]/[許可された悪意のあるトラフィック] などのボタンでトラフィック分布フィルターを選択したり、凡例情報を選択したりできます。 定義されたボタンを選択すると、それぞれのフィルターがマップに適用されます。たとえば、ユーザーがバナーの下の [アクティブ] フィルター ボタンを選択すると、デプロイメントの "アクティブ" なデータセンターがマップで強調表示されます。
        - **マップ**: バナーの下にある [マップ] セクションには、Azure のデータセンターや各国のトラフィック分布が表示されます。
    
        **バナーでのキーボード ナビゲーション**
    
        - 既定では、バナーについて geo マップ ページで選択されているのはフィルターの [Azure DC] ボタンです。
        - 別のフィルター ボタンに移動するには、`Tab` キーまたは `Right arrow` キーを使用して隣に移動できます。 戻るには、`Shift+Tab` キーまたは `Left arrow` キーを使用します。 前に移動するときの方向は、左から右が優先され、次に上から下の順になります。
        - `Enter` キーまたは `Down` 矢印キーを押して、選択したフィルターを適用します。 フィルターの選択とデプロイメントに基づいて、[マップ] セクションの 1 つまたは複数のノードが強調表示されます。
            - **バナー**と**マップ**を切り替えるには、`Ctrl+F6` を押します。
        
        **マップでのキーボード ナビゲーション**
    
        - バナーでフィルターを選択してから `Ctrl+F6` を押すと、マップ ビューで強調されたノードの 1 つ (**Azure データセンター**または**国/リージョン**) にフォーカスが移動します。
        - マップ内で強調表示された他のノードに移動するには、前方移動では `Tab` キーまたは `Right arrow` キー、後方移動では `Shift+Tab` キーまたは `Left arrow` キーを使用できます。
        - マップ内で強調表示されたノードを選択するには、`Enter` キーまたは `Down arrow` キーを使用します。
        - そのようなノードを選択すると、そのノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、**[Information Tool Box]\(情報ツール ボックス\)** の [閉じる] ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する際は、前方には `Right` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、**[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
        - **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスがあるときに `Tab`を押すと、フォーカスは、選択したノードと同じ大陸のエンドポイントに移動します。 `Right` キーと `Left arrow` キーを使用して、これらのエンドポイント間を移動できます。
        - 他のフロー エンドポイント/大陸クラスターに移動するには、前方移動では `Tab`、後方移動では `Shift+Tab` を使用します。
        - フォーカスが `Continent clusters` にある場合は、`Enter` または `Down` 矢印キーを使用して、大陸クラスター内のエンドポイントを強調表示します。 大陸クラスターのエンドポイントと情報ボックスの [閉じる] ボタンを移動するには、前方移動では `Right` キー、後方移動では `Left arrow` キーをそれぞれ使用します。 任意のエンドポイントで、`Shift+L` を使用すると、選択したノードからエンドポイントへの接続線に切り替えることができます。 再び `Shift+L` を押すと、選択したエンドポイントに移動します。
        
        すべてのステージ:
    
        - `ESC` を押すと、展開された選択範囲が折りたたまれます。
        - `UP Arrow` キーは `ESC` と同じ動作を実行します。 `Down arrow` キーは `Enter` と同じ動作を実行します。
        - `Shift+Plus` を使用して拡大、`Shift+Minus` を使用して縮小します。
