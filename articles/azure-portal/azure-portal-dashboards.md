---
title: Azure portal でダッシュボードを作成する
description: この記事では、Azure portal でダッシュボードを作成およびカスタマイズする方法について説明します。
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 57de040263fdc6ae7a3aaa366b7cabc4f98235b3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219851"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Azure portal でダッシュボードを作成する

ダッシュボードは、Azure portal でクラウドリソースを集中的に整理したビューです。 ダッシュボードは、日常業務のタスクをすばやく起動したり、リソースを監視したりできるワークスペースとして使用します。 たとえば、プロジェクト、タスク、またはユーザー ロールに基づいてカスタム ダッシュボードを作成します。

Azure portal には、出発点として既定のダッシュボードが用意されています。 既定のダッシュボードを編集し、追加のダッシュボードを作成およびカスタマイズできます。

> [!NOTE]
> 各ユーザーは、最大 100 のプライベート ダッシュボードを作成できます。 [ダッシュボードを発行して共有する](azure-portal-dashboard-share-access.md)と、サブスクリプションに Azure リソースとして実装され、この制限にはカウントされません。

この記事では、新しいダッシュボードを作成してカスタマイズする方法について説明します。 ダッシュボードの共有については、「[Azure ロールベースのアクセス制御を使用した Azure ダッシュボードの共有](azure-portal-dashboard-share-access.md)」を参照してください。

## <a name="create-a-new-dashboard"></a>新しいダッシュボードを作成する

この例では、割り当てられた名前を使用して新しいプライベート ダッシュボードを作成する方法を示します。 すべてのダッシュボードは作成時にプライベートになりますが、必要に応じて、ダッシュボードを発行して組織内の他のユーザーと共有することもできます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal メニューから、 **[ダッシュボード]** を選択します。 既定のビューがダッシュボードに既に設定されています。

    :::image type="content" source="media/azure-portal-dashboards/portal-menu-dashboard.png" alt-text="[ダッシュボード] が選択されている Azure portal のスクリーンショット。":::

1. **[新しいダッシュボード]** 、 **[空のダッシュボード]** の順に選択します。

    :::image type="content" source="media/azure-portal-dashboards/create-new-dashboard.png" alt-text="[新しいダッシュボード] オプションのスクリーンショット。":::

    この操作により、タイルを選択できる **[タイル ギャラリー]** と、タイルを配置する空のグリッドが開きます。

1. ダッシュボード ラベルの **[マイ ダッシュボード]** というテキストを選択し、カスタム ダッシュボードを簡単に識別できる名前を入力します。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="空のグリッドと [タイル ギャラリー] のスクリーンショット。":::

1. ダッシュボードをそのまま保存するには、ページ ヘッダーの **[カスタマイズ完了]** を選択します。 または、次のセクションのステップ 2 に進み、タイルを追加してダッシュボードを保存します。

ダッシュボード ビューに新しいダッシュボードが表示されます。 ダッシュボード名の横にある矢印を選択して、使用可能なダッシュボードを表示します。 リストには、他のユーザーが作成および共有したダッシュボードが含まれる場合があります。

## <a name="edit-a-dashboard"></a>ダッシュボードを編集する

次に、ダッシュボードを編集して、Azure リソースを表すタイルの追加、サイズ変更、配置を行いましょう。

### <a name="add-tiles-from-the-tile-gallery"></a>タイル ギャラリーからタイルを追加する

ダッシュボードにタイルを追加するには、次の手順に従います。

1. ダッシュボードのページ ヘッダーの ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択します。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-edit.png" alt-text="[編集] オプションが強調表示されたダッシュボードのスクリーンショット。":::

1. **[タイル ギャラリー]** を参照するか、検索フィールドを使用して特定のタイルを見つけます。 ダッシュボードに追加するタイルを選択します。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="タイル ギャラリーのスクリーンショット。":::

1. **[追加]** を選択して、タイルをデフォルトのサイズと場所でダッシュボードに追加します。 または、タイルをグリッドにドラッグし、希望の場所に配置します。 必要なタイルを追加しますが、いくつかのアイデアを次に示します。

    - **すべてのリソース** を追加して、既に作成したリソースを確認します。

    - 複数の組織を操作する場合は、リソースが属する組織を明確に示すために、 **[組織 ID]** タイルをダッシュボードに追加します。

