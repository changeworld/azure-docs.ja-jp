---
title: 'チュートリアル: オンプレミスの SQL Server の登録とスキャン'
description: このチュートリアルでは、オンプレミスの SQL Server を Purview に登録し、セルフホステッド IR を使用してサーバーをスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5fc1369c20130d8c689abe853e321fc1507fa414
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249573"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>チュートリアル: オンプレミスの SQL Server の登録とスキャン

Azure Purview は、データ ソースに接続することで機密データの管理、データ検出の簡素化、適切な使用の確保を支援するように設計されています。 Purview は、マルチクラウドとオンプレミスを含め、お使いの環境全体のソースに接続できます。 このシナリオでは、セルフホステッド統合ランタイムを使用して、オンプレミスの SQL Server 上のデータに接続します。 その後に、Azure Purview を使用してそのデータをスキャンし、分類します。

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * Purview Studio にサインインします。
> * Azure Purview でコレクションを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * Azure Key Vault に資格情報を格納します。
> * オンプレミスの SQL Server を Purview に登録します。
> * SQL Server をスキャンします。
> * データ カタログを参照して、SQL Server 内の資産を表示します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- アクティブな [Azure Key Vault](../key-vault/general/quick-create-portal.md)。
- Azure Purview アカウント。 まだない場合は、[クイック スタート ガイドに従って作成](create-catalog-portal.md)できます。
- [オンプレミスの SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)。

## <a name="sign-in-to-purview-studio"></a>Purview Studio にサインインする

