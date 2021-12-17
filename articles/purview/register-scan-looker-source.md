---
title: Looker に接続して管理する
description: このガイドでは、Azure Purview で Looker に接続し、Azure Purview の機能を使用して Looker ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 3bb15a424496c04f63537a9dac4622e52a052c46
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848042"
---
# <a name="connect-to-and-manage-looker-in-azure-purview-preview"></a>Azure Purview で Looker に接続して管理する (プレビュー)

この記事では、Looker を登録する方法と、Azure Purview で Looker を認証して操作する方法の概要を説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

> [!IMPORTANT]
> ソースとしての Looker は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-looker.md)|

> [!Important]
> サポートされている Looker サーバーのバージョンは 7.2 です

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Looker を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

Looker サーバーに接続するには、API3 キーが必要です。 API3 キーは、パブリック client_id とプライベート client_secret で構成され、OAuth2 認証パターンに従います。

### <a name="steps-to-register"></a>登録する手順

新しい Looker サーバーをデータ カタログに登録するには、次のようにします。

1. ご自分の Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します。
1. [ソースの登録] で、 **[Looker]** を選択します。 **[続行]** を選択します。

:::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="looker ソースの登録" border="true":::

[ソースの登録 (Looker)] 画面で、次の操作を行います。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. **[サーバー API URL]** フィールドに Looker API URL を入力します。 API 要求の既定のポートは、ポート 19999 です。 また、すべての Looker API エンドポイントには HTTPS 接続が必要です。 例: 'https://azurepurview.cloud.looker.com'

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="looker ソースのスキャン" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って Looker をスキャンし、資産を自動的に識別し、データを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. 管理センターで、[統合ランタイム] を選択します。 erwin Mart インスタンスが実行されている VM にセルフホステッド統合ランタイムが設定されている必要があります。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順に従って、セルフホステッド統合ランタイムを設定します。

1. **[ソース]** に移動します。

1. 登録されている **Looker** サーバーを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **サーバー API の URL** は、登録時に入力した値に基づいて自動的に設定されます。

    1. **資格情報:** Looker 資格情報を構成するときは、次のことを確認してください。

        * 認証方法として **[基本認証]** を選択します
        * Looker API3 キーのクライアント ID を [ユーザー名] フィールドに入力します
        * Looker API3 キーのクライアント シークレットをキー コンテナーのシークレットに保存します。

        クライアント ID とクライアント シークレットにアクセスするには、[Looker]\>[管理]\>[ユーザー]\> の順に移動し、ユーザーの **[編集]** を選択し、\> **[EditKeys]**  -\> を選択し、クライアント ID とクライアント シークレットを使用するか、新しいシークレットを作成します。

        :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="looker の詳細を取得する" border="true":::

        資格情報の詳細については、[こちら](manage-credentials.md)のリンクを参照してください。

    1. **プロジェクト フィルター** -- セミコロンで区切られた Looker プロジェクトの一覧を提供することによって、スキャンの範囲を指定します。 このオプションは、親プロジェクトによって look とダッシュボードを選択するために使用されます。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする erwin Mart のサイズによって異なります。

        :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="スキャンのトリガー" border="true":::

1. **[テスト接続]** を選択します。

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
