---
title: Azure Automation で変更を追跡する
description: Change Tracking ソリューションは、ユーザーの環境で起こるソフトウェアと Windows サービスの変更を特定するために役立ちます。
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86b8f76bd221be9f30a5b9336af858359ae0af8f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238881"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する

この記事では、Change Tracking ソリューションを使用して、ユーザーの環境内の変更箇所を簡単に識別する方法を説明します。 このソリューションは、Windows および Linux ソフトウェア、Windows および Linux ファイル、Windows レジストリ キー、Windows サービス、および Linux デーモンに対する変更を追跡します。 構成の変更を識別することで、運用上の問題を特定できるようになります。

監視対象サーバーにインストールされているソフトウェア、Windows サービス、および Windows レジストリとファイル、および Linux デーモンの変更が、クラウドの Log Analytics サービスに送信され、処理されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。 [変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム

Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2008 Service Pack 1 (SP1) 以降
* Windows 7 SP1 以降

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

次の Linux ディストリビューションは公式にサポートされています。 ただし、Linux エージェントは、ここに記載されていないディストリビューションでも動作する可能性があります。 記載されている各メジャー バージョンのマイナー リリースは、特に記載がない限りすべてサポートされます。  

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)  
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

変更の追跡を開始するには、Automation アカウントの Change Tracking および Inventory ソリューションを有効にする必要があります。

1. Azure Portal で、Automation アカウントに移動します
1. **[構成]** の **[Change Tracking]\(変更の追跡\)** を選択します。
1. 既存の Log Analytics ワークスペースまたは **[新しいワークスペースの作成]** を選択し、**[有効化]** をクリックします。

これにより Automation アカウントに対してソリューションが有効になります。 ソリューションを有効にするには最大 15 分かかります。 青のバナーは、ソリューションが有効になっていることを示します。 **[Change Tracking]\(変更の追跡\)** ページに戻って、ソリューションを管理します。

## <a name="configuring-change-tracking-and-inventory"></a>Change Tracking と Inventory の構成

コンピューターをソリューションに追加する方法については、[Automation ソリューションの配布準備](automation-onboard-solutions-from-automation-account.md)に関するページをご覧ください。 Change Tracking と Inventory ソリューションでのマシンのオンボードが完了したら、追跡する項目を構成できます。新しいファイルまたはレジストリ キーの追跡を有効にすると、Change Tracking と Inventory の両方に対して有効になります。

Windows と Linux の両方でファイルの変更を追跡する場合、ファイルの MD5 ハッシュが使用されます。 これらのハッシュは、前回のインベントリから変更が加えられたかどうかを検出するために使用されます。

### <a name="configure-linux-files-to-track"></a>追跡する Linux ファイルを構成する

