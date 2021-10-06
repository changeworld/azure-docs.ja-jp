---
title: Cassandra をソースとして登録してスキャンを設定する
description: この記事では、Azure Purview に Cassandra サーバーを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: dcc0ef65654d8100a1c96cdb0c84a6ac758587a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212735"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Cassandra ソースの登録とスキャン (プレビュー)

この記事では、Azure Purview に Cassandra サーバーを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Cassandra ソースではフル スキャンがサポートされており、Cassandra サーバーからメタデータを抽出し、データ資産間の系列をフェッチできます。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  サポートされている Cassandra サーバーのバージョンは 3.x から 4.x です

## <a name="register-a-cassandra-server"></a>Cassandra サーバーを登録する

新しい Cassandra サーバーをデータ カタログに登録するには、次のようにします。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[Data Map]** を選択します。
3.  **[登録]** を選択します。
4.  [ソースの登録] で、 **[Cassandra]** を選択します。 **[続行]** を選択します。
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Cassandra ソースの登録" border="true":::
   
[ソースの登録 (Cassandra)] 画面で、次の手順を実行します。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2. Cassandra サーバーが実行中のサーバー アドレスを **[ホスト]** フィールドに入力します。 例: 20.190.193.10

3. Cassandra サーバーによって使用されるポートを **[ポート]** フィールドに入力します。
4. コレクションを選択するか、新しいものを作成します (省略可能)

5.  **[登録]** を選択します。
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Cassandra ソースの構成" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2.  **[ソース]** に移動します。

3.  登録されている **Cassandra** サーバーを選択します。

4.  **[+ 新しいスキャン]** を選択します。

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    c.  **[資格情報]** : Cassandra の資格情報を構成するときは、次のことを確認してください。

    - 認証方法として **[基本認証]** を選択します
    - [ユーザー名] フィールドで、接続を行うユーザーのユーザー名を指定します。 
    - キー コンテナーのシークレットに、接続を行う Cassandra ユーザーのパスワードを保存します

    資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    d.  **[Keyspaces]\(キースペース\)** : インポートする Cassandra キースペースの一覧を指定します。 複数のキースペースはセミコロンで区切る必要があります。 たとえば、keyspace1; keyspace2 のようになります。 一覧が空の場合は、使用可能なすべてのキースペースがインポートされます。
    SQL LIKE 式の構文を使用したキースペース名のパターンとして、% の使用も許容されます。 

    例: A%; %B; %C%; D
    - A で始まる、または
    - B で終わる、または
    - C を含む、または
    - D と等しい    
NOT および特殊文字の使用は許容されません。
    
    f. **[Secure Sockets Layer (SSL) を使用する]** : Cassandra サーバーに接続するときに Secure Sockets Layer (SSL) を使用する必要があるかどうかを通知するために、[True] または [False] を選択します。 既定では、この値は Falseに設定されます。

    g. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Cassandra サーバーのサイズによって異なります。
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra ソースのスキャン" border="true":::

6.  **[接続テスト]** を選択します。

7.  **[続行]** を選択します。

8.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

9.  自分のスキャンを確認し、 **[保存および実行]** を選択します。

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