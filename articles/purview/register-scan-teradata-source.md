---
title: Teradata への接続と管理
description: このガイドでは、Azure Purview で Teradata に接続する方法と、Purview の機能を使用して Teradata ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ebaad16ff413b33f175815a1ddadb2fa1d5b63ae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841925"
---
# <a name="connect-to-and-manage-teradata-in-azure-purview"></a>Azure Purview での Teradata への接続と管理

この記事では、Azure Purview で、Teradata を登録する方法、および Teradata を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい**](how-to-lineage-teradata.md)|

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

サポートされている Teradata データベースのバージョンは 12.x から 16.x です。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* セルフホステッド統合ランタイムが実行されている仮想マシンで Teradata の JDBC Driver を手動でダウンロードする必要があります。 実行可能 JAR ファイルは、Teradata の [Web サイト](https://downloads.teradata.com/)からダウンロードできます。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Teradata を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

Teradata ソースでサポートされている認証は **基本認証** のみです。 スキャン対象の Teradata ソースに対する読み取りアクセス権があることを確認します。

### <a name="steps-to-register"></a>登録の手順

1.  ご自分の Purview アカウントに移動します。
1.  左側のナビゲーションで **[Data Map]** を選択します。
1.  **[登録]** を選択します
1.  [Register sources]\(ソースの登録\) で、 **[Teradata]** を選択します **[続行]** を選択します。

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata オプションの登録" border="true":::

**[Register sources (Teradata)]\(ソースの登録 (Teradata)\)** 画面で、次の手順を実行します。

1.  データ ソースがカタログに表示される際の **名前** を入力します。

1.  Teradata ソースに接続するための **ホスト** 名を入力します。 サーバーの IP アドレスまたは完全修飾接続文字列を指定することもできます。

1.  コレクションを選択するか、新しいものを作成します (省略可能)

1.  データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata の登録" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って Teradata をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

1. 管理センターで、 **[統合ランタイム]** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順に従って、セルフホステッド統合ランタイムを設定します

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録されている Teradata ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。
        * 資格情報を作成するときに [基本認証] を選択します。
        * ユーザー名入力フィールドで、データベース サーバーに接続するためのユーザー名を指定します。
        * 秘密鍵にデータベース サーバーのパスワードを格納します。

        資格情報の詳細については、[こちら](./manage-credentials.md)のリンクを参照してください。

    1. **[スキーマ]** : インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 たとえば、「`schema1; schema2`」のように入力します。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。 リストが空の場合は、使用可能なすべてのスキーマがインポートされます。

        SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます。 たとえば: `A%; %B; %C%; D`
        * A で始まる、または
        * B で終わる、または
        * C を含む、または
        * D と等しい

        NOT および特殊文字の使用は許容されません

    1. **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Teradata ソースのサイズによって異なります。

        > [!Note]
        > 経験則として、テーブル 1,000 個ごとに 2 GB のメモリを用意してください

        :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="スキャンを設定する" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