Purview を操作するには、Azure portal を使用して [Purview Studio](https://web.purview.azure.com/resource/) に接続します。 Studio を見つけるには、[Azure portal](https://portal.azure.com) で Purview リソースに移動し、概要ページで **[Purview Studio を開く]** タイルを選択します。

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Purview Studio のボタンが強調表示された、Azure portal 内の Purview ウィンドウのスクリーンショット。" border="true":::

## <a name="create-a-collection"></a>コレクションの作成

Azure Purview のコレクションは、資産とソースをカスタム階層として整理し、組織化して検出しやすくするために使用されます。 また、これらは Purview 全体のアクセス管理に使用されるツールでもあります。 このチュートリアルでは、1 つのコレクションを作成して、SQL Server のソースとそのすべての資産を格納します。 このチュートリアルでは他のユーザーへのアクセス許可の割り当てについては説明しないため、詳細については、[Purview のアクセス許可に関するガイド](catalog-permissions.md)を参照してください。

### <a name="check-permissions"></a>アクセス許可を確認してください

Purview でコレクションを作成および管理するには、Purview 内で **コレクション管理者** であることが必要です。 これらのアクセス許可は [Purview Studio](use-purview-studio.md) で確認できます。

1. 左側のウィンドウから **[Data Map] > [コレクション]** を選択し、コレクション管理ページを開きます。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択されています。" border="true":::

1. ルート コレクションを選択します。 ルート コレクションは、コレクション リストの一番上のコレクションであり、Purview リソースと同じ名前になります。 下の例では、[Purview Account]\(Purview アカウント\) という名前になっています。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ルート コレクションが強調表示されています。" border="true":::

1. コレクション ウィンドウで **ロールの割り当て** を選択します。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[ロールの割り当て] タブが強調表示されています。" border="true":::

1. コレクションを作成するには、ご自身がロールの割り当ての下のコレクション管理者リストに含まれている必要があります。 Purview リソースを作成した場合は、ルート コレクションの下にコレクション管理者として既に一覧表示されている必要があります。 それ以外の場合は、コレクション管理者に連絡してアクセス許可を付与してもらう必要があります。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、コレクション管理者のセクションが強調表示されています。" border="true":::

### <a name="create-the-collection"></a>コレクションを作成する

1. **[+ コレクションの追加]** を選択します。 この場合も、[コレクション管理者](#check-permissions)のみがコレクションを管理できます。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="[コレクションの追加] ボタンが強調表示された新しいコレクション ウィンドウを示す Purview Studio ウィンドウのスクリーンショット。" border="true":::

1. 右側のパネルで、コレクションの名前と説明を入力します。 必要に応じて、新しいコレクションにコレクション管理者としてユーザーまたはグループを追加できます。
1. **［作成］** を選択します

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。新しいコレクション ウィンドウが表示され、表示名とコレクション管理者が選択され、[作成] ボタンが強調表示されています。" border="true":::

1. 新しいコレクションの情報がページに反映されます。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="新しく作成されたコレクション ウィンドウが表示されている Purview Studio ウィンドウのスクリーンショット。" border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

セルフホステッド統合ランタイム (SHIR) は、オンプレミスのデータ ソースに接続するために Purview によって使用されるコンピューティング インフラストラクチャです。 SHIR は、オンプレミスのデータ ソースと同じネットワーク内のマシンにダウンロードしてインストールします。

このチュートリアルでは、セルフホステッド統合ランタイムをインストールするマシンから、インターネットへのネットワーク接続を行うことができることを前提としています。 この接続により、SHIR でソースと Azure Purview 間の通信を行うことができます。 お使いのマシンに制限付きファイアウォールがある場合、またはファイアウォールをセキュリティで保護する場合は、[セルフホステッド統合ランタイムのネットワーク要件](manage-integration-runtimes.md#networking-requirements)を確認してください。

1. Purview Studio のホーム ページの左側のナビゲーション ウィンドウで、 **[Data Map]** を選択します。

1. 左側のウィンドウの **[Source management]\(ソース管理\)** で **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="[統合ランタイム] ボタンを選択します。":::

1. **[統合ランタイムのセットアップ]** ページで **[セルフホステッド]** を選択してセルフホステッド IR を作成し、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="新しい SHIR を作成します。":::

1. IR の名前を入力し、 [作成] を選択します。

1. **[統合ランタイムのセットアップ]** ページで、 **[手動セットアップ]** セクションに記載されている手順に従います。 統合ランタイムは、ダウンロード サイトから、オンプレミスの SQL Server と同じネットワーク内にある VM またはマシンにダウンロードする必要があります。 必要なマシンの種類については、[統合ランタイムの管理に関するガイド](manage-integration-runtimes.md#prerequisites)に従ってください。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="キーの取得":::

   - 認証キーをコピーして貼り付けます。

   - セルフホステッド統合ランタイムを、「[Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)」からローカルの Windows マシンにダウンロードします。 インストーラーを実行します。 5\.4.7803.1 や 5.6.7795.1 などのセルフホステッド統合ランタイム バージョンがサポートされています。 

   - **[Integration Runtime (セルフホステッド) の登録]** ページで、前に保存した 2 つのキーのいずれかを貼り付け、 **[登録]** を選択します。

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="キーの入力":::

   - **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。

1. セルフホステッド統合ランタイムが正常に登録されると、このウィンドウが表示されます。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="正常に登録":::

## <a name="set-up-sql-authentication"></a>SQL 認証を設定する

オンプレミス SQL Server の認証を設定する方法は 1 つだけです。

- SQL 認証

### <a name="sql-authentication"></a>SQL 認証

SQL アカウントには、**master** データベースへのアクセス権が必要です。 これは、`sys.databases` がそのデータベース内にあるためです。 Purview スキャナーは、サーバー上のすべての SQL データベースを検出するために、`sys.databases` を列挙する必要があります。

#### <a name="create-a-new-login-and-user"></a>新しいログインとユーザーを作成する

新しいログインとユーザーを作成して SQL Server をスキャンできるようにするには、次の手順に従います。

> [!Note]
> 下のすべての手順は、[こちら](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)で提供されているコードを使用して実行できます。

1. SQL Server Management Studio (SSMS) に移動してサーバーに接続し、セキュリティに移動し、[ログイン] を選択したままにして (または右クリックして) 新しいログインを作成します。 必ず SQL 認証を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="新しいログインとユーザーを作成します。":::

1. 左側のナビゲーションで [サーバー ロール] を選択し、public ロールが割り当てられていることを確認します。

1. 左側のナビゲーションで [ユーザー マッピング] を選択し、マップ内のすべてのデータベースを選択し、データベース ロール: **db_datareader** を選択します。

1. **[OK]** を選択して保存します。

1. ユーザーを選択したままにして (または右クリックして) **[プロパティ]** を選択し、作成したユーザーにもう一度移動します。 新しいパスワードを入力し、それを確認します。 [古いパスワードを指定する] を選択し、古いパスワードを入力します。 **新しいログインを作成したらすぐに、パスワードを変更する必要があります。**

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="パスワードを変更します。":::

#### <a name="create-a-key-vault-credential"></a>Key Vault の資格情報を作成する

1. Azure portal で、ご自身のキー コンテナーに移動します。 **[設定] > [シークレット]** を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="左側のメニューで [シークレット] を選択します":::

1. **[+生成/インポート]** を選択します

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="上部のメニューから [生成/インポート] を選択します。":::

1. アップロード オプションで **[手動]** を選択し、**名前** と **値** を SQL Server ログインの "*パスワード*" として入力します。 **[有効]** が **[はい]** に設定されていることを確認します。 アクティブ化と有効期限の日付を設定する場合は、今日の日付がその 2 つの間であることを確認します。そうでない場合、資格情報を使用できません。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="キー コンテナーの資格情報に値を追加します。":::

1. **[作成]** を選択して完了します。
1. [Azure Purview Studio](#sign-in-to-purview-studio) で、左側のメニューの **[管理]** ページに移動します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="左側のメニューで [管理] ページを選択します。":::

1. **[資格情報]** ページを選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="[管理] ページの [資格情報] ボタンが強調表示されています。":::

1. **[資格情報]** ページで、 **[Key Vault 接続の管理]** を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="Azure Key Vault 接続を管理します。":::

1. [Key Vault 接続の管理] ページで **[+ 新規]** を選択します。

1. 必要な情報を入力して、 **[作成]** を選択します。

1. この例に示すように、Key Vault が Azure Purview アカウントに正常に関連付けられていることを確認します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="確認のために Azure Key Vault 接続を表示します。":::

1. **[+ 新規]** を選択して、SQL Server の新しい資格情報を作成します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="[+ 新規] を選択して資格情報を作成します。":::

1. 必要な情報を入力します。 **[認証方法]** と、シークレットの選択元となる **[Key Vault インスタンス]** を選択します。

1. すべての詳細の入力が完了したら、 **[作成]** を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="新しい資格情報":::

1. 新しい資格情報がリスト ビューに表示され、使用する準備ができていることを確認します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="資格情報の表示":::

## <a name="register-sql-server"></a>SQL Server を登録する

1. [Azure portal](https://portal.azure.com) で Purview アカウントに移動し、[[Purview Studio]](#sign-in-to-purview-studio) を選択します。

1. 左側のナビゲーションの Sources and scanning\(ソースとスキャン\) で、**統合ランタイム** を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](manage-integration-runtimes.md)に記載されている手順に従って、オンプレミスまたはオンプレミス ネットワークにアクセスできる Azure VM でスキャンするためのセルフホステッド統合ランタイムを作成します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. **[SQL Server]** を選択し、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL データ ソースを設定します。":::

1. フレンドリ名とサーバー エンドポイントを指定し、 **[完了]** を選択してデータ ソースを登録します。 たとえば、SQL Server の FQDN が **foobar.database.windows.net** の場合は、サーバー エンドポイントとして *foobar* を入力します。

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側のペインで **[Data Map]** タブを選択します。

1. 登録した SQL Server のソースを選択します。

1. **[新しいスキャン]** 選択する

1. 対象のデータ ソースに接続するための資格情報を選択します。

1. リストから適切な項目を選択することによって、特定のテーブルに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後も引き続きこの Purview または SQL ソースを使用しない場合は、下の手順に従って、統合ランタイム、SQL 資格情報、Purview リソースを削除できます。

### <a name="remove-shir-from-purview"></a>Purview から SHIR を削除する

1. [Purview Studio](https://web.purview.azure.com/resource/) のホーム ページの左側のナビゲーション ウィンドウで、 **[Data Map]** を選択します。

1. 左側のウィンドウの **[Source management]\(ソース管理\)** で **[統合ランタイム]** を選択します。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="[統合ランタイム] ボタンを選択します。":::

1. 統合ランタイムの横にあるチェック ボックスをオンにし、 **[削除]** ボタンを選択します。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="統合ランタイムの横のチェック ボックスと [削除] ボタンが強調表示されています。":::

1. 次のウィンドウで **[確定]** を選択して削除を確定します。

1. ウィンドウが自己更新され、統合ランタイムの下に SHIR が表示されなくなります。

### <a name="uninstall-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをアンインストールする

1. セルフホステッド統合ランタイムがインストールされているマシンにサインインします。
1. コントロール パネルを開き、 *[プログラムのアンインストール]* で "Microsoft Integration Runtime" を検索します

1. 既存の統合ランタイムをアンインストールします。

> [!IMPORTANT] 
> 次のプロセスで、 [はい] を選択します。 アンインストール プロセス中はデータを保持しないでください。

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="統合ランタイムからすべてのユーザー データを削除するための [はい] ボタンのスクリーンショット。":::

### <a name="remove-sql-credentials"></a>SQL 資格情報を削除する

1. [Azure portal](https://portal.azure.com) にアクセスし、Purview の資格情報を格納した Key Vault リソースに移動します。

1. 左側のメニューの **[設定]** で、 **[シークレット]** を選択します

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="Azure Key Vault の左側のメニューから [シークレット] を選択します。":::

1. このチュートリアルで作成した SQL Server 資格情報のシークレットを選択します。
1. **[削除]** を選択します

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="Azure Key Vault シークレットの上部メニューからシークレットを削除します。":::

1. **[はい]** を選択して、リソースを完全に削除します。

### <a name="delete-purview-account"></a>Purview アカウントを削除する

このチュートリアルの完了後に Purview アカウントを削除する場合は、こちらの手順に従います。

1. [Azure portal](https://portal.azure.com) に進み、Purview アカウントに移動します。

1. ページの上部にある **[削除]** ボタンを選択します。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="Azure portal の [Purview account]\(Purview アカウント\) ページの [削除] ボタンが選択されています。":::

1. プロセスが完了すると、Azure portal で通知を受け取ります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Purview REST API を使用する](tutorial-using-rest-apis.md)