---
title: Azure Stack のサービスとしての検証でテストを監視する | Microsoft Docs
description: Azure Stack のサービスとしての検証でテストを監視します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 553d2a0e4bf6b23f5d8ab200f533d9245bf72d36
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286586"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Azure Stack のサービスとしての検証でテストを監視する

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

テストの実行は、処理中または完了済みのテスト スイートの **[操作]** ページを見て監視できます。 このページでは、テストとその操作の状態について詳しく説明します。

## <a name="monitor-a-test"></a>テストの監視

1. ソリューションを選択します。

2. いずれかのワークフロー タイルの **[管理]** を選択します。

3. ワークフローをクリックしてそのテスト サマリー ページを開きます。

4. いずれかのテスト スイートのインスタンスについて、そのコンテキスト メニュー (**[...]**) を展開します。

5. **[操作の表示]** を選択します。

![Alt text](media\image4.png)

実行が完了したテストについては、テスト サマリー ページからログをダウンロードできます。テストのコンテキスト メニュー (**[...]**) にある **[ログのダウンロード]** をクリックしてください。Azure Stack のパートナーは、これらのログを使って、不合格となったテストの問題をデバッグできます。

## <a name="open-the-test-pass-summary"></a>テスト パスのサマリーを開く

1. ポータルを開きます。 
2. 実行済みのテストまたはスケジュールされているテストを含んだ既存のソリューションの名前を選択します。

    ![テスト パスの管理](media/managetestpasses.png)

3. **[Test Passes]\(テスト パス\)** パネルの **[管理]** を選択します。
4. テスト パスを選択して、テスト パスのサマリーを開きます。 テストの名前、作成日、実行日、所要時間、結果 (成功または失敗) を確認することができます。
5. [ **. .  .** ] を選択します。

### <a name="test-pass-summary"></a>テスト パスのサマリー

| 分割 | 説明 |
| --- | --- |
| テスト名 | テストの名前。 検証番号が引用されます。 |
| 作成日時 | テスト パスが作成された時刻。 |
| 開始済み | テスト パスが実行された時刻。 |
| duration | テスト パスの実行に要した時間。 |
| Status | 残りのパスの結果 (成功または失敗)。 |
| エージェント名 | エージェントの完全修飾ドメイン名。 |
| 操作の合計 | テスト パスで試行された操作の総数。 |
| Passed operations (合格した操作) | テスト パスで合格した操作の数。 |
|  失敗した操作 | 失敗した操作の数。 |

### <a name="group-columns-in-the-test-pass-summary"></a>テスト パスのサマリーに表示される列のグループ化

列を選択してヘッダーにドラッグすることで、列の値に基づくグループを作成できます。

## <a name="reschedule-a-test"></a>テストのスケジュール変更

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. テスト パスのスケジュールを変更するには、**[スケジュール変更]** を選択します。
3. Azure Stack インスタンスのクラウド管理者のパスワードを入力します。
4. アカウントを設定するときに定義した診断ストレージの接続文字列を入力します。
5. テストのスケジュールを変更します。

## <a name="cancel-a-test"></a>テストのキャンセル

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. **[キャンセル]** を選択します。

## <a name="get-test-information"></a>テスト情報の取得

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. テスト パスのスケジュールを変更するには、**[詳細の表示]** を選択します。

**テスト情報**

| Name | 説明 |
| -- | -- |
| テスト名 | テストの名前 (例: OEM Update on Azure Stack 1806 RC Validation)。 |
| テスト バージョン | テストのバージョン (例: 5.1.4.0)。 |
| 発行元 | テストの発行元 (Microsoft など)。 |
| Category | テストのカテゴリ (**機能**、**信頼性**など)。 |
| Target services (ターゲット サービス) | テスト対象のサービス (VirtualMachines など)。 |
| 説明 | テストの説明。 |
| Estimated duration (minutes) (見積もり期間 (分)) | テストの実行に要した時間 (分)。 |
| リンク | GitHub Issue Tracker へのリンク。 |

## <a name="get-test-parameters"></a>テスト パラメーターの取得

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. テスト パスのスケジュールを変更するには、**[パラメーターの表示]** を選択します。

**パラメーター**

| Name | 説明 |
| -- | -- |
| テスト名 | テストの名前 (例: oemupdate1806test)。 |
| テスト バージョン | テストのバージョン (例: 5.1.4.0)。 |
| Test instance ID (テスト インスタンス ID) | テストの特定のインスタンスを識別する GUID (例: 20b20645-b400-4f0d-bf6f-1264d866ada9)。 |
| cloudAdminUser | クラウド管理者として使用されるアカウントの名前 (例: **cloudadmin**)。 |
| DiagnosticsContainerName | 診断コンテナーの ID (例: 04dd3815-5f35-4158-92ea-698027693080)。 |

## <a name="get-test-operations"></a>テスト操作の取得

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. テスト パスのスケジュールを変更するには、**[操作の表示]** を選択します。 操作のサマリー ウィンドウが開きます。

## <a name="get-test-logs"></a>テスト ログの取得

1. [テスト パスのサマリーを開きます](#open-the-test-pass-summary)。
2. テスト パスのスケジュールを変更するには、**[ログのダウンロード]** を選択します。  
    ログを含んだ ReleaseYYYY-MM-DD.zip という名前の ZIP ファイルがダウンロードされます。

## <a name="next-steps"></a>次の手順

- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
