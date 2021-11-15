---
title: Google BigQuery プロジェクトに接続して管理する
description: このガイドでは、Azure Purview で Google BigQuery プロジェクトに接続する方法と、Purview の機能を使用して Google BigQuery ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f28de0520e4b69970549f7258a5a41dd109d2b48
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852581"
---
# <a name="connect-to-and-manage-google-bigquery-projects-in-azure-purview-preview"></a>Azure Purview で Google BigQuery プロジェクトに接続して管理する (プレビュー)

この記事では、Azure Purview で、Google BigQuery プロジェクトを登録する方法と、Google BigQuery を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

> [!IMPORTANT]
> ソースとしての Google BigQuery は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [あり](how-to-lineage-google-bigquery.md)|

> [!Important]
> サポートされている Google BigQuery のバージョンは 11.0.0 です。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* セルフホステッド統合ランタイムが実行されているマシン上に、BigQuery の JDBC ドライバーをダウンロードしてインストールします。 ドライバーは[こちら](https://cloud.google.com/bigquery/providers/simba-drivers)で見つけることができます。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Google BigQuery プロジェクトを登録する方法について説明します。

### <a name="steps-to-register"></a>登録する手順

1. ご自分の Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します。
1. [Register sources]\(ソースの登録\) で **[Google BigQuery]** を選択します。 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="BigQuery ソースを登録する" border="true":::

[Register sources (Google BigQuery)]\(ソースの登録 (Google BigQuery)\) で、次の手順を実行します。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. **ProjectID** を入力します。 これは完全修飾プロジェクト ID にする必要があります。 たとえば、mydomain.com:myProject などです。

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. **[登録]** を選択します。

    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="BigQuery ソースを構成する" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Google BigQuery プロジェクトをスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用します。

1. **[ソース]** に移動します。

1. 登録されている **BigQuery** プロジェクトを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    1. **資格情報:** BigQuery の資格情報を構成するときは、次のことを確認してください。

        * 認証方法として **[基本認証]** を選択します
        * [ユーザー名] フィールドに、サービス アカウントのメール ID を指定します。 たとえば、`xyz\@developer.gserviceaccount.com` のように指定します。
        * 次の手順に従って、秘密キーを生成し、JSON をコピーして、Key Vault シークレットの値として格納します。

        Google のクラウド プラットフォームから新しい秘密キーを作成するには、次のようにします。
        1. ナビゲーション メニューで、[IAM と管理]、[サービス アカウント] の順に選択し、プロジェクトを選択します。 
        1. キーを作成するサービス アカウントのメール アドレスを選択します。
        1. **[キー]** タブを選択します。
        1. **[キーの追加]** ドロップダウン メニューを選択し、[新しいキーの作成] を選択します。 
        1. JSON 形式を選択します。

          > [!Note]
          > スキャン プロセスが実行されている間、秘密キーの内容は VM 上の一時ファイルに保存されます。 この一時ファイルは、スキャンが正常に完了すると削除されます。 スキャンに失敗した場合は、成功するまで再試行が繰り返されます。 SHIR が実行されている VM 上で、アクセスが適切に制限されていることを確認してください。

        資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    1. **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。 

        > [!Note]
        > このドライバーには、VM 内のすべてのアカウントからアクセスできるようにしてください。

    1. **データセット**: インポートする BigQuery データセットの一覧を指定します。
        たとえば、dataset1; dataset2 などです。 一覧が空の場合は、使用可能なすべてのデータセットがインポートされます。
        SQL LIKE 式の構文を使用したデータセット名のパターンとして、% の使用も許容されます。

        例:  A%; %B; %C%; D
        * A で始まる、または
        * B で終わる、または
        * C を含む、または
        * D と等しい

        NOT および特殊文字の使用は許容されません。

    1. **[使用可能な最大メモリ]:** スキャン プロセスで使用される、お使いの VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンされる Google BigQuery プロジェクトのサイズによって変わります。

        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="BigQuery ソースをスキャンする" border="true":::

1. **[接続テスト]** を選択します。

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
