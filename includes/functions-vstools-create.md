---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a8be74558fb71ceb1c28c27004b27a5a5f477a8f
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650703"
---
Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio で、**[ファイル]** メニューから **[新規]** > **[プロジェクト]** の順に選択します。

2. **[新しいプロジェクト]** ダイアログで、**[インストール済み]** を選択し、**[Visual C#]** > **[クラウド]** の順に展開して **[Azure Functions]** を選択します。プロジェクトの**名前**を入力して、**[OK]** をクリックします。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

    ![Visual Studio で関数を作成するための [新しいプロジェクト] ダイアログ](./media/functions-vstools-create/functions-vs-new-project.png)

3. 図の下の表に示した設定を使用してください。

    ![Visual Studio の [新しい関数] ダイアログ](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Setting      | 推奨値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **バージョン** | Azure Functions 2.x <br />(.NET Core) | これは、.NET Core をサポートする Azure Functions のバージョン 2.x ランタイムを使用する関数プロジェクトを作成します。 Azure Functions 1.x では、.NET Framework がサポートされます。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](../articles/azure-functions/functions-versions.md)」をご覧ください。   |
    | **テンプレート** | HTTP トリガー | これは、HTTP 要求によってトリガーされる関数を作成します。 |
    | **ストレージ アカウント**  | ストレージ エミュレーター | HTTP トリガーはストレージ アカウント接続を使用しません。 他のすべてのトリガーの種類では、有効なストレージ アカウント接続文字列が必要です。 |
    | **アクセス権** | Anonymous | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[Azure Functions における HTTP と webhook のバインド](../articles/azure-functions/functions-bindings-http-webhook.md)」の「[承認キー](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)」をご覧ください。 |
4. **[OK]** をクリックして、関数プロジェクトと、HTTP でトリガーされる関数を作成します。
