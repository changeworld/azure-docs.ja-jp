---
title: 組み込みテンプレートから Microsoft Sentinel プレイブックを作成してカスタマイズする | Microsoft Docs
description: この記事では、プレイブック テンプレートを使用してプレイブックを作成し、ニーズに合わせてカスタマイズする方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 407c57a235a1c716edeb8e17b80cbd1881d562ea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711156"
---
# <a name="create-and-customize-microsoft-sentinel-playbooks-from-built-in-templates"></a>組み込みテンプレートから Microsoft Sentinel プレイブックを作成してカスタマイズする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **プレイブック テンプレート** は、現在、**プレビュー段階** にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

プレイブック テンプレートは、事前に構築されてテストされた、すぐに使用できるワークフローであり、ニーズに合わせてカスタマイズできます。 テンプレートは、プレイブックをゼロから開発するときのベスト プラクティスのリファレンスとして、または新しい自動化シナリオのためのインスピレーションを得るためにも、役に立つ場合があります。

プレイブック テンプレートは、それからプレイブック (テンプレートの編集可能なコピー) を作成するまで、アクティブなプレイブック自体ではありません。

世界中のセキュリティ運用センターで使用される一般的な自動化シナリオに基づいて、Microsoft Sentinel コミュニティ、独立系ソフトウェア ベンダー (ISV)、Microsoft 独自の専門家によって、多くのプレイブック テンプレートが開発されています。

プレイブック テンプレートは、次のソースから取得できます。

- **[プレイブック テンプレート]** タブ ( **[オートメーション]** の下) には、Microsoft Sentinel コミュニティによって提供された主要なシナリオが表示されます。 同じテンプレートから複数のアクティブなプレイブックを作成できます。

    新しいバージョンのテンプレートが発行されると、そのテンプレートから作成されたアクティブなプレイブックに ( **[プレイブック]** タブ)、更新プログラムが利用できるという通知のラベルが付きます。

- プレイブック テンプレートは、特定の製品のコンテキストで [Microsoft Sentinel ソリューション](sentinel-solutions.md)の一部として入手することもできます。 ソリューションをデプロイすると、アクティブなプレイブックが生成されます。

- [Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)には、多くのプレイブック テンプレートが含まれています。 **[Azure に配置する]** ボタンを選択することで、Azure サブスクリプションにデプロイできます。

技術的には、プレイブック テンプレートは、いくつかのリソース (Azure Logic Apps ワークフローや、関連する各接続の API 接続など) で構成される [Azure Resource Manager (ARM) テンプレート](../azure-resource-manager/templates/index.yml)です。

この記事では、 **[オートメーション]** の **[プレイブック テンプレート]** タブからプレイブック テンプレートをデプロイする方法について説明します。

この記事は、次の方法を理解するのに役立ちます。

> [!div class="checklist"]
> - 既製のプレイブック テンプレートを探す
> - プレイブック テンプレートをデプロイする

## <a name="explore-playbook-templates"></a>プレイブック テンプレートを調べる

Microsoft Sentinel のナビゲーション メニューから **[オートメーション]** を選択し、 **[プレイブック テンプレート]** タブを選択します。

ここに表示されるプレイブック テンプレートでは、SOC が使用したりアイデアを得たりすることが多い自動化シナリオが示されています。 これらのプレイブックのほとんどは、Microsoft Sentinel コミュニティによって提供されたもので、もともとは Microsoft Sentinel GitHub リポジトリにありました。 これらの一部は、Microsoft Sentinel ソリューションに統合されています。

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="プレイブック ギャラリーのスクリーンショット。" lightbox="media/use-playbook-templates/gallery.png":::

要件に合うプレイブック テンプレートを見つけるには、次の条件で一覧をフィルター処理できます。

- **[トリガー]** は、インシデントの作成 (したがって、自動化ルールにアタッチできます)、アラートの作成 (したがって、分析ルールにアタッチできます)、または他の何かによって、プレイブックがトリガーされることを示します。 [詳細情報](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary)

- **[Logic Apps コネクタ]** は、このプレイブックが対話する外部サービスを示します。 デプロイ プロセスの間に、各コネクタは ID を使用して外部サービスに対する認証を行う必要があります。

- **[エンティティ]** は、インシデントにおいてエンティティの種類を検索することが期待されるプレイブックによって明示的にフィルター処理および解析されるエンティティの種類を示します。 たとえば、IP アドレスをブロックするようにファイアウォールに指示するプレイブックは、ブルート フォース攻撃検出ルールなど、IP アドレスを含むアラートを生成する分析ルールによって作成されるインシデントに対して動作することが予想されます。

- **[タグ]** は、特定のシナリオに関連付けるため、または特別な特性を示すために、プレイブックに適用されるラベルを示します。

  例 :

  - **[Enrichment]\(エンリッチメント\)** - プレイブックによって別のサービスから情報がフェッチされて、インシデントに情報が追加されます。 この情報は、通常、インシデントにコメントとして追加されるか、SOC に送信されます。

  - **[Remediation]\(修復\)** - プレイブックによって、潜在的な脅威を排除するために、影響を受けるエンティティに対してアクションが実行されます。

  - **[Sync]\(同期\)** - このプレイブックは、インシデント管理サービスなどの外部サービスがインシデントのプロパティで更新された状態を維持するのに役立ちます。

  - **[Notification]\(通知\)** - プレイブックによってメールまたはメッセージが送信されます。

  - **[Response from Teams]\(Teams からの応答\)** - このプレイブックを使用すると、アナリストは対話型カードを使用して Teams から手動操作を実行できます。

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="プレイブック テンプレートの一覧をフィルター処理します":::

## <a name="customize-a-playbook-from-a-template"></a>テンプレートのプレイブックをカスタマイズする

