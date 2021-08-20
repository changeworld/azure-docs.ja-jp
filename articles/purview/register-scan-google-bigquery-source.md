---
title: Azure Purview での Google BigQuery プロジェクトの登録とスキャンの設定
description: この記事では、Azure Purview で Google BigQuery プロジェクトを登録し、スキャンを設定する方法について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/15/2021
ms.openlocfilehash: f7b134f14d190e7ef65eb2da897f9c106583d497
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393590"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>Google BigQuery ソースの登録とスキャン (プレビュー)

この記事では、Purview で Google BigQuery プロジェクトを登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

BigQuery ソースでは、BigQuery プロジェクトからメタデータを抽出するためのフル スキャンがサポートされており、データ資産間の系列がフェッチされます。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  セルフホステッド統合ランタイムが実行されている仮想マシン上に、[こちら](https://cloud.google.com/bigquery/providers/simba-drivers)から BigQuery の JDBC ドライバーを手動でダウンロードする必要があります。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

5.  サポートされている Google BigQuery のバージョンは 11.0.0 です

## <a name="register-a-google-bigquery-project"></a>Google BigQuery プロジェクトを登録する

データ カタログに新しい Google BigQuery プロジェクトを登録するには、次の手順を実行します。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[ソース]** を選択します。
3.  **[登録]** を選択します。
4.  [Register sources]\(ソースの登録\) で **[Google BigQuery]** を選択します。 **[続行]** を選択します。
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="BigQuery ソースの登録" border="true":::
   
[Register sources (Google BigQuery)]\(ソースの登録 (Google BigQuery)\) で、次の手順を実行します。

1.  データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2.  **ProjectID** を入力します。 これは完全修飾プロジェクト ID にする必要があります。たとえば、mydomain.com:myProject などです

3.  コレクションを選択するか、新しいものを作成します (省略可能)

4.  **[登録]** をクリックします。
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="BigQuery ソースを構成する" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、 [統合ランタイム] をクリックします。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2.  **[ソース]** に移動します。

3.  登録されている **BigQuery** プロジェクトを選択します。

4.  **[+ 新しいスキャン]** を選択します。

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    c.  **資格情報:** BigQuery の資格情報を構成するときは、次のことを確認してください。

    - 認証方法として **[基本認証]** を選択します
    - [ユーザー名] フィールドに、サービス アカウントのメール ID を指定します。 たとえば、example,\xyz\@developer.gserviceaccount.com などです
    - サービス アカウントの秘密キー ファイルを JSON 形式でキー コンテナーのシークレットに保存します。

    Google のクラウド プラットフォームから新しい秘密キーを作成するには、ナビゲーション メニューの [IAM & Admin]\(IAM と管理者\) -\> [Service Accounts]\(サービス アカウント\) をクリックし -\> プロジェクトを選択し -\> キーを作成するサービス アカウントのメール アドレスをクリックし -\> **[Keys]\(キー\)** タブをクリックし -\> **[Add key]\(キーの追加\)** ドロップダウン メニューをクリックし、[Create new key]\(新しいキーの作成\) を選択します。 次に、JSON 形式を選択します。

      > [!Note]
      > スキャン プロセスが実行されている間、秘密キーの内容は VM 上の一時ファイルに保存されます。 この一時ファイルは、スキャンが正常に完了すると削除されます。 スキャンに失敗した場合は、成功するまで再試行が繰り返されます。 SHIR が実行されている VM 上で、アクセスが適切に制限されていることを確認してください。**

    資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    d.  **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります    
    > [!Note]
    > このドライバーには、VM 内のすべてのアカウントからアクセスできるようにしてください。

    e.  **データセット**: インポートする BigQuery データセットの一覧を指定します。 たとえば、dataset1; dataset2 などです。 一覧が空の場合は、使用可能なすべてのデータセットがインポートされます。
        SQL LIKE 式の構文を使用したデータセット名のパターンとして、% の使用も許容されます。 

    例: A%; %B; %C%; D
    - A で始まる、または
    - B で終わる、または
    - C を含む、または
    - D と等しい    
NOT および特殊文字の使用は許容されません。
    
    f.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンされる Google BigQuery プロジェクトのサイズによって変わります。
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="BigQuery ソースをスキャンする" border="true":::

6.  **[テスト接続]** をクリックします。

7.  **[続行]** をクリックします。

8.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

9.  スキャンを確認し、 **[保存および実行]** をクリックします。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)