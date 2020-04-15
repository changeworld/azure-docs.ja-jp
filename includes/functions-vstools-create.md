---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 477f3cf270377bc1341e65ab500093e05277afa8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657508"
---
Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

1. Visual Studio メニューで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** の検索ボックスに「*functions*」と入力し、**Azure Functions** テンプレートを選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトの**プロジェクト名**を入力し、 **[作成]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

1. **[Create a new Azure Function Application]\(新しい Azure 関数アプリケーションの作成\)** 設定で、次の表の値を使用します。

    | 設定      | 値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions ランタイム** | **Azure Functions v2 <br />(.NET Core)** | この値は、.NET Core をサポートする Azure Functions のバージョン 2.x ランタイムを使用する関数プロジェクトを作成します。 Azure Functions 1.x では、.NET Framework がサポートされます。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](../articles/azure-functions/functions-versions.md)」をご覧ください。   |
    | **関数テンプレート** | **HTTP トリガー** | この値は、HTTP 要求によってトリガーされる関数を作成します。 |
    | **ストレージ アカウント**  | **ストレージ エミュレーター** | Azure Function にはストレージ アカウントが必要であるため、プロジェクトを Azure に発行するときに割り当てられるか、作成されます。 HTTP トリガーによって、Azure Storage アカウントの接続文字列が使用されることはありません。その他のすべてのトリガーの種類には、有効な Azure Storage アカウントの接続文字列が必要です。  |
    | **アクセス権** | **Anonymous** | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[承認キー](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)」と [HTTP と Webhook のバインド](../articles/azure-functions/functions-bindings-http-webhook.md)に関するページをご覧ください。 |
    

    
    ![Azure Functions プロジェクトの設定](./media/functions-vs-tools-create/functions-project-settings.png)

    **[アクセス権]** を **[匿名]** に設定していることを確認します。 **関数**の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)を提示する必要があります。

1. **[作成]** を選択して、関数プロジェクトと HTTP トリガー関数を作成します。