1. 必要に応じて、タイルの[サイズ変更または再配置](#resize-or-rearrange-tiles)を行います。

1. 変更を保存するには、ページ ヘッダーの **[保存]** を選択します。 ページ ヘッダーで **[プレビュー]** を選択して、保存せずに変更をプレビューすることもできます。 このプレビュー モードでは、[フィルター](#set-and-override-dashboard-filters)がタイルにどのように影響するかを確認することもできます。 プレビュー画面で、 **[保存]** を選択して変更を保持するか、 **[破棄]** を選択して削除するか、 **[編集]** を選択して編集オプションに戻り、さらに変更を加えることができます。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="[プレビュー]、[保存]、および [破棄] の各オプションのスクリーンショット。":::

> [!NOTE]
> マークダウン タイルを使用すると、ダッシュボードにカスタムの静的コンテンツを表示できます。 これには、基本的な手順、画像、一連のハイパーリンク、または連絡先情報を指定できます。 マークダウンタイルの使用の詳細については、[Azure ダッシュボードでマークダウンタイルを使用してカスタムコンテンツを表示する](azure-portal-markdown-tile.md)を参照してください。

### <a name="pin-content-from-a-resource-page"></a>リソース ページからのコンテンツをピン留めする

ダッシュボードにタイルを追加するもう 1 つの方法では、リソース ページから直接行います。

多くのリソース ページのページ ヘッダーにピン アイコンがあります。これは、ソース ページを表すタイルをピン留めできることを意味します。 場合によっては、ページ内の特定のコンテンツの近くにピン アイコンが表示されることもあります。この場合、ページ全体ではなく、その特定のコンテンツのタイルをピン留めすることができます。

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-blade.png" alt-text="ピン アイコンが含まれたページのコマンド バーのスクリーンショット。":::

このアイコンを選択した場合は、既存のプライベートまたは共有のダッシュボードにタイルをピン留めできます。 **[新規作成]** を選択して、このピンを含む新しいダッシュボードを作成することもできます。

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-pane.png" alt-text="[ダッシュボードにピン留め] オプションのスクリーンショット。":::

### <a name="copy-a-tile-to-a-new-dashboard"></a>新しいダッシュボードにタイルをコピーする

別のダッシュボードでタイルを再利用したい場合は、他のダッシュボードにタイルをコピーすることができます。 そのためには、右上隅のコンテキスト メニューを選択し、 **[コピー]** を選択します。

:::image type="content" source="media/azure-portal-dashboards/copy-dashboard.png" alt-text="Azure portal でのタイルのコピー方法を示すスクリーンショット。":::

その後、既存のプライベート ダッシュボードや共有ダッシュボードにタイルをコピーしたり、作業中のダッシュボード内にタイルのコピーを作成したりできます。 **[新規作成]** を選択して、タイルのコピーを含む新しいダッシュボードを作成することもできます。

### <a name="resize-or-rearrange-tiles"></a>タイルのサイズまたは配置を変更する

タイルのサイズを変更したり、ダッシュボードのタイルを並べ替えたりするには、次の手順に従います。

1. ページ ヘッダーの ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択します。

1. タイルの右上隅にあるコンテキスト メニューを選択します。 次に、タイルのサイズを選択します。 任意のサイズをサポートするタイルには、右下隅に "ハンドル" も含まれています。これを使用して、タイルを目的のサイズにドラッグできます。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="タイル サイズ メニューが表示されたダッシュボードのスクリーンショット。":::

1. タイルを選択し、グリッド上の新しい場所にドラッグしてダッシュボードを編集します。

### <a name="set-and-override-dashboard-filters"></a>ダッシュボード フィルターを設定およびオーバーライドする

ダッシュボードの上部には、ダッシュボードに表示されるデータの **自動更新** と **時刻の設定** を設定するオプションと共に、フィルターを追加するオプションが表示されます。

:::image type="content" source="media/azure-portal-dashboards/dashboard-global-filters.png" alt-text="ダッシュボードのグローバル フィルターを示すスクリーンショット。":::

既定では、データは 1 時間ごとに更新されます。 これを変更するには、 **[自動更新]** を選択し、新しい更新間隔を選択します。 選択し終わったら、 **[適用]** を選択します。

既定の時刻設定は **UTC 時刻** であり、**過去 24 時間** のデータが表示されます。 これを変更するには、該当するボタンを選択し、新しい時間の範囲、時間の細分性、タイム ゾーンを選択して、 **[適用]** を選択します。

追加のフィルターを適用するには、 **[フィルターの追加]** を選択します。 表示されるオプションは、ダッシュボードのタイルによって異なります。 たとえば、特定のサブスクリプションまたは場所のデータのみを表示できる場合があります。 使用するフィルターを選び、必要な選択を行います。 その後、フィルターがデータに適用されます。 フィルターを解除するには、該当するボタンの **[X]** を選択します。

フィルター処理がサポートされているタイルには、タイルの左上隅に ![フィルター アイコン](./media/azure-portal-dashboards/dashboard-filter.png) フィルター アイコンがあります。 一部のタイルでは、そのタイルに固有のフィルターでグローバル フィルターをオーバーライドできます。 これを行うには、コンテキスト メニューから **[Configure tile data]\(タイル データの構成\)** を選択するか、フィルター アイコンを選択して目的のフィルターを適用します。

特定のタイルのフィルターを設定すると、そのタイルの左隅に二重フィルター アイコンが表示され、そのタイルのデータに独自のフィルターが反映されていることが示されます。

:::image type="content" source="media/azure-portal-dashboards/dashboard-filter-override.png" alt-text="フィルターのオーバーライドが使用されているタイルのアイコンを示すスクリーンショット。":::

## <a name="modify-tile-settings"></a>タイルの設定を変更する

一部のタイルには必要な情報を表示するため詳細な構成が必要となる場合があります。 たとえば、 **[メトリックのグラフ]** タイルは、Azure Monitor のメトリックを表示するように設定する必要があります。 また、タイル データをカスタマイズして、ダッシュボードの既定である時刻の設定とフィルターをオーバーライドすることもできます。

### <a name="complete-tile-configuration"></a>タイルの構成を完了する

設定が必要なタイルには、タイルをカスタマイズするまでバナーが表示されます。 たとえば、**メトリック グラフ** では、バナーに **[メトリックで編集]** と表示されます。 他のバナーでは、 **[タイルの構成]** などの異なるテキストが使用される場合があります。

タイルをカスタマイズするには:

1. ページ ヘッダーで、 **[保存]** を選択して、編集モードを終了します。

1. バナーを選択し、必要な設定を行います。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-configure-tile.png" alt-text="構成が必要なタイルのスクリーンショット。":::

### <a name="customize-time-span-for-a-tile"></a>タイルの期間をカスタマイズする

ダッシュボード上のデータには、アクティビティがグローバル フィルターに基づいて表示され、更新されます。 一部のタイルでは、1 つのタイルに対して異なる期間を選択できます。 これを行うには、次のステップに従います。

1. コンテキスト メニューから **[タイル データのカスタマイズ]** を選択するか、タイルの左上隅の![フィルター アイコン](./media/azure-portal-dashboards/dashboard-filter.png)を選択します。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-customize-tile-data.png" alt-text="タイルのコンテキスト メニューのスクリーンショット。":::

1. **[タイル レベルでのダッシュボード時刻の設定を上書きします]** チェックボックスをオンにします。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-override-time-settings.png" alt-text="タイルの時間設定を構成するためのダイアログのスクリーンショット。":::

1. このタイルに表示する期間を選択します。 過去 30 分から過去 30 日までの範囲の中から選択することも、カスタム範囲を定義することもできます。

1. 表示する時間の粒度を選択します。  1 分単位から 1 か月単位まで任意の粒度で表示できます。

1. **[適用]** を選択します。

### <a name="change-the-title-and-subtitle-of-a-tile"></a>タイルのタイトルとサブタイトルを変更する

タイルによっては、タイトルとサブタイトルを編集できます。 これを行うには、コンテキストメニューから **[タイル設定の構成]** を選択します。

:::image type="content" source="media/azure-portal-dashboards/dashboard-tile-rename.png" alt-text="[タイル設定の構成]"::: オプションを示すスクリーンショット。

タイルのタイトルやサブタイトルに変更を加え、 **[適用]** を選択します。

:::image type="content" source="media/azure-portal-dashboards/dashboard-title-subtitle.png" alt-text="タイルのタイトルとサブタイトルを変更する方法を示すスクリーンショット。":::
 
## <a name="delete-a-tile"></a>タイルを削除する

ダッシュボードからタイルを削除するには、次のいずれかの操作を行います。

- タイルの右上隅のコンテキスト メニューを選択し、 **[ダッシュボードから削除する]** を選択します。

- ![編集アイコン](./media/azure-portal-dashboards/dashboard-edit-icon.png) **[編集]** を選択して、カスタマイズ モードに切り替えます。 タイルの右上隅にマウス ポインターを移動し、![削除アイコン](./media/azure-portal-dashboards/dashboard-delete-icon.png) 削除アイコンを選択してダッシュボードからタイルを削除します。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-tile.png" alt-text="ダッシュボードからタイルを削除する方法を示すスクリーンショット。":::

## <a name="clone-a-dashboard"></a>ダッシュボードを複製する

既存のダッシュボードを新しいダッシュボードのテンプレートとして使用するには、次の手順に従います。

1. コピーするダッシュボードがダッシュボード ビューに表示されていることを確認します。

1. ページ ヘッダーで、![複製アイコン](./media/azure-portal-dashboards/dashboard-clone.png) **[複製]** を選択します。

1. *ダッシュボード名* の **複製** という名前のダッシュボードのコピーが編集モードで開きます。 この記事の前の手順を使用して、ダッシュボードの名前を変更し、カスタマイズします。

## <a name="publish-and-share-a-dashboard"></a>ダッシュボードを発行して共有する

作成したダッシュボードは、既定でプライベートになります。つまり、ダッシュボードを表示できるのは、その作成者に限られます。 他のユーザーがダッシュボードを利用できるようにするには、それらを発行して共有できます。 詳細については、「[Azure ロールベースのアクセス制御を使用した Azure ダッシュボードの共有](azure-portal-dashboard-share-access.md)」を参照してください。

### <a name="open-a-shared-dashboard"></a>共有ダッシュボードを開く

共有ダッシュボードを見つけて開くには、次の手順に従います。

1. ダッシュボード名の横にある矢印を選択します。

1. 表示されたダッシュボードの一覧から選択します。 開きたいダッシュボードが一覧に表示されない場合は、次のようにします。

    1. **[すべてのダッシュボードを参照]** を選択します。

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse.png" alt-text="ダッシュボード選択メニューのスクリーンショット。":::

    1. **[種類]** フィールドで、 **[共有ダッシュボード]** を選択します。

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse-all.png" alt-text="すべてのダッシュボード選択メニューのスクリーンショット。":::

    1. 1 つ以上のサブスクリプションを選択します。 テキストを入力して、ダッシュボードを名前でフィルター処理することもできます。

    1. 共有ダッシュボードの一覧からダッシュボードを選択します。

## <a name="delete-a-dashboard"></a>ダッシュボードを削除する

プライベートまたは共有ダッシュボードを完全に削除するには、次の手順に従います。

1. ダッシュボード名の横のリストから削除するダッシュボードを選択します。

1. ページ ヘッダーの ![削除アイコン](./media/azure-portal-dashboards/dashboard-delete-icon.png) **[削除]** を選択します。

1. プライベート ダッシュボードの場合は、確認ダイアログで **[OK]** をクリックしてダッシュボードを削除します。 共有ダッシュボードの場合は、確認ダイアログで、この発行済みのダッシュボードが他のユーザーに表示されなくなることを確認するチェックボックスをオンにします。 **[OK]** をクリックします。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-dash.png" alt-text="削除の確認のスクリーンショット。":::

## <a name="recover-a-deleted-dashboard"></a>削除されたダッシュボードの復旧

グローバル Azure クラウドを使用していて、Azure portal で _発行された_ ダッシュボードを削除した場合は、削除から 14 日以内であればそのダッシュボードを回復できます。 詳細については、「[Azure portal で削除されたダッシュボードを復旧する](recover-shared-deleted-dashboard.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御を使用した Azure ダッシュボードの共有](azure-portal-dashboard-share-access.md)
- [プログラムによる Azure ダッシュボードの作成](azure-portal-dashboards-create-programmatically.md)
