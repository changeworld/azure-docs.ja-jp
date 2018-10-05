---
title: Azure Automation でのソース管理の統合
description: この記事では、Azure Automation での GitHub とのソース管理の統合について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046853"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation でのソース管理の統合

ソース管理を使用すると、Automation アカウントの Runbook を、GitHub または Azure Dev Ops のソース管理リポジトリ内のスクリプトを使用して最新の状態を維持することができます。 ソース管理により、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを簡単に実行できるようになります。 たとえば、開発、テスト、または運用の Automation アカウントに異なるブランチをソース管理で同期できるようになり、開発環境内でテストされたコードを運用の Automation アカウントに昇格することが容易になります。

Azure Automation は、次の 3 種類のソース管理をサポートしています。

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>前提条件

* ソース管理リポジトリ (GitHub または Visual Studio Team Services)
* [実行アカウントと接続](manage-runas-account.md)

> [!NOTE]
> ソース管理の同期ジョブは、ユーザーの Automation アカウントの下で実行され、その他の Automation ジョブと同じレートで課金されます。

## <a name="configure-source-control"></a>ソース管理を構成する

Automation アカウント内で、**[Source Control (preview)] (ソース管理 (プレビュー))** を選択し、**[+ 追加]** をクリックします

![ソース管理の選択](./media/source-control-integration/select-source-control.png)

**[ソース管理の種類]** を選択し、**[認証]** をクリックします。

アプリのアクセス許可の要求ページを確認し、**[Accept] (承認)** をクリックします。

**[Source Control Summary] (ソース管理の概要)** ページで情報を入力し、**[保存]** をクリックします。 次の表に、指定できるフィールドの説明を示します。

|プロパティ  |説明  |
|---------|---------|
|ソース管理名     | ソース管理のわかりやすい名前        |
|ソース管理の種類     | ソース管理のソースの種類。 使用できるオプションは次のとおりです。</br> Github</br>Visual Studio Team Services (Git)</br>Visual Studio Team Services (TFVC)        |
|リポジトリ     | リポジトリまたはプロジェクトの名前。 これは、ソース管理リポジトリから取得されます。 例: $/ContosoFinanceTFVCExample         |
|[Branch]\(ブランチ\)     | ソース ファイルの抽出元のブランチ。 TFVC ソース管理の種類では、ブランチのターゲット設定は使用できません。          |
|フォルダー パス     | 同期する Runbook が含まれているフォルダー。例: /Runbooks         |
|自動同期     | ソース管理リポジトリでコミットが行われたときに、自動同期をオンまたはオフにします         |
|Runbook の発行     | **[オン]** に設定されている場合は、ソース管理から同期されたときに Runbook が自動的に発行されます。         |
|説明     | 詳細情報を入力するテキスト フィールド        |

![ソース管理の概要](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>同期中

ソース管理の統合の構成時に自動同期を設定した場合は、自動的に初期同期が開始されます。 自動同期が設定されていない場合は、**[Source control (Preview)] (ソース管理 (プレビュー))** ページの表でソースを選択します。 **[Start Sync] (同期の開始)** をクリックして、同期プロセスを開始します。  

**[Sync jobs] (同期ジョブ)** タブをクリックすると、現在の同期ジョブまたは以前の同期ジョブの状態を表示することができます。**[ソース管理]** ドロップダウン リストで、ソース管理を選択します。

![同期状態](./media/source-control-integration/sync-status.png)

ジョブをクリックすると、ジョブの出力を表示することができます。 次は、ソース管理の同期ジョブから出力の例です。

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="disconnecting-source-control"></a>ソース管理の切断

ソース管理リポジトリから切断するには、Automation アカウントの **[アカウント設定]** の下の **[Source control (Preview)] (ソース管理 (プレビュー))** を開きます。

削除するソース管理を選択します。 **Source Control Summary (ソース管理の概要)** ページで **削除** をクリックします。

## <a name="next-steps"></a>次の手順

Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
