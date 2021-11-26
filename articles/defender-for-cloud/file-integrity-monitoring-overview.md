---
title: Microsoft Defender for Cloud でのファイルの整合性の監視
description: このチュートリアルでは、Microsoft Defender for Cloud でファイルの整合性の監視 (FIM) を構成する方法について説明します。
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 8ceb46b1a70397d73379df3f2d9f1027feabf49b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525576"
---
# <a name="file-integrity-monitoring-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でのファイルの整合性の監視

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このチュートリアルでは、Microsoft Defender for Cloud でファイルの整合性の監視 (FIM) を構成する方法について説明します。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要です。<br>FIM は、Log Analytics エージェントを使用して、データを Log Analytics ワークスペースにアップロードします。 データ料金は、アップロードするデータの量に基づいて適用されます。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)」をご覧ください。|
|必要なロールとアクセス許可:|**ワークスペースの所有者** は、FIM を有効/無効にすることができます (詳細については、[Log Analytics での Azure のロール](/services-hub/health/azure-roles#azure-roles)に関する記事を参照してください)。<br>**閲覧者** は結果を表示できます。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)<br>Azure Automation の変更追跡ソリューションが利用可能なリージョンでのみサポートされます。<br>:::image type="icon" source="./media/icons/yes-icon.png"::: [Azure Arc](../azure-arc/servers/overview.md) 対応デバイス。<br>「[リンクされた Log Analytics ワークスペースでサポートされるリージョン](../automation/how-to/region-mappings.md)」を参照してください。<br>[変更追跡についてさらに学習](../automation/change-tracking/overview.md)します。|
|||

## <a name="what-is-fim-in-defender-for-cloud"></a>Defender for Cloud での FIM とは
ファイルの整合性の監視 (FIM) は変更の監視とも呼ばれ、オペレーティング システム ファイル、Windows レジストリ、アプリケーション ソフトウェア、Linux システム ファイルなどを調べて、攻撃の兆候となる変更を確認します。 

Defender for Cloud によって、FIM で監視の対象にするエンティティが推奨されます。ユーザーが、独自の FIM ポリシーまたは監視するエンティティを定義することもできます。 FIM を使用すると、次のような疑わしいアクティビティについて通知されます。

- ファイルとレジストリ キーの作成または削除
- ファイルの変更 (ファイル サイズ、アクセス制御リスト、およびコンテンツのハッシュの変更)
- レジストリの変更 (サイズ、アクセス制御リスト、種類、およびコンテンツの変更)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * FIM での監視対象として推奨されるエンティティの一覧を確認する方法
> * 独自のカスタム FIM ルールを定義する方法
> * 監視対象エンティティに対する変更を監査する方法
> * ワイルドカードを使用して、ディレクトリをまたいだ追跡を簡略化する方法


## <a name="how-does-fim-work"></a>FIM のしくみ

Log Analytics エージェントは、Log Analytics ワークスペースにデータをアップロードします。 FIM では、これらの項目の現在の状態が前回のスキャン中の状態と比較され、疑わしい変更が加えられた場合に通知されます。

FIM は、Azure Change Tracking ソリューションを使用して、ユーザーの環境内の変更を追跡して識別します。 ファイルの整合性の監視を有効にすると、種類が **ソリューション** である **Change Tracking** リソースを使用できるようになります。 データ収集の頻度の詳細については、[Change Tracking のデータ収集の詳細](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)に関する記事を参照してください。

> [!NOTE]
> **Change Tracking** リソースを削除すると、Defender for Cloud のファイルの整合性の監視機能も無効になります。

## <a name="which-files-should-i-monitor"></a>どのファイルを監視する必要があるか

監視するファイルを選択するときは、ご利用のシステムとアプリケーションにとって重要なファイルを考慮します。 計画なしで変更されることがないファイルを監視してください。 アプリケーションまたはオペレーティング システムによって頻繁に変更されるファイル (ログ ファイルやテキスト ファイルなど) を選択すると、多数のノイズの発生によって攻撃の識別が困難になります。

Defender for Cloud により、既知の攻撃パターンに基づいて、監視対象として推奨される項目の次のリストが提供されます。

|Linux ファイル|Windows ファイル|Windows レジストリ キー (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>ファイルの整合性の監視の有効化 

FIM は、Azure portal の Defender for Cloud のページからのみ利用できます。 現在、FIM を操作するための REST API はありません。

1. **[ワークロード保護]** ダッシュボードの **[高度な保護]** 領域で、 **[ファイルの整合性の監視]** を選びます。

   :::image type="content" source="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png" alt-text="FIM の起動。" lightbox="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png":::

    **[ファイルの整合性の監視]** 構成ページが開きます。

    各ワークスペースの次の情報が提供されます。

    - 過去 1 週間に発生した変更の合計数 (ワークスペースで FIM が有効になっていない場合は、ダッシュ "-" が表示されることがあります)
    - ワークスペースに対して報告を行っているコンピューターと VM の合計数
    - ワークスペースの地理的な場所
    - ワークスペースが存在する Azure サブスクリプション

1. このページを使用して次のことができます。

    - 各ワークスペースの状態と設定にアクセスして表示する

    - ![アップグレード プラン アイコン。][4] 強化されたセキュリティ機能を使用するためにワークスペースをアップグレードします。 このアイコンにより、ワークスペースまたはサブスクリプションが Microsoft Defender for servers で保護されていないことが示されます。 FIM 機能を使用するには、サブスクリプションがこのプランで保護されている必要があります。 詳しくは、「[Microsoft Defender for Cloud の強化されたセキュリティ機能](enhanced-security-features-overview.md)」をご覧ください。

    - ![[有効化] アイコン][3] ワークスペースにあるすべてのマシンで FIM を有効にし、FIM オプションを構成する。 このアイコンは、ワークスペースの FIM が有効ではないことを示します。

        :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-list-fim.png" alt-text="特定のワークスペースに対して FIM を有効にする。":::


    > [!TIP]
    > 有効化とアップグレードのどちらのボタンもなく、スペースが空白の場合、そのワークスペースで FIM が既に有効になっていることを意味します。


1. **[有効化]** を選択します。 ワークスペースの詳細が表示されます。ワークスペースにある Windows と Linux のマシンの数も含まれます。

    :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-fim-status.png" alt-text="FIM ワークスペースの詳細ページ。":::

   Windows と Linux の推奨設定も表示されます。  **[Windows ファイル]** 、 **[レジストリ]** 、および **[Linux ファイル]** を展開して、推奨される項目の完全な一覧を表示します。

1. FIM で監視しない推奨エンティティのチェックボックスをオフにします。

1. FIM を有効にするには、 **[ファイルの整合性の監視を適用する]** を選択します。

> [!NOTE]
> 設定はいつでも変更できます。 詳細については、「[監視対象エンティティを編集する](#edit-monitored-entities)」を参照してください。



## <a name="audit-monitored-workspaces"></a>監視対象ワークスペースの監査 

**ファイルの整合性の監視** ダッシュボードには、FIM が有効になっているワークスペースが表示されます。 FIM ダッシュボードは、ワークスペースの FIM を有効にした後、または **[ファイルの整合性の監視]** ウィンドウで FIM が既に有効になっているワークスペースを選択したときに開きます。

:::image type="content" source="./media/file-integrity-monitoring-overview/fim-dashboard.png" alt-text="FIM ダッシュボードとそのさまざまな情報パネル。":::

ワークスペースの FIM ダッシュボードには、次の詳細が表示されます。

- ワークスペースに接続されているマシンの合計数
- 選択した期間中に発生した変更の合計数
- 変更の種類の内訳 (ファイル、レジストリ)
- 変更のカテゴリの内訳 (変更、追加、削除)

ダッシュボードの上部にある **[フィルター]** を選択して、表示する変更内容の期間を変更します。

:::image type="content" source="./media/file-integrity-monitoring-overview/dashboard-filter.png" alt-text="FIM ダッシュボードの期間フィルター。":::

**[サーバー]** タブには、このワークスペースに対して報告を行うマシンが一覧表示されます。 ダッシュボードには、コンピューターごとに以下が一覧表示されます。

- 選択した期間中に発生した変更の合計数
- ファイルの変更またはレジストリの変更としての変更の合計数の内訳

マシンを選択すると、そのマシンに対して選択した期間中に行われた変更を識別する結果と共に、クエリが表示されます。 変更を展開して詳細を確認できます。

:::image type="content" source="./media/file-integrity-monitoring-overview/query-machine-changes.png" alt-text="Microsoft Defender for Cloud のファイルの整合性の監視によって識別された変更を示す Log Analytics クエリ" lightbox="./media/file-integrity-monitoring-overview/query-machine-changes.png":::

**[変更]** タブ (下図参照) には、選択した期間中に行われたワークスペースのすべての変更が一覧表示されます。 ダッシュボードには、変更されたエンティティごとに、以下が一覧表示されます。

- 変更が発生したマシン
- 変更の種類 (レジストリまたはファイル)
- 変更のカテゴリ (変更、追加、削除)
- 変更の日時

:::image type="content" source="./media/file-integrity-monitoring-overview/changes-tab.png" alt-text="Microsoft Defender for Cloud の [ファイルの整合性の監視] の [変更] タブ" lightbox="./media/file-integrity-monitoring-overview/changes-tab.png":::

**[変更の詳細]** は、検索フィールドに変更を入力するか、 **[変更]** タブに一覧表示されているエンティティを選択したときに開きます。

:::image type="content" source="./media/file-integrity-monitoring-overview/change-details.png" alt-text="変更の詳細ウィンドウが示されている Microsoft Defender for Cloud の [ファイルの整合性の監視]" lightbox="./media/file-integrity-monitoring-overview/change-details.png":::

## <a name="edit-monitored-entities"></a>監視対象エンティティを編集する

1. ワークスペースの **[ファイルの整合性の監視] ダッシュボード** で、ツール バーから **[設定]** を選択します。 

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png" alt-text="ワークスペース用のファイルの整合性の監視設定にアクセスする。" lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png":::

   **[ワークスペースの構成]** が、監視できる要素の種類ごとのタブと共に開きます。

      - Windows レジストリ
      - Windows ファイル
      - Linux ファイル
      - ファイルのコンテンツ
      - Windows サービス

      各タブには、そのカテゴリで編集できるエンティティが一覧表示します。 一覧表示されるエンティティごとに、Defender for Cloud によって、FIM が有効になっている (true) か、有効になっていない (false) かが識別されます。 エンティティを編集して、FIM を有効または無効にします。

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-workspace-configuration.png" alt-text="Microsoft Defender for Cloud でのファイルの整合性の監視に関するワークスペース構成。":::

1. いずれかのタブからエントリを選択し、**Change Tracking の編集** ウィンドウで使用可能な任意のフィールドを編集します。 次のオプションがあります。

    - ファイルの整合性の監視を有効 (True) にするか無効 (False) にする
    - エンティティ名を指定するか変更する
    - 値またはパスを指定するか変更する
    - エンティティを削除する

1. 変更を破棄するか、保存します。


## <a name="add-a-new-entity-to-monitor"></a>監視する新しいエンティティを追加する

1. ワークスペースの **[ファイルの整合性の監視] ダッシュボード** で、ツール バーから **[設定]** を選択します。 

    **[ワークスペースの構成]** が開きます。

1. **[ワークスペースの構成]** で:

    1. 追加するエンティティの種類のタブ ([Windows レジストリ]、[Windows ファイル]、[Linux ファイル]、[ファイル コンテンツ]、または [Windows サービス]) を選択します。 
    1. **[追加]** を選択します。 

        この例では、 **[Linux ファイル]** が選択されています。

        :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png" alt-text="Microsoft Defender for Cloud のファイルの整合性の監視での監視する要素の追加" lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png":::

1. **[追加]** を選択します。 **[Edit for Change Tracking]\(変更追跡を行うための追加\)** が開きます。

1. 必要な情報を入力し、 **[保存]** を選択します。

## <a name="folder-and-path-monitoring-using-wildcards"></a>ワイルドカードを使用するフォルダーとパスの監視

ワイルドカードを使用して、ディレクトリ全体の追跡を簡略化します。 ワイルドカードを使用するフォルダーの監視を構成する場合、次のルールが適用されます。
-   複数のファイルを追跡するにはワイルドカードが必要です。
-   ワイルドカードを使用できるのは、C:\folder\file or /etc/*.conf など、パスの最後のセグメントでのみです。
-   環境変数に有効でないパスが含まれている場合、検証は成功しますが、インベントリの実行時にそのパスはエラーになります。
-   パスを設定するときは、漠然としたパス (\* など) は避けてください。走査の対象になるフォルダーが膨大な数になります。

## <a name="disable-fim"></a>FIM を無効にする
FIM を無効にすることができます。 FIM は、Azure Change Tracking ソリューションを使用して、ユーザーの環境内の変更を追跡して識別します。 FIM を無効にすることで、選択したワークスペースから Change Tracking ソリューションを削除します。

FIM を無効にするには

1. ワークスペースの **[ファイルの整合性の監視] ダッシュボード** で、 **[無効にする]** を選択します。

    :::image type="content" source="./media/file-integrity-monitoring-overview/disable-file-integrity-monitoring.png" alt-text="[設定] ページでファイルの整合性の監視を無効にする。":::

1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
この記事では、Defender for Cloud のファイルの整合性の監視 (FIM) を使用する方法について説明しました。 Defender for Cloud について詳しくは、次のページをご覧ください。

* [セキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
* [セキュリティに関する推奨事項の管理](review-security-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
* [Azure セキュリティ ブログ](/archive/blogs/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[3]: ./media/file-integrity-monitoring-overview/enable.png
[4]: ./media/file-integrity-monitoring-overview/upgrade-plan.png