この手順では、プレイブック テンプレートをデプロイする方法について説明します。

このプロセスを繰り返して、同じテンプレートで複数のプレイブックを作成できます。

1. **[プレイブック テンプレート]** タブからプレイブック名を選択します。

1. プレイブックに前提条件がある場合は、必ず指示に従ってください。

    - 一部のプレイブックでは、他のプレイブックがアクションとして呼び出されます。 この 2 つ目のプレイブックは、**入れ子になったプレイブック** と呼ばれます。 このような場合の前提条件の 1 つとして、入れ子になったプレイブックを最初にデプロイする必要があります。

    - 一部のプレイブックでは、カスタム Logic Apps コネクタまたは Azure 関数をデプロイする必要があります。 そのような場合は、 **[Azure に配置する]** リンクが表示され、 <!-- WHERE? --> 一般的な ARM テンプレート デプロイ プロセスに移動します。

1. **[Create playbook]\(プレイブックの作成\)** を選択し、選択したテンプレートに基づくプレイブック作成ウィザードを開きます。 ウィザードには 4 つのタブがあります。

    - **[基本]:** 新しいプレイブック (Logic Apps リソース) を見つけて、名前を付けます (既定値を使用できます)。
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="プレイブック作成ウィザードの [基本] タブ":::

    - **[パラメーター]:** プレイブックで使用する顧客固有の値を入力します。 たとえば、このプレイブックで SOC にメールが送信される場合は、ここで受信者のアドレスを定義できます。 このタブは、プレイブックにパラメーターがある場合にのみ表示されます。

        > [!NOTE]
        > このプレイブックでカスタム コネクタが使用されている場合は、同じリソース グループにそれをデプロイする必要があり、このタブにその名前を挿入できます。

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="プレイブック作成ウィザードの [パラメーター] タブ":::

    - **[接続]:** 各アクションを展開して、以前のプレイブック用に作成した既存の接続を表示します。 [プレイブック用の接続の作成](authenticate-playbooks-to-sentinel.md)に関する詳細を確認してください。

        > [!NOTE]
        > カスタム コネクタの場合、接続は **[パラメーター]** タブで入力したカスタム コネクタの名前で表示されます。

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="プレイブック作成ウィザードの [接続] タブ":::

        何もない場合、または新しいものを作成する場合は、 **[Create new connection after deployment]\(デプロイ後に新しい接続を作成する\)** を選択します。 その場合、デプロイ プロセスが完了した後で Logic Apps デザイナーに移動します。

        [マネージド ID での接続](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity)をサポートするコネクタの場合 (**Microsoft Sentinel** など)、この接続方法が既定で選択されます。

    - **[確認および作成]:** プロセスの概要を確認し、プレイブックを作成する前に入力の検証を待ちます。

1. プレイブック作成ウィザードの手順を最後まで実行すると、Logic Apps デザイナーでの新しいプレイブックのワークフローのデザインに表示が移動します。

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Logic Apps デザイナーでプレイブックを確認します":::

1. デプロイ後に新しい接続を作成することを選択したコネクタごとに、次のようにします。
    1. ナビゲーション メニューから、 **[API 接続]** を選択します。
    1. 接続の名前を選択します。
    1. ナビゲーション メニューから **[API 接続の編集]** を選択します。
    1. 必要なパラメーターを入力して、 **[保存]** をクリックします。
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="API 接続の編集方法を示すスクリーンショット。":::

    または、Logic Apps デザイナーの関連する手順内から新しい接続を作成できます。
    1. エラー記号が表示される各ステップについて、それを選択して展開します。
    1. **[新規追加]** を選択します。
    1. 関連する手順に従って認証を行います。
    1. この同じコネクタを使用する他の手順がある場合は、そのボックスを展開します。 表示される接続の一覧から、先に作成した接続を選択します。

1. Microsoft Sentinel (または他のサポートされている接続) にマネージド ID 接続を使用することを選択した場合は、Microsoft Sentinel ワークスペース (または他のコネクタの関連するターゲット リソース) の新しいプレイブックに対するアクセス許可を付与します。

1. プレイブックを保存します。 **[アクティブなプレイブック]** タブに表示されるようになります。

1. このプレイブックを実行するには、[自動応答を設定する](automate-responses-with-playbooks.md#set-an-automated-response)か、[手動で実行](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert)します (アラート トリガーのみ)。

1. ほとんどのテンプレートはそのまま使用できますが、SOC のニーズに合わせて新しいプレイブックを調整することをお勧めします。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="issue-found-a-bug-in-the-playbook"></a>問題: プレイブックでバグが見つかった

プレイブックのバグを報告したり、改善を要求したりするには、プレイブックの詳細ペインで **[サポート元]** リンクを選択します。 コミュニティでサポートされているプレイブックの場合は、リンクを選択すると GitHub のイシューが開きます。 それ以外の場合は、サポートのページに移動します。

## <a name="next-steps"></a>次の手順

この記事では、プレイブック テンプレートを使用し、ニーズに合わせてプレイブックを作成およびカスタマイズする方法について説明しました。 Microsoft Sentinel でのプレイブックとオートメーションの詳細については、以下を参照してください。

- [Microsoft Sentinel でのセキュリティ オーケストレーション、オートメーション、および応答 (SOAR)](automation.md)
- [Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)
- [チュートリアル: Microsoft Sentinel でオートメーション ルールとプレイブックを使用する](tutorial-respond-threats-playbook.md)
- [Microsoft Sentinel へのプレイブックの認証](authenticate-playbooks-to-sentinel.md)
- [Microsoft Sentinel のプレイブックでトリガーとアクションを使用する](playbook-triggers-actions.md)
