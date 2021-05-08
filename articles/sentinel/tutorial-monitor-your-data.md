---
title: Azure Sentinel で Azure Monitor ブックを使用してデータを視覚化する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel でブックを使用してデータを視覚化する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491487"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>チュートリアル: データの視覚化と監視

Azure Sentinel に[データ ソースを接続](quickstart-onboard.md)した後、Azure Sentinel による Azure Monitor ブックの適用を使用して、データを視覚化および監視できます。これにより、多用途のカスタム ダッシュボードを作成できます。 ブックの表示は Azure Sentinel では異なりますが、[Azure Monitor ブックを使用して対話型レポートを作成する](../azure-monitor/visualize/workbooks-overview.md)方法を確認すると役立つ場合があります。 Azure Sentinel を使用すると、データ全体に対してカスタム ブックを作成できます。また、用意されている組み込みのブック テンプレートを使用してデータ ソースに接続すると、すぐにデータ全体の分析情報をすばやく得ることもできます。

このチュートリアルは、Azure Sentinel でデータを視覚化するのに役立ちます。
> [!div class="checklist"]
> * 組み込みのブックを使用する
> * 新しいブックを作成する

## <a name="prerequisites"></a>前提条件

Azure Sentinel ワークスペースのリソース グループに対して、少なくとも **ブックの閲覧者** または **ブックの共同作成者** のアクセス許可が必要です。

> [!NOTE]
> Azure Sentinel で表示されるブックは、Azure Sentinel ワークスペースのリソース グループ内に保存され、作成されたワークスペースごとにタグ付けされます。

## <a name="use-built-in-workbooks"></a>組み込みのブックを使用する

1. **[ブック]** にアクセスし、 **[テンプレート]** を選択して、Azure Sentinel の組み込みのブックの完全な一覧を表示します。 

    どれが接続しているデータ型と関連しているかを確認する場合、Azure Sentinel に関連データが既にストリーミングされている場合には各ブックの **[Required data types]\(必要なデータ型\)** フィールドでそのデータ型の隣に緑色のチェックマークが表示されます。

    [ ![ブックに移動](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. データが設定されているテンプレートを確認するには、 **[テンプレートの表示]** を選択します。

1. ブックを編集するには、 **[保存]** を選択して、テンプレートの JSON ファイルを保存する場所を選択します。

   > [!NOTE]
   > これにより、関連するテンプレートに基づいて Azure リソースが作成され、データではなく、ブックの JSON ファイルが保存されます。


1. **[保存されたブックの表示]** を選択します。 

    [ ![ブックの表示](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    必要に応じてブックをカスタマイズするには、ブックのツールバーの **[編集]** ボタンを選択します。 完了したら、 **[保存]** を選択して変更を保存します。

    詳細については、「[Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)」をご覧ください。

> [!TIP]
> ブックを複製するには、 **[編集]** 、 **[名前を付けて保存]** の順に選択します。このとき、同じサブスクリプションとリソース グループの下に別の名前で保存してください。
> 複製されたブックは、 **[マイ ブック]** タブに表示されます。
>
## <a name="create-new-workbook"></a>新しいブックを作成する

1. 新しいブックを最初から作成するには、 **[ブック]** 、 **[Add workbook]\(ブックの追加\)** の順に選択します。

    [ ![新しいブック](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. ブックを編集するには、 **[編集]** を選択し、必要に応じてテキスト、クエリ、およびパラメーターを追加します。 ブックをカスタマイズする方法の詳細については、「[Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)」を参照してください。 

1. クエリを作成するときに、 **[データ ソース]** が **[ログ]** に設定され、 **[リソース タイプ]** が **[Log Analytics]** に設定されていることを確認してから、関連するワークスペースを選択します。 

1. ブックを作成したら、Azure Sentinel ワークスペースのサブスクリプションとリソース グループの下にブックを保存します。

1. 組織内の他のユーザーがブックを使用できるようにする場合は、 **[保存先]** で **[共有レポート]** を選択します。 このブックを自分だけが使用できるようにする場合は、 **[個人用レポート]** を選択します。

1. ワークスペース内のブックを切り替えるには、 **[開く]** 、![ブックを開くアイコン](./media/tutorial-monitor-data/switch.png)を選択します。 任意のブックのツールバーにあります。 この画面は、切り替えることができる他のブックの一覧に切り替わります。

    開くブックを選択します。

    [ ![ブックの切り替え](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>ブック データの更新

ブックを更新して、更新されたデータを表示します。 ツールバーで、次のいずれかのオプションを選択します。

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **更新** して、ブックのデータを手動で更新します。

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **自動更新** は、構成された間隔で自動的に更新するようにブックを設定します。

    - サポートされる自動更新間隔の範囲は **5 分** から **1 日** です。

    - ブックの編集中は自動更新が一時停止します。間隔は、編集モードから表示モードに戻るたびに再起動されます。

    - データを手動で更新すると、自動更新の間隔も再起動されます。

    > [!TIP]
    > 既定では、自動更新はオフになっています。 パフォーマンスを最適化するために、ブックを閉じるたびに自動更新もオフになり、バックグラウンドでは実行されません。 次にブックを開いたときに、必要に応じて自動更新をオンにしてください。
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>ブックを印刷または PDF として保存する

ブックを印刷する、または PDF として保存するには、ブックのタイトルの右側にあるオプション メニューを使用します。

1. オプション > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **[コンテンツを印刷する]** を選択します。 
2. 印刷画面で、必要に応じて印刷設定を調整するか、 **[PDF として保存]** を選択してローカルに保存します。

次に例を示します。

[ ![ブックを印刷するか、PDF として保存します。](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>ブックを削除する方法

保存されたブック (保存されたテンプレートまたはカスタマイズされたブック) を削除するには、[ブック] ページで、削除する保存済みブックを選択し、 **[削除]** を選択します。 これにより、保存されたブックが削除されます。

> [!NOTE]
> これにより、ブック リソースだけでなく、テンプレートに対して行った変更も削除されます。 元のテンプレートは引き続き使用できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ブックを使用して、Azure Sentinel でデータを視覚化する方法を説明しました。

脅威への対応を自動化する方法については、「[Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。
