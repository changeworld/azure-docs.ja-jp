---
title: Azure Purview に Looker を登録してスキャンを設定する
description: この記事では、Azure Purview に Looker ソースを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: ed524382bf1fcc0c93fc61dd1c0450d69a16b74c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749411"
---
# <a name="register-and-scan-lookerpreview"></a>Looker の登録とスキャン (プレビュー)

この記事では、Azure Purview に Looker サーバーを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Looker ソースでは、Looker サーバーからメタデータを抽出するフルスキャンがサポートされています。 Looker サーバーからメタデータがインポートされました。これには、データベース接続、LookML モデル、関連付けられているレポート (Look とダッシュボード) が含まれます。 このデータ ソースにより、データ資産間の系列もフェッチされます。

> [!Note]
> ソースとしての Looker は、現在、プライベート プレビューでサポートされています。 このソースを登録し、非運用環境の Purview アカウントにスキャンを設定して、フィードバックをお寄せください。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイムが実行されている VM に \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 インストールしていない場合は、\'[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  サポートされている Looker サーバーのバージョンは 7.2 です

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

Looker サーバーに接続するには、API3 キーが必要です。 API3 キーは、パブリック client_id とプライベート client_secret で構成され、OAuth2 認証パターンに従います。

## <a name="register-a-looker-server"></a>Looker サーバーを登録する

新しい Looker サーバーをデータ カタログに登録するには、次のようにします。

1. ご自分の Purview アカウントに移動します。
2. 左側のナビゲーションで **[Data Map]** を選択します。
3. **[登録]** を選択します。
4. [ソースの登録] で、 **[Looker]** を選択します。 **[続行]** を選択します。
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="looker ソースの登録" border="true":::


[ソースの登録 (Looker)] 画面で、次の操作を行います。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2. **[サーバー API URL]** フィールドに Looker API URL を入力します。 API 要求の既定のポートは、ポート 19999 です。 また、すべての Looker API エンドポイントには HTTPS 接続が必要です。 たとえば、'https://azurepurview.cloud.looker.com ' です

3. コレクションを選択するか、新しいものを作成します (省略可能)

4. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="looker ソースのスキャン" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、 [統合ランタイム] をクリックします。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2. **[ソース]** に移動します。

3. 登録されている **Looker** サーバーを選択します。

4. **[+ 新しいスキャン]** を選択します。

5. 次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    c.  **サーバー API の URL** は、登録時に入力した値に基づいて自動的に設定されます。

    d.  **資格情報:** Looker 資格情報を構成するときは、次のことを確認してください。

    - 認証方法として **[基本認証]** を選択します
    - Looker API3 キーのクライアント ID を [ユーザー名] フィールドに入力します
    - Looker API3 キーのクライアント シークレットをキー コンテナーのシークレットに保存します。

    **注:** クライアント ID とクライアント シークレットにアクセスするには、[Looker] -\> [管理] -\> [ユーザー] に移動 -\> ユーザーの **[編集]** をクリック -\> **[EditKeys] をクリック**  -\> クライアント ID とクライアント シークレットを使用するか、新しいシークレットを作成します。
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="looker の詳細を取得する" border="true":::
    

    資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    e.  **プロジェクト フィルター** -- セミコロンで区切られた Looker プロジェクトの一覧を提供することによって、スキャンの範囲を指定します。 このオプションは、親プロジェクトによって look とダッシュボードを選択するために使用されます。

    f.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする erwin Mart のサイズによって異なります。

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="スキャンのトリガー" border="true":::

6. **[テスト接続]** をクリックします。

7. **[続行]** をクリックします。

8. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

9. スキャンを確認し、 **[保存および実行]** をクリックします。

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
