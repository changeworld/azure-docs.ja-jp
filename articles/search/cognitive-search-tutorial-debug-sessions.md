---
title: チュートリアル:デバッグ セッションを使用してスキルセットに対する診断、修正、および変更のコミットを行う
titleSuffix: Azure Cognitive Search
description: デバッグ セッション (プレビュー) では、スキルセットの問題やエラーを評価および修復するためのポータルベースのインターフェイスを提供します
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8bbd0b1979da69e5d4d18009100a7caee5a3d722
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397404"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>チュートリアル:スキルセットに対する診断、修復、および変更のコミットを行う

この記事では、Azure portal を使用してデバッグ セッションにアクセスし、指定されたスキルセットの問題を修復します。 このスキルセットには、対処する必要があるいくつかのエラーがあります。 このチュートリアルでは、デバッグ セッションを通じて、スキルの入力と出力に関する問題を特定し、解決します。

> [!Important]
> デバッグ セッションは、サービス レベル アグリーメントなしで提供されるプレビュー機能です。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成するか、現在のサブスクリプションを使用してください
> * Azure Cognitive Search サービス インスタンス
> * Azure ストレージ アカウント
> * [Postman デスクトップ アプリ](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>サービスを作成してデータを読み込む

このチュートリアルでは、Azure Cognitive Search サービスと Azure Storage サービスを使用します。

* 19 個のファイルから成る[サンプル データをダウンロード](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)してください。

* [Azure Storage アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal)するか、[既存のアカウントを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)してください。 

   帯域幅の料金を避けるため、リージョンは、Azure Cognitive Search と同じものを選択してください。
   
   [StorageV2 (汎用 v2)] のアカウントの種類を選択します。

* ストレージ サービス ページを開き、コンテナーを作成してください。 アクセス レベルとして "プライベート" を指定することをお勧めします。 コンテナーの名前は `clinicaltrialdataset` にします。

* コンテナーの **[アップロード]** をクリックして、最初の手順でダウンロードして解凍したサンプル ファイルをアップロードします。

* [Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

:::image type="content" source="media/search-get-started-postman/get-url-key.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" border="false":::

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="create-data-source-skillset-index-and-indexer"></a>データ ソース、スキルセット、インデックス、およびインデクサーを作成する

このセクションでは、Postman と指定されたコレクションを使用して、検索サービスのデータ ソース、スキルセット、インデックス、およびインデクサーを作成します。

1. Postman がない場合は、[こちらから Postman デスクトップ アプリをダウンロード](https://www.getpostman.com/)できます。
1. [Debug Sessions Postman コレクションをダウンロード](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)します
1. Postman を起動します
1. **[ファイル]**  >  **[新規作成]** で、インポートするコレクションを選択します。
1. コレクションがインポートされたら、アクションの一覧 ([...]) を展開します。
1. **[編集]** をクリックします。
1. searchService の名前を入力します (たとえば、エンドポイントが `https://mydemo.search.windows.net` の場合、サービス名は "`mydemo`" です)。
1. apiKey と、検索サービスのプライマリ キーまたはセカンダリ キーを入力します。
1. Azure Storage アカウントのキー ページにある storageConnectionString を入力します。
1. ストレージ アカウントに作成したコンテナーの containerName を入力します。

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="HTTP エンドポイントとアクセス キーを取得する":::

コレクションには、このセクションを完了するために使用される 4 種類の REST 呼び出しが含まれています。

最初の呼び出しでは、データ ソースが作成されます。 `clinical-trials-ds`. 2 つ目の呼び出しでは、スキルセット `clinical-trials-ss` が作成されます。 3 つ目の呼び出しでは、インデックスの `clinical-trials` が作成されます。 最後の 4 つ目の呼び出しでは、インデクサーの `clinical-trials-idxr` が作成されます。 コレクション内のすべての呼び出しが完了したら、Postman を閉じて Azure portal に戻ります。

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="HTTP エンドポイントとアクセス キーを取得する":::

## <a name="check-the-results"></a>結果を確認する

このスキルセットには、いくつかの一般的なエラーが含まれています。 このセクションでは、すべてのドキュメントが返されるように空のクエリを実行することで、複数のエラーが表示されます。 その後の手順で、デバッグ セッションを使用して問題を解決します。

1. Azure portal で、検索サービスに移動します。 
1. **[インデックス]** タブを選択します。 
1. `clinical-trials` インデックスを選択します
1. **[検索]** をクリックして、空のクエリを実行します。 

検索が終了した後、データを持たない 2 つのフィールド、"organizations" と "locations" がウィンドウの一覧に表示されます。 手順に従って、スキルセットによって生成されたすべての問題を検出します。

1. 検索サービスの [概要] ページに戻ります。
1. **[インデクサー]** タブを選択します。 
1. `clinical-trials-idxr` をクリックし、警告通知を選択します。 

プロジェクション出力フィールド マッピングには多くの問題があり、3 ページ目には複数の警告が表示されています。これは、1 つ以上のスキルの入力が無効であるためです。

検索サービスの [概要] 画面に戻ります。

## <a name="start-your-debug-session"></a>デバッグ セッションを開始する

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="HTTP エンドポイントとアクセス キーを取得する":::

1. [Debug sessions (preview)]\(デバッグ セッション (プレビュー)\) タブをクリックします。
1. [+NewDebugSession] を選択します
1. セッションに名前を指定します。 
1. セッションをストレージ アカウントに接続します。 
1. インデクサー名を指定します。 インデクサーには、データ ソース、スキルセット、およびインデックスへの参照があります。
1. コレクション内の最初のドキュメントとして、既定のドキュメント選択を受け入れます。 
1. セッションを**保存**します。 セッションを保存すると、スキルセットでの定義に従って AI エンリッチメント パイプラインが開始されます。

> [!Important]
> デバッグ セッションは、1 つのドキュメントでのみ機能します。 データ セット内の特定のドキュメントを選択できます。また、セッションでは既定で最初のドキュメントが対象になります。

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="HTTP エンドポイントとアクセス キーを取得する":::

デバッグ セッションの実行が完了すると、セッションでは既定で [AI Enrichments]\(AI エンリッチメント\) タブが表示され、スキル グラフが強調表示されます。

+ スキル グラフには、スキルセットの視覚的な階層と、上から下への実行順序が示されます。 グラフで左右に並んでいるスキルは、並列で実行されます。 グラフ内のスキルの色分けは、スキルセットで実行されているスキルの種類を示します。 この例では、緑色のスキルはテキストであり、赤のスキルはビジョンです。 グラフ内の個々のスキルをクリックすると、そのスキルのインスタンスの詳細が、セッション ウィンドウの右側のペインに表示されます。 スキルの設定、JSON エディター、実行の詳細、エラーと警告は、いずれも確認および編集できます。
+ エンリッチ処理されたデータ構造には、スキルによってソース ドキュメントのコンテンツから生成されるエンリッチメント ツリー内のノードの詳細が示されます。

[エラー/警告] タブには、前に表示された一覧よりもはるかに細かい一覧が表示されます。この一覧には、1 つのドキュメントのエラーの詳細のみが表示されるためです。 インデクサーによって表示される一覧と同様に、警告メッセージをクリックして、その警告の詳細を確認できます。

## <a name="fix-missing-skill-input-value"></a>不足しているスキル入力値を修正する

[エラー/警告] タブには、`Enrichment.NerSkillV2.#1` というラベルが付いている操作に関するエラーが表示されます。 このエラーの詳細では、スキル入力値 "/document/languageCode" に問題があることが示されています。 

1. [AI Enrichments]\(AI エンリッチメント\) タブに戻ります。
1. **スキル グラフ**をクリックします。
1. #1 というラベルのスキルをクリックすると、その詳細が右側のペインに表示されます。
1. "languageCode" の入力を見つけます。
1. 行の先頭にある **</>** 記号を選択し、式エバリュエーターを開きます。
1. **[評価]** ボタンをクリックして、この式の結果がエラーになることを確認します。 "languageCode" プロパティが有効な入力ではないことが確認されます。

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" に対応する、マッピングされたパスがありません。

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" という値が不足しています。

## <a name="fix-missing-skill-output-values"></a>不足しているスキル出力値を修正する

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" として一覧表示されているそれぞれの式が表示されます。

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" の **</>** で式エバリュエーターを開きます。

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" の **</>** で式エバリュエーターを開きます。

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" エンティティの **</>** で式エバリュエーターを開きます。

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" となるように編集します。
1. 右側のスキルの詳細ペインで **[保存]** をクリックします。
1. セッションのウィンドウ メニューで **[実行]** をクリックします。 これにより、ドキュメントを使用してスキルセットの別の実行が開始されます。

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="HTTP エンドポイントとアクセス キーを取得する":::

すべてのエラーが解決されました。

## <a name="commit-changes-to-the-skillset"></a>スキルセットに変更をコミットする

デバッグ セッションが開始されたとき、検索サービスによってスキルセットのコピーが作成されました。 これは、行われた変更が運用システムに影響しないようにするためです。 スキルセットのデバッグが完了したので、修正を運用システムにコミット (元のスキルセットを上書き) できるようになりました。 運用システムに影響を与えることなく、スキルセットを引き続き変更する場合は、デバッグ セッションを保存して、後で再度開くことができます。

1. メイン デバッグ セッション メニューの **[Commit changes]\(変更のコミット\)** をクリックします。
1. **[OK]** をクリックして、スキルセットの更新に同意します。
1. デバッグ セッションを閉じ、 **[インデクサー]** タブを選択します。
1. "clinical-trials-idxr" を開きます。
1. **[リセット]** をクリックします。
1. **[実行]** をクリックします。 **[OK]** をクリックして確定します。

インデクサーの実行が完了すると、[実行履歴] タブに緑色のチェックマークが表示され、最新の実行のタイム スタンプの横に "成功" という単語が表示されるはずです。変更が適用されたことを確認するには、次のようにします。

1. **インデクサー**を終了し、 **[インデックス]** タブを選択します。
1. "clinical-trials" インデックスを開き、[検索エクスプローラー] タブで **[検索]** をクリックします。
1. 結果ウィンドウで、組織と場所のエンティティに予期した値が設定されるようになったことが示されるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [スキルセットの詳細を学習する](./cognitive-search-working-with-skillsets.md)
> [インクリメンタル エンリッチメントとキャッシュの詳細を学習する](./cognitive-search-incremental-indexing-conceptual.md)