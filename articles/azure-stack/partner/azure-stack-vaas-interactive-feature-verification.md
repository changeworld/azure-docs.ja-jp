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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972067"
---
# <a name="interactive-feature-verification-testing"></a>対話型機能検証テスト  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

お客様は、対話型機能検証テスト フレームワークを使用して、ご自分のシステムに対するテストを要求できます。 お客様がテストを要求すると、Microsoft はフレームワークを使用して、対話型の手動ステップが必要なテストを準備します。 Microsoft は、フレームワークを使用していくつかの独立した自動テストをつなぎ合わせることができます。

この記事では、シンプルな手動シナリオについて説明します。 Azure Stack におけるディスクの交換がテストでチェックされます。 フレームワークによって、各ステップの診断ログが収集されます。 問題が見つかったら、それらをデバッグできます。 このフレームワークでは、他のツールやプロセスによって生成されたログの共有もできるほか、シナリオに関するフィードバックを送ることができます。

## <a name="overview-of-a-test-pass"></a>テスト パスの概要

ディスク交換に関するテストは一般的なシナリオです。 この例では、テストに 7 つのステップが含まれています。

1.  新しい**テスト パス** ワークフローを作成する。
2.  **Disk Identification Test** を選択する。
3.  テストを開始する。
4.  対話型検証シナリオでのアクションを選択する。
5.  シナリオの結果をチェックする。
6.  テスト結果を Microsoft に送信する。
7.  VaaS ポータルで状態をチェックする。

## <a name="create-a-new-test-pass"></a>新しいテスト パスの作成

1.  [Azure Stack 検証ポータル](https://www.azurestackvalidation.com)に移動して、サインインします。

2.  新しいソリューションを作成するか、既存のものを選択します。

3.  **[テスト パス]** タイルで **[開始]** を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  **テスト パス** ワークフローの名前を入力します。

5.  「[Azure Stack のサービスとしての検証のワークフロー共通パラメーター](azure-stack-vaas-parameters.md)」という記事の手順に従って、環境パラメーターと共通テスト パラメーターを入力します。

6.  診断接続文字列は、[ワークフロー共通パラメーター](azure-stack-vaas-parameters.md)に関する記事の「[テスト パラメーター](azure-stack-vaas-parameters.md#test-parameters)」セクションで指定されている形式にする必要があります。

7.  テストに必須のパラメーターを入力します。

8.  対話型テストを実行するエージェントを選択します。

> [!Note]  
> ディスク識別の対話型機能検証テストに対して、ドメイン管理者ユーザーとパスワードを指定する必要があります。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>テストの選択

1.  **Disk Identification Test \<バージョン>** を選択します。

    > [!Note]  
    > テストのバージョン番号は、2 次テストの改良が行われると増加します。 Microsoft による別段の指示がない限り、常に最新バージョンを使用することが推奨されます。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  **[編集]** を選択して、ドメイン管理者ユーザーとパスワードを指定します。

3.  テストの起動先として適切なテスト実行エージェントまたは DVM を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  **[送信]** を選択してテストを開始します。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>テストの開始

Disk Identification Test のプロンプトが、VaaS エージェントが実行されるコンピューターに表示されます。 これは通常、Azure Stack インスタンス用の DVM またはジャンプボックスです。

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>アクションの選択

1.  **[ドキュメント]** と **[検証]** のリンク先に移動して、このシナリオの実施方法に関する Microsoft からの指示を確認します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  **[次へ]** を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  手順に従って事前チェック スクリプトを実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  事前チェック スクリプトが正常に完了したら、**[情報]** タブの **[ドキュメント]** リンクと **[検証]** リンクのとおりに、手動シナリオ (ディスク交換) を実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  手動シナリオを実施している際中は、ダイアログ ボックスを閉じないでください。

6.  手動シナリオを実施し終えたら、手順に従って事後チェック スクリプトを実行します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  手動シナリオ (ディスク交換) が正常に完了した後、**[次へ]** を選択します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> ウィンドウを閉じると、テストは完了する前に停止します。

## <a name="check-the-status"></a>状態をチェックする

1.  テストが完了したら、フィードバックを送るよう求められます。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  これらの質問は、Microsoft がシナリオの成功率とリリース品質を評価するのに役立てられます。

## <a name="send-the-test-result"></a>結果の送信

1.  Microsoft に送信したいログ ファイルを添付します。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  フィードバック送信の EULA に同意します。

3.  **[送信]** を選択して、結果を Microsoft に送信します。

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)
