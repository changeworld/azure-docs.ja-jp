---
title: Azure Automation で Change Tracking と Inventory を管理する
description: この記事では、Change Tracking と Inventory を使用して、ご使用環境で発生するソフトウェアと Microsoft サービスの変更を追跡する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779299"
---
# <a name="manage-change-tracking-and-inventory"></a>Change Tracking と Inventory の管理

新しいファイルまたはレジストリ キーを追跡対象として追加すると、Azure Automation は、[Change Tracking と Inventory](change-tracking.md) 機能に対してそれを有効にします。 この記事には、この機能を使用するための手順が含まれています。

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>完全な Change Tracking と Inventory 機能を有効にする

[Azure Security Center File Integrity Monitoring (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) を有効にした場合は、次に示すように完全な Change Tracking と Inventory 機能を使用できます。 お使いの設定は、このプロセスによって削除されません。

> [!NOTE]
> 完全な Change Tracking と Inventory 機能を有効にすると、追加料金が発生する可能性があります。 「[Automation の価格](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

1. ワークスペースに移動し、[インストールされている監視ソリューションの一覧](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)から監視ソリューションを見つけて削除します。
2. 「[監視ソリューションを削除する](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)」で説明されているように、ソリューション名をクリックして [概要] ページを開き、 **[削除]** をクリックします。
3. Change Tracking と Inventory を再度有効にするには、Automation アカウントに移動し、 **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。
4. Log Analytics ワークスペースと Automation アカウントを選択し、ワークスペースの設定を確認し、 **[有効にする]** をクリックします。

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Change Tracking と Inventory にマシンをオンボードする

変更の追跡を開始するには、Azure Automation で Change Tracking と Inventory を有効にする必要があります。 お使いのマシンをこの機能にオンボードするために推奨およびサポートされている方法を次に示します。 

* [仮想マシンからオンボードする](automation-onboard-solutions-from-vm.md)
* [複数のマシンの参照からオンボードする](automation-onboard-solutions-from-browse.md)
* [Automation アカウントからオンボードする](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook でオンボードする](automation-onboard-solutions.md)

## <a name="track-files"></a>ファイルを追跡する

### <a name="configure-file-tracking-on-windows"></a>Windows でファイル追跡を構成する

次の手順を使用して、Windows コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで **[Windows ファイル]** を選択し、次に **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
4. [Add Windows File for Change Tracking]\(変更履歴用の Windows ファイルを追加する\) ペインで、追跡するファイルの情報を入力し、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイルを確認するパス (例: **c:\temp\\\*.txt**)。 `%winDir%\System32\\\*.*` などの環境変数も使用できます。       |
    |パスの種類     | パスの種類。 指定できる値は [ファイル] と [ディレクトリ] です。        |    
    |再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |    
    |ファイル コンテンツのアップロード | 追跡された変更についてのファイル内容をアップロードする場合は True、それ以外の場合は False。|

5. **[ファイル コンテンツのアップロード]** には、必ず True を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

### <a name="configure-file-tracking-on-linux"></a>Linux でファイル追跡を構成する

次の手順を使用して、Linux コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで **[Linux ファイル]** を選択し、次に **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
4. [Add Linux File for Change Tracking]\(変更履歴用の Linux ファイルを追加する\) ペインで、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイル確認のためのパス (例: **/etc/*.conf**)。       |
    |パスの種類     | パスの種類。 指定できる値は [ファイル] と [ディレクトリ] です。        |
    |再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |
    |sudo の使用     | 項目を確認するときに sudo を使用する場合は True、そうでない場合は False。         |
    |リンク     | ディレクトリを走査するときにシンボリック リンクを処理する方法を決定する設定。 次のいずれかの値になります。<br> 無視 - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>フォロー - 再帰中、シンボリック リンクに従います。また、参照先のファイルやディレクトリを含めます。<br>管理 - シンボリック リンクに従います。また、返された内容の変更を許可します。 **注** - ファイル コンテンツの取得がサポートされないため、このオプションは推奨されません。    |
    |ファイル コンテンツのアップロード | 追跡された変更についてのファイル内容をアップロードする場合は True、それ以外の場合は False。 |

5. **[ファイル コンテンツのアップロード]** には、必ず True を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

   ![Linux ファイルを追加する](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>ファイル コンテンツを追跡する

ファイル コンテンツの追跡により、追跡された変更の前後のファイルのコンテンツを表示できます。 この機能は、変更が発生するたびにファイル コンテンツをストレージ アカウントに保存します。 ファイル コンテンツを追跡するためのいくつかの規則を次に示します。

* ファイル コンテンツの格納には、Resource Manager デプロイ モデルを使用している Standard ストレージ アカウントが必要です。 

* Premium およびクラシック デプロイ モデルのストレージ アカウントは使用しないでください。 [Azure Storage アカウントの概要](../storage/common/storage-create-storage-account.md)に関する記事をご覧ください。

* 使用しているストレージ アカウントは、1 つの Automation アカウントにのみ接続できます。

* [Change Tracking と Inventory](change-tracking.md) は、Automation アカウントで有効になっています。

### <a name="enable-tracking-for-file-content-changes"></a>ファイル コンテンツの変更の追跡を有効にする

1. Azure portal で、Automation アカウントを開き、次に **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. **[ファイル コンテンツ]** を選択し、 **[リンク]** をクリックします。 この選択により、[Add Content Location for Change Tracking]\(Change Tracking の対象となるコンテンツの場所を追加\) ペインが開きます。

   ![コンテンツの場所を有効にする](./media/change-tracking-file-contents/enable.png)

4. ファイル コンテンツを格納するために使用するサブスクリプションとストレージ アカウントを選択します。 

5. 既存の追跡対象ファイルすべてに対してファイル コンテンツの追跡を有効にする場合は、 **[すべての設定のファイル コンテンツをアップロードする]** で **[オン]** を選択します。 この設定は、後でファイル パスごとに変更できます。

   ![ストレージ アカウントを設定する](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking と Inventory は、ファイル コンテンツの変更追跡を有効にするときに、ストレージ アカウントと Shared Access Signature (SAS) の URI を表示します。 署名は 365 日後に期限切れになりますが、 **[再生成]** をクリックすると再作成できます。

   ![アカウント キーの一覧表示](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>追跡対象ファイルのコンテンツを表示する

Change Tracking と Inventory により追跡対象ファイルの変更が検出されると、[変更の詳細] ペインでファイル コンテンツを表示できます。  

![変更を一覧表示する](./media/change-tracking-file-contents/change-list.png)

1. Azure portal で、Automation アカウントを開き、次に **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。

2. 変更の一覧からファイルを選択し、 **[ファイル コンテンツの変更の表示]** を選択してファイルのコンテンツを表示します。 [変更の詳細] ペインに、標準の変更前と変更後のファイル情報が表示されます。

   ![変更の詳細](./media/change-tracking-file-contents/change-details.png)

3. ファイル コンテンツは左右に並べて表示されています。 **[インライン]** を選択すると、変更はインライン ビューで表示されます。

## <a name="track-registry-keys"></a>レジストリ キーを追跡する

次の手順を使用して、Windows コンピューターでのレジストリ キー追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで、 **[Windows レジストリ]** を選択します。
4. **[+ 追加]** をクリックして、追跡する新しいレジストリ キーを追加します。
5. [Add Windows Registry for Change Tracking]\(変更履歴用の Windows レジストリを追加する\) ペインで、追跡するキーの情報を入力し、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するレジストリ キーのフレンドリ名。        |
    |グループ     | レジストリ キーを論理的にグループ化するためのグループ名。        |
    |Windows レジストリ キー   | パスを含むキー名。例:  **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**。      |

## <a name="search-logs-for-change-records"></a>ログで変更レコードを検索する

Azure Monitor ログに対して、変更レコードのさまざまな検索を実行できます。 [Change tracking]\(変更の追跡\) ページを開いた状態で **[Log Analytics]** をクリックして、[ログ] ページを開きます。 次の表に、変更レコードのログ検索の例を示します。

|クエリ  |説明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "Microsoft services" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Microsoft サービスの最新のインベントリ レコードで、Auto に設定されたが、Stopped として報告されたものを表示します。 結果は、指定されたソフトウェア名とコンピューターの最新のレコードに限定されます。    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|削除されたソフトウェアの変更レコードを表示します。|

## <a name="create-alerts-on-changes"></a>変更に関するアラートを作成する

次の例は、ファイル **C:\windows\system32\drivers\etc\hosts** がコンピューター上で変更されていることを示しています。 このファイルは、Windows がホスト名を IP アドレスに解決するために使用するため、重要です。 この操作は DNS より優先されるため、接続の問題が発生する可能性があります。 また、悪意のある、またはその他の危険な Web サイトにトラフィックがリダイレクトされる可能性もあります。

![hosts ファイルの変更を示すグラフ](./media/change-tracking-file-contents/changes.png)

次の例を使用して、変更に関するアラートを作成する手順について説明します。

1. Automation アカウントで、 **[構成管理]** の **[Change tracking]\(変更の追跡\)** を選択し、次に **[Log Analytics]** を選択します。 
2. ログ検索で、クエリ `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` を使用して、**hosts** ファイルに対するコンテンツ変更を検索します。 このクエリは、"hosts" という単語を含む完全修飾パスを持つファイルのコンテンツ変更を検索します。 パスの部分を完全修飾の形式に変更する (たとえば、`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` を使用する) ことで、特定のファイルを要求することもできます。

3. クエリが目的の結果を返したら、ログ検索で **[新しいアラート ルール]** をクリックしてアラート作成ページを開きます。 Azure portal で **Azure Monitor** を使用してこのページに移動することもできます。 

4. もう一度クエリを確認し、警告ロジックを変更してください。 この場合は、環境内のすべてのマシンで 1 つでも変更が検出されたら、アラートがトリガーされるようにします。

    ![hosts ファイルに対する変更を追跡するためのクエリへの変更](./media/change-tracking-file-contents/change-query.png)

5. アラート ロジックを設定した後、トリガーされるアラートに対応してアクションを実行するアクション グループを割り当てます。 この場合は、送信する電子メールと、作成する IT サービスマネジメント (ITSM) チケットを設定します。 

    ![変更に関するアラートを通知するようにアクション グループを構成する](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>次のステップ

* Change Tracking と Inventory の基本については、[Change Tracking と Inventory の概要](change-tracking.md)に関する記事をご覧ください。
* Azure VM の変更をトラブルシューティングするには、[Change Tracking と Inventory での問題のトラブルシューティング](troubleshoot/change-tracking.md)に関する記事をご覧ください。
* [Azure Monitor ログのログ検索](../log-analytics/log-analytics-log-searches.md)を使用して、詳細な変更追跡データを確認します。