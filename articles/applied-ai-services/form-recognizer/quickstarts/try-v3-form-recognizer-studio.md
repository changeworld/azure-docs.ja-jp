---
title: 'クイック スタート: Form Recognizer Studio | プレビュー'
titleSuffix: Azure Applied AI Services
description: Form Recognizer Studio を使用したフォームとドキュメントの処理、データ抽出、分析 (プレビュー)
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc4f54a6ab42089068c3db8b7fe64bfada59adf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290687"
---
# <a name="get-started-form-recognizer-studio--preview"></a>概要: Form Recognizer Studio | プレビュー

>[!NOTE]
> Form Recognizer Studio は現在、パブリック プレビューの段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。 

[Form Recognizer Studio プレビュー](https://formrecognizer.appliedai.azure.com/)は、アプリケーションの Form Recognizer サービスの機能を視覚的に探索、理解、および統合するためのオンライン ツールです。 サンプル ドキュメントまたは独自のドキュメントを使用して、事前トレーニング済みのモデルの探索を開始します。 [Python SDK プレビュー](try-v3-python-sdk.md)や他のクイックスタートを使用して、カスタム フォーム モデルを構築するプロジェクトを作成し、アプリケーションでモデルを参照します。

## <a name="migrating-from-the-sample-labeling-tool"></a>サンプル ラベル付けツールからの移行

[サンプル ラベル付けツール](try-sample-label-tool.md)を以前に使用したことがある場合は、[**Studio プレビューにサインインする**](try-v3-form-recognizer-studio.md#sign-into-the-form-recognizer-studio-preview)ための前提条件をスキップして、既存の Azure アカウントと Form Recognizer または Cognitive Services リソースを Studio で使用します。 

既存のカスタム プロジェクトを Studio に移行するには、[**カスタム モデルの使用を開始する**](try-v3-form-recognizer-studio.md#custom-model-basics)方法に関するセクションに進んで新しいプロジェクトを作成し、Azure でアクセスできることを前提に、同じ Azure Blob ストレージの場所を指定します。 新しいプロジェクトを構成すると、ラベル付けとトレーニングに使用されるすべてのドキュメントと中間ファイルが Studio に読み込まれます。

## <a name="minimum-prerequisites-for-new-users"></a>新しいユーザー向けの最小限の前提条件

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。
* [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) または [**Cognitive Services マルチサービス**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) リソース。

## <a name="additional-prerequisites-for-custom-projects"></a>カスタム プロジェクトに必要な追加の前提条件

Azure アカウントと、Form Recognizer または Cognitive Services のリソースに加えて、次が必要になります。

### <a name="azure-blob-storage-container"></a>Azure BLOB Storage コンテナー

**標準パフォーマンスの** [**Azure Blob Storage アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。 コンテナーを含む Azure ストレージ アカウントを作成する方法がわからない場合は、次のクイックスタートに従ってください。

  * [**ストレージ アカウントを作成する**](../../../storage/common/storage-account-create.md)。 ストレージ アカウントを作成するときに、 **[インスタンスの詳細] → [パフォーマンス]** フィールドで必ず **[Standard]** を選択してください。
  * [**コンテナーを作成する**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 コンテナーを作成するときに、 **[新しいコンテナー]** ウィンドウの **[パブリック アクセス レベル]** フィールドを **[コンテナー]** (コンテナーと BLOB の匿名読み取りアクセス) に設定します。

### <a name="configure-cors"></a>CORS を構成する

[CORS (クロス オリジン リソース共有)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) に Form Recognizer Studio から アクセスするには、Azure ストレージ アカウントに構成する必要があります。 Azure portal で CORS を構成するには、ストレージ アカウントの [CORS] ブレードにアクセスする必要があります。

:::image type="content" source="../media/quickstarts/cors-updated-image.png" alt-text="ストレージ アカウントの CORS 構成を示すスクリーンショット。":::

1. ストレージ アカウントの [CORS] ブレードを選択します。
2. まず、Blob service に新しい CORS エントリを作成します。
3. **[許可されたオリジン]** は、 **https://formrecognizer.appliedai.azure.com** に設定します。
4. **[許可されたメソッド]** で使用可能な 8 つすべてのオプションを選択します。
5. 各フィールドに * を入力して、 **[許可されたヘッダー]** と **[公開されるヘッダー]** をすべて承認します。
6. **[Max Age]\(最長有効期間\)** 120 秒、または任意の許容値に設定します。
7. ページ上部にある [保存] ボタンをクリックして、変更を保存します。

これで、CORS は、Form Recognizer Studio からストレージ アカウントを使用するように構成されました。

### <a name="sample-documents-set"></a>サンプル ドキュメント セット

1. [Azure portal](https://ms.portal.azure.com/#home) で、次のように移動します。**お使いのストレージ アカウント** → **[データ ストレージ]** → **[コンテナー]**

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="スクリーンショット: Azure portal の [データ ストレージ] メニュー。":::

1. 一覧から **コンテナー** を選択します。

1. ページ上部のメニューから **[アップロード]** を選択します。

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="スクリーンショット: Azure portal のコンテナーの [アップロード] ボタン。":::

1. **[BLOB のアップロード]** ウィンドウが表示されます。

1. アップロードするファイルを選択します。

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="スクリーンショット: Azure portal の [BLOB のアップロード] ウィンドウ。":::

> [!NOTE]
> 既定では、Studio ではコンテナーのルートにあるフォーム ドキュメントが使用されます。 ただし、カスタム フォーム プロジェクトの作成手順で指定されている場合は、フォルダーに編成されたデータを使用できます。 *「* [**データをサブフォルダーに整理する**](../build-training-data-set.md#organize-your-data-in-subfolders-optional)」を参照してください

## <a name="sign-into-the-form-recognizer-studio-preview"></a>Form Recognizer Studio プレビューにサインインする

前提条件を満たしたら、[Form Recognizer Studio プレビュー](https://formrecognizer.appliedai.azure.com)に移動します。

1. Studio ホーム ページから Form Recognizer サービス機能を選択します。

1. お使いの Azure サブスクリプション、リソース グループ、リソースを選択します。 (リソースは、上部メニューの [設定] でいつでも変更できます。)

1. 選択内容を確認して確定します。

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="Form Recognizer Studio の概要の例":::

## <a name="layout"></a>Layout

[レイアウト] ビューで:

1. [分析] コマンドを選択してサンプル ドキュメントのレイアウト分析を実行するか、[追加] コマンドを使用してドキュメントを試します。

1. 強調表示された抽出テキスト、抽出されたテーブルの場所を示すテーブル アイコン、および強調表示された選択マークを確認します。

1. ドキュメント ビューの拡大/縮小と回転を行うには、画面の下部にあるコントロールを使用します。

1. テキスト、テーブル、および選択マークのレイヤーを表示および非表示にし、一度に 1 つずつレイヤーにフォーカスを設定します。

1. 出力セクションの [結果] タブで、JSON 出力を参照して、サービス応答の形式を理解します。 統合をジャンプスタートするためにコピーしてダウンロードします。

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="Form Recognizer レイアウトの例":::

## <a name="prebuilt-models"></a>事前構築済みのモデル

複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [🆕**一般的なドキュメント**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document): テキスト、テーブル、構造、キーと値のペア、名前付きエンティティを分析および抽出します。
* [**請求書**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice): 請求書からテキスト、選択マーク、テーブル、キーと値のペア、キー情報を抽出します。
* [**レシート**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard): 名刺からテキストとキー情報を抽出します。

[事前構築済み] ビューで:

1. Studio ホームから、事前構築済みモデルのいずれかを選択します。 この例では、請求書モデルを使用しています。

1. [分析] コマンドを選択してサンプル ドキュメントの分析を実行するか、[追加] コマンドを使用して請求書を試します。

1. 視覚化セクションで、強調表示されているフィールドと値、および請求書明細項目を確認します。 抽出されたテキストとテーブルもすべて表示されます。

1. 出力セクションの [フィールド] タブで、一覧表示されているフィールドと値をメモし、テーブルのような形式で表示する品目を選択します。

1. 出力セクションの [結果] タブで、JSON 出力を参照して、サービス応答の形式を理解します。 統合をジャンプスタートするためにコピーしてダウンロードします。

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Form Recognizer の事前構築済みの例":::

## <a name="custom-model-basics"></a>カスタム モデルの基本

### <a name="getting-started"></a>作業の開始

カスタム モデルを作成するには、まずプロジェクトを構成することから始めます。

1. Studio ホームから [[カスタム フォーム] プロジェクト](https://formrecognizer.appliedai.azure.com/studio/customform/projects)を選択して、[カスタム フォーム] ホーム ページを開きます。

1. [プロジェクトの作成] コマンドを使用して、新しいプロジェクト構成ウィザードを開始します。

1. プロジェクトの詳細を入力し、Azure サブスクリプションとリソース、および自分のデータが含まれている Azure Blob Storage コンテナーを選択します。

1. 設定を確認して送信し、プロジェクトを作成します。

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="Form Recognizer カスタム プロジェクトの概要の例":::

### <a name="basic-flow"></a>基本のフロー

プロジェクトの作成手順を終えたら、カスタム モデル フェーズで次の手順を実行します。

1. ラベル付けビューから、抽出するラベルとその種類を定義します。

1. ドキュメント内のテキストを選択し、ドロップダウン リストまたは [ラベル] ペインからラベルを選択します。

1. さらに 4 つのドキュメントにラベルを付け、少なくとも 5 つのドキュメントにラベルを付けます。

1. [トレーニング] コマンドを選択し、モデル名と説明を入力してカスタム モデルのトレーニングを開始します。

1. モデルの準備ができたら、[テスト] コマンドを使用してテスト ドキュメントでそのモデルを検証し、結果を確認します。

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="Form Recognizer カスタム プロジェクトの基本的なワークフローの例":::

### <a name="other-features"></a>その他の機能

さらに、左側の [モデル] タブを使用して、すべてのモデルを表示します。 リスト ビューからモデルを選択して、次のアクションを実行します。

1. リスト ビューからモデルをテストします。

1. [削除] コマンドを使用して、不要なモデルを削除します。

1. オフラインで表示するため、モデルの詳細をダウンロードします。

1. 複数のモデルを選択し、アプリケーションで使用する新しいモデルに構成します。

## <a name="labeling-as-tables"></a>テーブルとしてのラベル付け

カスタム モデルを作成しているときに、ドキュメントからデータ コレクションを抽出する必要がある場合があります。 これらは、いくつかの形式で表示される場合があります。 ビジュアル パターンとしてテーブルを使用した場合、

* 特定のフィールド (列) セットに対する値 (行) の動的または可変の値の数

* 特定のフィールド (列や行) セットに対する特定の値のコレクション

### <a name="label-as-dynamic-table"></a>動的テーブルとしてのラベル付け

動的テーブルを使用して、特定のフィールド (列) セットの値 (行) の変数を抽出します。

1. 新しい "テーブル" 型ラベルを追加し、"動的テーブル" 型を選択して、ラベルに名前を付けます。

1. 必要な列 (フィールド) と行 (データの場合) の数を追加します。

1. ページ内のテキストを選択し、テキストに割り当てるセルを選択します。 すべてのドキュメント内のすべてのページのすべての行と列に対して繰り返します。

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="動的テーブルとしての Form Recognizer のラベル付けの例":::

### <a name="label-as-fixed-table"></a>固定テーブルとしてのラベル付け

固定テーブルを使用して、特定のフィールド (列や行) セットに対する特定の値のコレクションを抽出します。

1. 新しい "テーブル" 型ラベルを作成し、"固定テーブル" 型を選択して、それに名前を付けます。

1. 2 つのフィールド セットに対応させる必要がある列と行の数を追加します。

1. ページ内のテキストを選択し、テキストに割り当てるセルを選択します。 他のドキュメントに対して同じ手順を繰り返します。

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="固定テーブルとしての Form Recognizer のラベル付けの例":::

## <a name="labeling-for-signature-detection"></a>シグネチャ検出のラベル付け

シグネチャ検出にラベル付けするには、次の手順を実行します。

1. ラベル付けビューで、新しい "シグネチャ" 型のラベルを作成し、それに名前を付けます。

1. [領域] コマンドを使用して、シグネチャの予想される位置に四角形の領域を作成します。

1. 描画領域を選択し、シグネチャ型のラベルを選択して描画領域に割り当てます。 他のドキュメントに対して同じ手順を繰り返します。

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="シグネチャ検出の Form Recognizer のラベル付けの例":::

## <a name="next-steps"></a>次の手順

* [**Form Recognizer v3.0 の移行ガイド**](../v3-migration-guide.md)に関するページで、REST API の以前のバージョンとの違いを確認してください。
* [**プレビュー SDK クイックスタート**](try-v3-python-sdk.md)に関するページを探索し、新しい SDK を使用してアプリケーションでプレビュー機能を試してください。
* [**プレビュー REST API クイックスタート**](try-v3-rest-api.md)に関するページを参照し、新しい REST API を使用してプレビュー機能を試してください。

[Form Recognizer Studio (プレビュー) の使用を開始する](https://formrecognizer.appliedai.azure.com)。
