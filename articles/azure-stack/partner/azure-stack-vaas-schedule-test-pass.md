---
title: Azure Stack のサービスとしての検証ポータルを使用して初めてのテストをスケジュールする | Microsoft Docs
description: Azure Stack のサービスとしての検証ポータルを使用して初めてのテストをスケジュールします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1fbda6c0351287b9bc7574d100c0862b172a0aed
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650975"
---
# <a name="schedule-your-first-test"></a>最初のテストのスケジュール設定

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) ポータルで、Azure Stack ソリューションのテストをスケジュールします。 VaaS ソリューションは、特定のハードウェア部品表 (BoM) を含む Azure Stack ソリューションを表します。 ハードウェアが Azure Stack を実行できることを確認するためのテストをスケジュールできます。

ソリューションを確認するには、テストのワークフローを作成します。 VaaS ワークフローは、VaaS ソリューションのコンテキスト内で動作します。 ワークフローは、ハードウェア上で Azure Stack デプロイの機能を実行する一連のテスト スイートを表します。 ソリューションの環境パラメーターを追加し、ソリューションで実行する 1 つ以上のテストを選択します。

テスト成功ワークフローを使用して、VaaS で提供されるテスト (検証ワークフローに含まれているテストなど) を実行できますが、テスト成功ワークフローの結果は "*公式*" とは見なされません。 公式の検証ワークフローについては、「[ワークフロー](azure-stack-vaas-key-concepts.md#workflows)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行する前に、次の項目を完了してください。

- [サービスとしての検証のリソースを設定する](azure-stack-vaas-set-up-resources.md)
- [ローカル エージェントをデプロイする](azure-stack-vaas-local-agent.md) (推奨)
- [サービスとしての検証の主要概念](azure-stack-vaas-key-concepts.md) (推奨)

## <a name="start-a-workflow"></a>ワークフローの開始

![VaaS ポータルにサインインする](media/vaas_portalsignin.png)

ポータルにサインインし、ソリューションを選択するか、ソリューションを作成してそのソリューションを選択します。

1. [VaaS ポータル](https://azurestackvalidation.com)にサインインします。
2. 既存のソリューションの名前を入力するか、**[新しいソリューション]** を選択して新しいソリューションを作成します。 手順については、「[VaaS ポータルでソリューションを作成する](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal)」をご覧ください。
3. **[Test Passes]\(テスト成功\)** タイルで **[開始]** を選択します。

## <a name="specify-parameters"></a>パラメーターの指定

![代替テキスト](media/vaas_test_pass_parameters.png)

ソリューションのワークフローを定義します。 ワークフローには、ソリューションのテストに使用されるプロセス ステップが含まれます。

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. **[次へ]** を選択して、スケジュールするテストを選択します。

## <a name="select-tests-to-run"></a>実行するテストの選択

ワークフローで実行するテストを選択します。

1. ワークフローで実行するテストを選択します。

    テストの共通パラメーター (前のセクションで指定したパラメーター) を上書きする場合は、横の **[編集]** リンクを選択して新しい値を指定します。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. **[次へ]** を選択して、ワークフローを確認します。

## <a name="review-and-submit"></a>確認と送信

ワークフローを確認、作成し、スケジュールします。

1. 表示された情報を確認します。

    サービスにより、提供した情報を使用してワークフローが作成され、選択したテストがスケジュールされます。

    誤りがある場合は、**[前へ]** ボタンを使用して前のセクションに移動します。

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>次の手順

- [VaaS ポータルでのテストの監視と管理](azure-stack-vaas-monitor-test.md)