次の手順を使用して、Linux コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、**[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[変更の追跡]** ページで、**[Linux ファイル]** を選択し、**[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
3. **[変更履歴用の Linux ファイルを追加する]** で、追跡するファイルまたはディレクトリの情報を入力し、**[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|項目名     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス。 例: "/etc/*.conf"       |
|パスの種類     | 追跡する項目の種類。"ファイル" または "ディレクトリ" を指定できます。        |
|再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
|sudo の使用     | この設定により、項目を確認するときに、sudo を使用するかどうかが決まります。         |
|リンク     | この設定により、ディレクトリを走査するときの、シンボリック リンクの処理方法が決まります。<br> **無視** - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>**フォロー** - 再帰中、シンボリック リンクに従います。参照先のファイル/ディレクトリも含めます。<br>**管理** - シンボリック リンクに従います。また、返却された内容の変更を許可します。     |
|すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用可能なオプション: **True** または **False**。|

> [!NOTE]
> "管理" リンク オプションはお勧めしません。 ファイルのコンテンツの取得はサポートされていません。

### <a name="configure-windows-files-to-track"></a>追跡する Windows ファイルの構成

次の手順を使用して、Windows コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、**[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[Change Tracking]\(変更の追跡\)** ページで、**[Windows ファイル]** を選択し、**[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
3. **[変更履歴用の Windows ファイルを追加する]** で、追跡するファイルの情報を入力し、**[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|項目名     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス。例: "c:\temp\myfile.txt"       |
|すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用可能なオプション: **True** または **False**。|

## <a name="configure-file-content-tracking"></a>ファイル コンテンツの追跡を構成する

ファイル コンテンツの変更の追跡を使用して、ファイル変更の前後のコンテンツを表示できます。 これは、Windows および Linux ファイルに対して使用できます。ファイルが変更されるたびに、ファイルのコンテンツがストレージ アカウントに格納され、変更の前後のファイルがインラインで、または横に並べて表示されます。 詳細については、[追跡されたファイルのコンテンツの表示](change-tracking-file-contents.md)に関するページを参照してください。

![ファイル内の変更を表示する](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>追跡する Windows レジストリ キーを構成する

次の手順を使用して、Windows コンピューターでのレジストリ キー追跡を構成します。

1. Automation アカウントで、**[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[Change Tracking]\(変更の追跡\)** ページで、**[Windows レジストリ]** を選択し、**[+ 追加]** をクリックして、追跡する新しいレジストリ キーを追加します。
3. **[変更履歴用の Windows レジストリを追加する]** で、追跡するキーの情報を入力し、**[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|項目名     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|Windows レジストリ キー   | ファイル確認のためのパス。 例: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>制限事項

現在、Change Tracking ソリューションでは以下の項目に対応していません。

* Windows ファイル追跡用のフォルダー (ディレクトリ)
* Windows ファイル追跡用の再帰
* Windows ファイル追跡用のワイルドカード
* Windows レジストリ追跡用の再帰
* パス変数
* ネットワーク ファイル システム
* ファイル コンテンツ

その他の制限事項:

* **[最大ファイル サイズ]** 列と値は現在の実装では使用されません。
* 30 分間の収集サイクルで収集するファイル数が 2500 を超えると、ソリューションのパフォーマンスが低下する可能性があります。
* ネットワーク トラフィックが高いとき、変更レコードが表示されるまでに最大 6 時間かかることがあります。
* コンピューターのシャットダウン中に構成を変更した場合、そのコンピューターから前の構成に対応する変更が送信される可能性があります。

## <a name="known-issues"></a>既知の問題

現在、Change Tracking ソリューションでは、以下の問題が発生しています。

* Windows 10 Creators Update および Windows Server 2016 Core RS3 マシンについては、修正プログラムの更新は収集されていません。

## <a name="change-tracking-data-collection-details"></a>変更の追跡データ収集の詳細

次の表は、変更の種類ごとにデータ収集の頻度を示したものです。 すべての種類について、現在の状態のデータ スナップショットも、少なくとも 24 時間ごとに更新されます。

| **変更の種類** | **頻度** |
| --- | --- |
| Windows レジストリ | 50 分 |
| Windows ファイル | 30 分 |
| Linux ファイル | 約 15 分 |
| Windows サービス | 10 秒から 30 分</br> 既定値: 30 分 |
| Linux デーモン | 5 分 |
| Windows ソフトウェア | 30 分 |
| Linux ソフトウェア | 5 分 |

### <a name="windows-service-tracking"></a>Windows サービスの追跡

Windows サービスに対する既定の収集の頻度は 30 分です。 この頻度を構成するには、**[変更の追跡]** に移動します。 **[Windows サービス]** タブの **[設定の編集]** には、Windows サービスに対する収集の頻度を 10 秒から 30 分まで変更できるスライダーがあります。 このスライダーを必要な頻度に移動すると、その頻度が自動的に保存されます。

![Windows サービスのスライダー](./media/automation-change-tracking/windowservices.png)

エージェントは変更のみを追跡します。これにより、エージェントのパフォーマンスが最適化されます。 しきい値を大きすぎる値に設定すると、サービスがその元の状態に戻した場合、変更が検出されない可能性があります。 頻度を小さな値に設定すると、そうしないと検出されなかった可能性がある変更を捕まえることができます。

> [!NOTE]
> エージェントは変更を 10 秒の間隔まで追跡できますが、データがポータルに表示されるにはまだ数分かかります。 ポータルに表示される期間中の変更も引き続き追跡され、ログに記録されます。
  
### <a name="registry-key-change-tracking"></a>レジストリ キーの変更追跡

レジストリ キーへの変更を監視する目的は、サード パーティのコードやマルウェアがアクティブ化できる拡張性のポイントを正確に特定することです。 次の一覧は、事前構成されたレジストリ キーを示しています。 これらのキーは構成されていますが、有効にはなっていません。 これらのレジストリ キーを追跡するには、それぞれを有効にする必要があります。

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Windows エクスプローラーに直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;スタートアップ時に実行されるスクリプトを監視します。     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;シャットダウン時に実行されるスクリプトを監視します。     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;ユーザーが Windows アカウントにサインインする前に読み込まれるキーを監視します。 このキーは、64 ビット コンピューターで実行される 32 ビット プログラムに使用されます。    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;アプリケーションの設定の変更を監視します。     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows エクスプローラーに直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows エクスプローラーに直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;アイコン オーバーレイ ハンドラーの登録を監視します。|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;64 ビット コンピューターで実行される 32 ビット プログラムのアイコン オーバーレイ ハンドラーの登録を監視します。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、ナビゲーションを制御するときに使用されます。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、64 ビットコンピューターで実行される 32 ビット プログラムのナビゲーションを制御するときに使用されます。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;カスタム ツール メニューやカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;64 ビットコンピューターで実行される 32 ビット プログラムのカスタム ツールのメニューやカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 SYSTEM.INI ファイルの [drivers] セクションに似ています。|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;64 ビットコンピューターで実行される 32 ビット プログラムの wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 SYSTEM.INI ファイルの [drivers] セクションに似ています。|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;既知のまたは一般的に使用されるシステムの DLL の一覧を監視します。このシステムは、システム DLL のトロイの木馬バージョンを削除することで、弱いアプリケーション ディレクトリのアクセス許可が悪用されることを防止します。|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Windows オペレーティング システムの対話型ログオン サポート モデルである Winlogon からイベント通知を受信できるパッケージの一覧を監視します。|

## <a name="use-change-tracking"></a>変更の追跡を使用する

ソリューションを有効にした後、監視対象コンピューターの変更の概要を表示するには、Automation アカウントの **[構成管理]** で **[Change Tracking]\(変更の追跡\)** を選択します。

コンピューターに対する変更を表示し、各イベントの詳細を確認することができます。 グラフ上部にあるドロップ ダウンを使用すると、変更の種類および時間の範囲に基づいて、グラフと詳細情報を制限できます。 グラフ上をクリックしてドラッグすることで、カスタムの時間の範囲を選択することもできます。

![[変更の追跡] ダッシュボードの画像](./media/automation-change-tracking/change-tracking-dash01.png)

変更またはイベントをクリックすると、その変更に関する詳細情報が表示されます。 例に示すように、サービスの起動の種類が手動から自動に変更されました。

![変更追跡の詳細の画像](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>検索ログ

ポータルで提供されている詳細のほか、ログに対して検索を実行できます。 **[Change Tracking]\(変更の追跡\)** ページを開いた状態で、**[Log Analytics]** をクリックします。これにより、**[ログ検索]** ページが開きます。

### <a name="sample-queries"></a>サンプル クエリ

次の表は、このソリューションによって収集された変更レコードを探すログ検索の例です。

|クエリ  |説明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Windows サービスの最新のインベントリ レコードで、自動に設定されたが、停止中として報告されたものを表示します<br>結果はその SoftwareName と Computer の最新のレコードに限定されます      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|削除されたソフトウェアの変更レコードを表示します|

## <a name="next-steps"></a>次の手順

ソリューションの使用方法の詳細については、Change Tracking のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [環境の変更に関する問題を解決する](automation-tutorial-troubleshoot-changes.md)

* [Log Analytics のログ検索機能](../log-analytics/log-analytics-log-searches.md) を使用して、詳細な変更追跡データを確認してください。
