---
title: Cassandra ソースの登録とスキャン
description: この記事では、Azure Purview に Cassandra サーバーを登録し、スキャンを設定してメタデータを抽出する方法について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5f8feffebff71c25f2a0d62d775894e7c4431615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005318"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Cassandra ソースの登録とスキャン (プレビュー)

この記事では、Azure Purview に Cassandra サーバーを登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Purview を使用すると、Cassandra でフル スキャンを実行して、データ資産間のメタデータと系列を抽出できます。 

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムをインストールしていない場合は、[こちらから](https://www.microsoft.com/download/details.aspx?id=30679)ダウンロードします。

4.  お使いの Cassandra サーバーがバージョン 3.*x* または 4.*x* であることを確認します。

## <a name="register-a-cassandra-server"></a>Cassandra サーバーを登録する

新しい Cassandra サーバーをデータ カタログに登録するには:

1.  ご自分の Purview アカウントに移動します。
2.  左側のペインで **[Data Map]** を選択します。
3.  **[登録]** を選択します。
4.  **[ソースの登録]** 画面で **[Cassandra]** を選択し、 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="[ソースの登録] 画面を示すスクリーンショット。" border="true":::
   
1. **[ソースの登録 (Cassandra)]** 画面で:

   1. **[名前]** を入力します。 データ ソースでは、この名前がカタログで使用されます。

   2. **[ホスト]** ボックスに、Cassandra サーバーが実行されているサーバー アドレスを入力します。 例: 20.190.193.10。

   3. **[ポート]** ボックスに、Cassandra サーバーで使用されるポートを入力します。
   4. コレクションを選択するか新規作成します (省略可能)。
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="[ソースの登録 (Cassandra)] 画面を示すスクリーンショット。" border="true":::
   5.  **[登録]** を選択します。


## <a name="create-and-run-a-scan"></a>スキャンを作成して実行する

新しいスキャンを作成して実行するには:

1.  管理センターで、 **[統合ランタイム]** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちらの手順を実行して、セルフホステッド統合ランタイムを設定します](./manage-integration-runtimes.md)。
    

2.  **[ソース]** に移動します。

3.  登録されている Cassandra サーバーを選択します。

4.  **[新しいスキャン]** を選択します。

5.  以下の詳細を指定します。

    a.  **[名前]** : スキャンの名前を指定します。

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    c.  **[資格情報]** : Cassandra の資格情報を構成する場合は、必ず次のことを行ってください。

    - 認証方法として **[基本認証]** を選択します。
    - **[ユーザー名]** ボックスに、接続するユーザーの名前を指定します。 
    - キー コンテナーのシークレットに、接続する Cassandra ユーザーのパスワードを保存します。

    詳細については、[Purview でのソース認証用の資格情報](manage-credentials.md)に関するページを参照してください。

    d.  **[Keyspaces]\(キースペース\)** : インポートする Cassandra キースペースの一覧を指定します。 複数のキースペースは、セミコロンで区切る必要があります。 たとえば、keyspace1; keyspace2 のようになります。 一覧が空の場合は、使用可能なすべてのキースペースがインポートされます。
    
    % を含む SQL LIKE 式構文を使用するキースペース名パターンを使用できます。 

    例: A%; %B; %C%; D

    この式は、次を意味します。
    - A で始まる、または
    - B で終わる、または
    - C が含まれている、または
    - D と等しい    

    NOT または特殊文字は使用できません。
    
    e. **[Secure Sockets Layer (SSL) を使用する]** : Cassandra サーバーに接続するときに Secure Sockets Layer (SSL) を使用するかどうかを指定するには、**True** または **False** を選択します。 既定では、このオプションは **False** に設定されています。

    f. **[Maximum memory available]\(使用可能な最大メモリ\)** : スキャン プロセスで使用される、お使いの VM で使用可能な最大メモリ (GB 単位) を指定します。 この値は、スキャンする Cassandra サーバーのサイズによって異なります。
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra ソースのスキャン" border="true":::

6.  **[接続テスト]** を選択します。

7.  **[続行]** を選択します。

8.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

9.  自分のスキャンを確認し、 **[保存および実行]** を選択します。

## <a name="view-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を表示する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションで **[データ ソース]** を選択します。

2. スキャンを表示するデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

   この結果のページには、以前のすべてのスキャン実行が、それぞれのメトリックとステータスとともに表示されます。 
   また、次も示されます。 
   - スキャンはスケジュールされたものか、または手動だったか。 
   - 分類が適用された資産の数。 
   - 検出された総資産の数。 
   - スキャンの開始および終了時刻。
   - スキャンの期間。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには:

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションで **[データ ソース]** を選択します。 次に、スキャンを管理するデータ ソースを選択します。

2. 管理するスキャンを選択します。 
   - スキャンを編集するには、 **[編集]** を選択します。

   - **[削除]** を選択することで、スキャンを削除できます。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
