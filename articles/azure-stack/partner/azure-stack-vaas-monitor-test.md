---
title: Azure Stack VaaS ポータルでのテストの監視と管理 | Microsoft Docs
description: Azure Stack VaaS ポータルでテストを監視および管理します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 03efe32e7a9a29318e4f97ce5636616fad443284
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956665"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>VaaS ポータルでのテストの監視と管理

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack ソリューションに対するテストをスケジュールすると、サービスとしての検証 (VaaS) によってテストの実行状況のレポートが開始されます。 この情報は、テストのスケジュール変更や取り消しなどのアクションと共に VaaS ポータルで利用できます。

## <a name="navigate-to-the-workflow-tests-summary-page"></a>ワークフロー テストの概要ページに移動する

1. ソリューション ダッシュボードで、少なくとも 1 つのワークフローを備えた既存のソリューションを選択します。

    ![ワークフロー タイル](media/tile_all-workflows.png)

1. ワークフロー タイルの **[管理]** を選択します。 次のページに、選択したソリューションのために作成されたワークフローの一覧が表示されます。

1. ワークフロー名を選択して、そのテストの概要を開きます。

## <a name="change-workflow-parameters"></a>ワークフロー パラメーターを変更する

ワークフローの種類ごとに、ワークフローの作成中に指定した[テスト パラメーター](azure-stack-vaas-parameters.md#test-parameters)を編集できます。

1. テストの概要ページで **[編集]** ボタンを選択します。

1. 「[Azure Stack Validation as a Service に使用される一般的なワークフロー パラメーター](azure-stack-vaas-parameters.md)」に従って、新しい値を指定します。

1. **[送信]** を選択して値を保存します。

> [!NOTE]
> **テスト成功**のワークフローでは、テストの選択を完了し、新しいパラメーター値を保存する前に確認ページに移動する必要があります。

### <a name="add-tests-test-pass-only"></a>テストを追加する (テスト成功のみ)

**テスト成功**のワークフローでは、**[テストの追加]** ボタンでも **[編集]** ボタンでも、ワークフローに新しいテストをスケジュールすることができます。

> [!TIP]
> 新しいテストをスケジュールするだけで、**テスト成功**のワークフローのパラメーターを編集する必要がない場合は、**[テストの追加]** を選択します。

## <a name="managing-test-instances"></a>テスト インスタンスを管理する

非公式の実行 (つまり、**テスト成功**のワークフロー) の場合、テストの概要ページには、Azure Stack ソリューションに対してスケジュールされたテストの一覧が表示されます。

公式の実行 (つまり、**検証**のワークフロー) の場合、テストの概要ページには、Azure Stack ソリューションの検証を完了するために必要なテストの一覧が表示されます。 検証テストは、このページでスケジュールされます。

スケジュールされた各テスト インスタンスには、次の情報が示されます。

| 列 | 説明 |
| --- | --- |
| テスト名 | テストの名前とバージョン。 |
| Category | テストの目的。 |
| 作成日時 | テストがスケジュールされた時刻。 |
| 開始済み | テストの実行が開始された時刻。 |
| duration | テストが実行された時間の長さ。 |
| Status | テストの状態または結果。 実行前または進行中の状態は、`Pending`、`Running` です。 終了状態は、`Cancelled`、`Failed`、`Aborted`、`Succeeded` です。 |
| エージェント名 | テストを実行したエージェントの名前。 |
| 操作の合計 | テスト中に試行された操作の総数。 |
| Passed operations (合格した操作) | テスト中に成功した操作の数。 |
|  失敗した操作 | テスト中に失敗した操作の数。 |

### <a name="actions"></a>Actions

テスト インスタンス テーブルのコンテキスト メニュー **[...]** をクリックすると、各テスト インスタンスに、実行できるアクションの一覧が表示されます。

#### <a name="view-information-about-the-test-definition"></a>テストの定義に関する情報を表示する

テストの定義に関する全般的な情報を表示するには、コンテキスト メニューの **[情報の表示]** を選択します。 これは、名前とバージョンが同じ各テスト インスタンスに共有されます。

| テストのプロパティ | 説明 |
| -- | -- |
| テスト名 | テストの名前。 |
| テスト バージョン | テストのバージョン。 |
| 発行元 | テストの発行元。 |
| Category |  テストの目的。 |
| Target services (ターゲット サービス) | テストされている Azure Stack サービス。 |
| 説明 | テストの説明。 |
| Estimated duration (minutes) (見積もり期間 (分)) | テストの予期される実行時間。 |
| リンク | テストの関連情報や連絡先。 |

#### <a name="view-test-instance-parameters"></a>テスト インスタンスのパラメーターを表示する

スケジュール時にテスト インスタンスに指定されたパラメーターを表示するには、コンテキスト メニューの **[View parameters]\(パラメーターの表示\)** を選択します。 パスワードのような機密性の高い文字列は表示されません。 このアクションは、スケジュールされているテストに対してだけ実行できます。

このウィンドウには、すべてのテスト インスタンスの次のメタデータが含まれています。

| テスト インスタンスのプロパティ | 説明 |
| -- | -- |
| テスト名 | テストの名前。 |
| テスト バージョン | テストのバージョン。 |
| Test instance ID (テスト インスタンス ID) | テストの特定のインスタンスを識別する GUID。 |

#### <a name="view-test-instance-operations"></a>テスト インスタンスの操作を表示する

テスト中に実行された操作の詳しい状態を表示するには、コンテキスト メニューの **[操作の表示]** を選択します。 このアクションは、スケジュールされているテストに対してだけ実行できます。

![操作を表示する](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>完了したテスト インスタンスのログをダウンロードする

テストの実行中に出力されたログの `.zip` ファイルをダウンロードするには、コンテキスト メニューの **[ログのダウンロード]** を選択します。 このアクションは、完了したテスト (つまり状態が `Cancelled`、`Failed`、`Aborted`、または `Succeeded` であるテスト) にだけ実行できます。

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>テスト インスタンスをスケジュール変更する、またはテストをスケジュールする

管理ページでテストをスケジュールする方法は、テストを実行するワークフローの種類によって異なります。

##### <a name="test-pass-workflow"></a>テスト成功のワークフロー

テスト成功のワークフローでは、テスト インスタンスを**スケジュール変更**すると、元のテスト インスタンスと同じパラメーターのセットが再利用され、元の結果がログも含めて "*置き換え*" られます。 スケジュール変更するときに、パスワードなどの機密性の高い文字列を再入力する必要があります。

1. テスト インスタンスをスケジュール変更するためのプロンプトを開くには、コンテキスト メニューの **[スケジュール変更]** を選択します。

1. 適用できる任意のパラメーターを入力します。

1. テスト インスタンスをスケジュール変更して既存のインスタンスと置き換えるには、**[送信]** を選択します。

##### <a name="validation-workflows"></a>検証のワークフロー

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>テスト インスタンスを取り消す

スケジュールされたテストは、状態が `Pending` または `Running` である場合に取り消すことができます。  

1. テスト インスタンスを取り消すためのプロンプトを開くには、コンテキスト メニューの **[キャンセル]** を選択します。

1. テスト インスタンスを取り消すには、**[送信]** を選択します。

## <a name="next-steps"></a>次の手順

- [サービスとしての検証のトラブルシューティング](azure-stack-vaas-troubleshoot.md)
