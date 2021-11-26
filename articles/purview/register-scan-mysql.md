---
title: MySQL に接続して管理する
description: このガイドでは、Azure Purview で MySQL に接続する方法と、Purview の機能を使用して MySQL ソースをスキャンして管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f7d2dbdab590d2236032d3dd96a2008e2aa79c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553840"
---
# <a name="connect-to-and-manage-mysql-in-azure-purview-preview"></a>Azure Purview で MySQL に接続して管理する (プレビュー)

この記事では、Azure Purview で、MySQL を登録する方法、および MySQL を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

> [!IMPORTANT]
> ソースとしての MySQL は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [はい](#scan)| いいえ | いいえ | いいえ | いいえ| はい|

サポートされている MySQL サーバーのバージョンは、5.7 から 8.x です。

MySQL ソースをスキャンするとき、Purview では以下がサポートされます。

- MySQL サーバー、データベース、テーブル、ビュー、テーブル/ビュー列を含むメタデータの抽出。
- テーブルとビュー間の資産リレーションシップの系列のフェッチ。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳しくは、「[Azure Purview のアクセス許可](catalog-permissions.md)」ページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* MySQL ユーザーは、メタデータを含むターゲット MySQL スキーマごとに、SELECT、SHOW VIEW、EXECUTE アクセス許可を持っている必要があります。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に MySQL を登録する方法について説明します。

### <a name="steps-to-register"></a>登録する手順

新しい MySQL ソースをデータ カタログに登録するには、次のようにします。

1. [Purview Studio](https://web.purview.azure.com/resource/) で Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. [ソースの登録] で、 **[MySQL]** を選択します。 **[続行]** をクリックします。

**[Register sources (MySQL)]\(ソースの登録 (MySQL)\)** 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. MySQL ソースに接続するために、**サーバー** 名を入力します。 これは、次のいずれかにすることができます。
    * データベース サーバーに接続するために使用するホスト名。 例: `MyDatabaseServer.com`
    * IP アドレス。 例: `192.169.1.2`
    * 完全修飾の JDBC 接続文字列。 たとえば、次のように入力します。

        ```
        jdbc:mysql://COMPUTER_NAME_OR_IP/DATABASE_NAME
        ```

1. データベース サーバーに接続するために使用する **ポート** を入力します (MySQL の場合、既定では 3306)。

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-mysql/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って MySQL をスキャンし、資産を自動的に識別し、データを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="authentication-for-a-scan"></a>スキャンの認証

MySQL ソースでサポートされている認証の種類は、**基本認証** です。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します。

1. **[ソース]** に移動します。

1. 登録した MySQL ソースを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。
        * 資格情報を作成するときに **[基本認証]** を選択します。
        * ユーザー名入力フィールドに、データベース サーバーに接続するために使用するユーザー名を入力します。
        * データベース サーバーに接続するために使用するユーザー パスワードを秘密鍵に保存します。

    1. **[データベース]** : インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 たとえば、「 `schema1; schema2` 」のように入力します。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。 リストが空の場合は、使用可能なすべてのスキーマがインポートされます。

        SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます。 たとえば: `A%; %B; %C%; D`
        * A で始まる、または
        * B で終わる、または
        * C を含む、または
        * D と等しい

        NOT および特殊文字の使用は許容されません。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする MySQL ソースのサイズによって異なります。

        > [!Note]
        > 経験則として、テーブル 1,000 個ごとに 1 GB のメモリを用意してください

        :::image type="content" source="media/register-scan-mysql/scan.png" alt-text="MySQL のスキャン" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
