---
title: Cassandra に接続して管理する
description: このガイドでは、Azure Purview で Cassandra に接続する方法と、Purview の機能を使用して Cassandra ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 80f396e9b0bf53e25b188c42da6be6b8ad7f6061
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851897"
---
# <a name="connect-to-and-manage-cassandra-in-azure-purview-preview"></a>Azure Purview で Cassandra に接続して管理する (プレビュー)

この記事では、Azure Purview で、Cassandra を登録する方法、および Cassandra を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-cassandra.md)|

> [!Important]
> サポートされている Cassandra サーバーのバージョンは 3.*x* または 4.*x* です。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
  詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Cassandra を登録する方法について説明します。

### <a name="steps-to-register"></a>登録する手順

新しい Cassandra サーバーをデータ カタログに登録するには:

1. ご自分の Purview アカウントに移動します。
1. 左側のペインで **[Data Map]** を選択します。
1. **[登録]** を選択します。
1. **[ソースの登録]** 画面で **[Cassandra]** を選択し、 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="[ソースの登録] 画面を示すスクリーンショット。" border="true":::

1. **[ソースの登録 (Cassandra)]** 画面で:

   1. **[名前]** を入力します。 データ ソースでは、この名前がカタログで使用されます。
   1. **[ホスト]** ボックスに、Cassandra サーバーが実行されているサーバー アドレスを入力します。 例: 20.190.193.10。
   1. **[ポート]** ボックスに、Cassandra サーバーで使用されるポートを入力します。
   1. コレクションを選択するか新規作成します (省略可能)。
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="[ソースの登録 (Cassandra)] 画面を示すスクリーンショット。" border="true":::
   1. **[登録]** を選択します。

## <a name="scan"></a>スキャン

次の手順に従って、Cassandra をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください

### <a name="create-and-run-scan"></a>スキャンを作成して実行する

新しいスキャンを作成して実行するには:

1. 管理センターで、 **[統合ランタイム]** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちらの手順を実行して、セルフホステッド統合ランタイムを設定します](./manage-integration-runtimes.md)。

1. **[ソース]** に移動します。

1. 登録されている Cassandra サーバーを選択します。

1. **[新しいスキャン]** を選択します。

1. 以下の詳細を指定します。

    1. **[名前]** : スキャンの名前を指定します。

    1. **[統合ランタイム経由で接続]** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[資格情報]** : Cassandra の資格情報を構成する場合は、必ず次のことを行ってください。

        * 認証方法として **[基本認証]** を選択します。
        * **[ユーザー名]** ボックスに、接続するユーザーの名前を指定します。 
        * キー コンテナーのシークレットに、接続する Cassandra ユーザーのパスワードを保存します。

        詳細については、[Purview でのソース認証用の資格情報](manage-credentials.md)に関するページを参照してください。

    1. **[Keyspaces]\(キースペース\)** : インポートする Cassandra キースペースの一覧を指定します。 複数のキースペースは、セミコロンで区切る必要があります。 たとえば、keyspace1; keyspace2 のようになります。 一覧が空の場合は、使用可能なすべてのキースペースがインポートされます。

        % を含む SQL LIKE 式構文を使用するキースペース名パターンを使用できます。

        例: A%; %B; %C%; D

        この式は、次を意味します。
        * A で始まる、または
        * B で終わる、または
        * C が含まれている、または
        * D と等しい

        NOT または特殊文字は使用できません。

    1. **[Secure Sockets Layer (SSL) を使用する]** : Cassandra サーバーに接続するときに Secure Sockets Layer (SSL) を使用するかどうかを指定するには、**True** または **False** を選択します。 既定では、このオプションは **False** に設定されています。

    1. **[Maximum memory available]\(使用可能な最大メモリ\)** : スキャン プロセスで使用される、お使いの VM で使用可能な最大メモリ (GB 単位) を指定します。 この値は、スキャンする Cassandra サーバーのサイズによって異なります。
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Cassandra ソースのスキャン" border="true":::

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
