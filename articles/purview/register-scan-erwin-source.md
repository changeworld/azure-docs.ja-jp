---
title: Azure Purview に Erwin Mart を登録してスキャンを設定する
description: この記事では、Azure Purview に Erwin Mart を登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: 55ec355cf28f08618b1c51670b876f93dee69cc8
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393655"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>erwin Mart サーバーの登録とスキャン (プレビュー)

この記事では、Azure Purview に Erwin Mart サーバーを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

erwin ソースを使用すると、Erwin Mart サーバーからメタデータを抽出するためのフル スキャンがサポートされます。 メタデータの内容は次のとおりです。

1.  エンティティ、属性、ドメインを持つ論理限定モデル、または
2.  テーブル、列、データ型を持つ物理限定モデル、または
3.  論理または物理モデル

このデータ ソースにより、データ資産間の系列も取得されます。

> [!Note]
> ソースとしての Erwin は、現在プレビューでサポートされています。 このソースを登録し、非運用環境の Purview アカウントにスキャンを設定して、フィードバックをお寄せください。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「  
    [セルフホステッド統合ランタイムを作成して構成する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

    > [!Note]
    > セルフホステッド統合ランタイムは、必ず erwin Mart インスタンスが実行されている VM で実行してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイムが実行されている VM に \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 インストールされていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  サポートされている erwin Mart のバージョンは 9.x から 2021 までです

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

erwin Mart ソースでサポートされている認証は、ユーザー名とパスワードの形式での **サーバー認証** のみです

## <a name="register-an-erwin-mart"></a>erwin Mart を登録する

新しい erwin Mart をデータ カタログに登録するには、次のようにします。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[ソース]** を選択します。
3.  **[登録]** を選択します。
4.  [Register sources]\(ソースの登録\) で、 **[erwin]** を選択します。 **[続行].** を選択します。
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="erwin ソースの登録" border="true":::
    
ソースの登録 (erwin) 画面で、次のようにします。

1.  データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2.  erwin Mart の **サーバー名** を入力します。 これは、erwin Mart サーバーへの接続に使用されるネットワーク ホスト名です。 たとえば、localhost です

3.  erwin Mart に接続するときに使用される **ポート** 番号を入力します。 この値は既定では 18170 です。

4.  **アプリケーション名** を入力します

    >[!Note]
    > 上記の詳細は、erwin Data Modeler に移動することで確認できます。 [Mart] -\> [接続] をクリックして、サーバー名、ポート、アプリケーション名に関連した詳細を確認します。

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="erwin の詳細の確認" border="true":::
    
5.  コレクションを選択するか、新しいものを作成します (省略可能)

6.  データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="ソースの登録" border="true":::
    

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、 [統合ランタイム] をクリックします。 erwin Mart インスタンスが実行されている VM にセルフホステッド統合ランタイムが設定されている必要があります。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2.  **[ソース]** に移動します。

3.  登録した **erwin** Mart を選択します。

4.  **[+ 新しいスキャン]** を選択します。

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    c.  **[サーバー名]、[ポート]** および **[アプリケーション名]** は、登録時に入力した値に基づいて自動的に設定されます。

    d.  **資格情報:** erwin Mart サーバーに接続するように構成された資格情報を選択します。 資格情報を作成する際は、次の点にご注意ください。
    - 認証方法として **[基本認証]** を選択します
    - [ユーザー名] フィールドに、erwin Mart サーバーのユーザー名を入力します。
    - サーバー認証用のユーザー パスワードをキー コンテナーのシークレットに保存します。

    資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    e.  **インターネット インフォメーション サービス (IIS) を使用する** - erwin Mart サーバーに接続するときに Microsoft インターネット インフォメーション サービス (IIS) を使用する必要があるかどうかを通知するために、[True] または [False] を選択します。 既定では、この値は Falseに設定されます。

    f.  **Secure Sockets Layer (SSL) を使用する** - erwin Mart サーバーに接続するときに Secure Sockets Layer (SSL) を使用する必要があるかどうかを通知するために、[True] または [False] を選択します。 既定では、この値は Falseに設定されます。

    > [!Note]
    > このパラメーターは、erwin Mart バージョン 9.1 以降にのみ適用されます。

    g.  **ブラウズ モード** - erwin Mart を参照するモードを選択します。 使用できるオプションは、"ライブラリとモデル" または "ライブラリのみ" です。

    h.  **モデル** - セミコロンで区切られた erwin モデル ロケーター文字列のリストを指定して、スキャンのスコープを設定します。 例: mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    i.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする erwin Mart のサイズによって異なります。
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="スキャンのトリガー" border="true":::
   

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
