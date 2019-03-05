---
title: Azure Stack のサービスとしての検証における対話型機能検証テスト | Microsoft Docs
description: サービスとしての検証を使用して Azure Stack 用の対話型機能検証テストを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20a48e67a24763af7bcce9e8831e2a1d1846d094
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594306"
---
# <a name="interactive-feature-verification-testing"></a>対話型機能検証テスト  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

お客様は、対話型機能検証テスト フレームワークを使用して、ご自分のシステムに対するテストを要求できます。 お客様がテストを要求すると、Microsoft はフレームワークを使用して、対話型の手動ステップが必要なテストを準備します。 Microsoft は、フレームワークを使用していくつかの独立した自動テストをつなぎ合わせることができます。

この記事では、シンプルな手動シナリオについて説明します。 Azure Stack におけるディスクの交換がテストでチェックされます。 フレームワークによって、各ステップの診断ログが収集されます。 問題が見つかったら、それらをデバッグできます。 このフレームワークでは、他のツールやプロセスによって生成されたログの共有もできるほか、シナリオに関するフィードバックを送ることができます。

> [!Important]  
> この記事では、ディスク識別を実行する手順を参照します。 テスト成功ワークフローから収集された結果は、新しいソリューションの検証には使用できない可能性があるため、これは単なるデモンストレーションです。

## <a name="overview-of-interactive-testing"></a>対話型テストの概要

ディスク交換に関するテストは一般的なシナリオです。 この例では、テストに 7 つのステップが含まれています。

1. 新しい**テスト成功**ワークフローを作成する
1. **Disk Identification Test (ディスク識別テスト)** を選択する
1. メッセージが表示されたら、手動の手順を完了する
1. シナリオの結果をチェックする
1. テスト結果を Microsoft に送信する

## <a name="create-a-new-test-pass"></a>新しいテスト パスの作成

既存のテスト成功が利用できない場合は、[テストのスケジュール設定](azure-stack-vaas-schedule-test-pass.md)の指示に従ってください。

## <a name="schedule-the-test"></a>テストのスケジュール設定

1. **[Disk Identification Test]\(ディスク識別テスト\)** を選択します。

    > [!Note]  
    > テストのバージョン番号は、2 次テストの改良が行われると増加します。 Microsoft による別段の指示がない限り、常に最新バージョンを使用することが推奨されます。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. **[編集]** を選択して、ドメイン管理者ユーザー名とパスワードを指定します。

1. テストの起動先として適切なテスト実行エージェントまたは DVM を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. **[送信]** を選択してテストを開始します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. 前の手順で選択したエージェントから対話型テストの UI にアクセスします。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. **[ドキュメント]** と **[検証]** のリンク先に移動して、このシナリオの実施方法に関する Microsoft からの指示を確認します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. **[次へ]** を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. 手順に従って事前チェック スクリプトを実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. 事前チェック スクリプトが正常に完了したら、**[情報]** タブの **[ドキュメント]** リンクと **[検証]** リンクのとおりに、手動シナリオ (ディスク交換) を実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 手動シナリオを実施している際中は、ダイアログ ボックスを閉じないでください。

1. 手動シナリオを実施し終えたら、手順に従って事後チェック スクリプトを実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. 手動シナリオ (ディスク交換) が正常に完了した後、**[次へ]** を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > ウィンドウを閉じると、テストは完了する前に停止します。

1. 実行したテストについてのフィードバックを送信します。 これらの質問は、Microsoft がシナリオの成功率とリリース品質を評価するのに役立てられます。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Microsoft に送信したいログ ファイルを添付します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. フィードバック送信の EULA に同意します。

1. **[送信]** を選択して、結果を Microsoft に送信します。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)
