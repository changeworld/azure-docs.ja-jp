---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3d93d3aa3e4e646f8e054f96f17bbe4a011d422d
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211403"
---
Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

1. Visual Studio の **[ファイル]** メニューで､ **[新規作成]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** ダイアログ ボックスで `functions` を検索して **[Azure Functions]** テンプレートを選択し、 **[次へ]** を選択します。

1. プロジェクトの名前を入力し、 **[作成]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[新しい Azure Functions アプリケーションの作成]** で、以下のオプションを使用します。

    + **Azure Functions v2 (.NET Core)**
    + **HTTP トリガー**
    + **[ストレージ アカウント]** : **ストレージ エミュレーター**
    + **承認レベル**: **Anonymous** 

    | オプション      | 推奨値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions ランタイム** | **Azure Functions 2.x <br />(.NET Core)** | この設定で、.NET Core をサポートする Azure Functions のバージョン 2.x ランタイムを使用する関数プロジェクトが作成されます。 Azure Functions 1.x では、.NET Framework がサポートされます。 詳細については、[Azure Functions ランタイム バージョンをターゲットにする方法](../articles/azure-functions/functions-versions.md)に関するページを参照してください。   |
    | **関数テンプレート** | **HTTP トリガー** | この設定で、HTTP 要求によってトリガーされる関数が作成されます。 |
    | **ストレージ アカウント**  | **ストレージ エミュレーター** | HTTP トリガーは Azure Storage アカウント接続を使用しません。 他のすべてのトリガーの種類では、有効なストレージ アカウント接続文字列が必要です。 Functions にはストレージ アカウントが必要であるため、プロジェクトを Azure に発行するときに割り当てられるか、作成されます。 |
    | **承認レベル** | **Anonymous** | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[Azure Functions における HTTP と webhook のバインド](../articles/azure-functions/functions-bindings-http-webhook.md)」の「[承認キー](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)」をご覧ください。 |
    
    > [!NOTE]
    > **承認レベル**を `Anonymous` に設定していることを確認します。 `Function` の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)を提示する必要があります。
    
4. **[作成]** を選択して、関数プロジェクトと、HTTP でトリガーされる関数を作成します。
