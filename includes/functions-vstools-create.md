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
ms.openlocfilehash: 16bda26a80611b29fdb100736cfc48978e63f75a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739217"
---
Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio で、**[ファイル]** メニューから **[新規]** > **[プロジェクト]** の順に選択します。

2. **[新しいプロジェクト]** ダイアログで、**[インストール済み]** を選択し、**[Visual C#]** > **[クラウド]** の順に展開して **[Azure Functions]** を選択します。プロジェクトの**名前**を入力して、**[OK]** をクリックします。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

    ![Visual Studio で関数を作成するための [新しいプロジェクト] ダイアログ](./media/functions-vstools-create/functions-vstools-add-new-project.png)

3. 図の下の表に示した設定を使用してください。

    ![Visual Studio の [新しい関数] ダイアログ](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Setting      | 推奨値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **バージョン** | Azure Functions v1 <br />(.NET Framework) | これは、Azure Functions のバージョン 1 ランタイムを使用する関数を作成します。 .NET Core をサポートするバージョン 2 ランタイムは、現在プレビュー中です。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](../articles/azure-functions/functions-versions.md)」をご覧ください。   |
    | **テンプレート** | HTTP トリガー | これは、HTTP 要求によってトリガーされる関数を作成します。 |
    | **ストレージ アカウント**  | ストレージ エミュレーター | HTTP トリガーはストレージ アカウント接続を使用しません。 他のすべてのトリガーの種類では、有効なストレージ アカウント接続文字列が必要です。 |
    | **アクセス権** | 匿名 | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[Azure Functions における HTTP と webhook のバインド](../articles/azure-functions/functions-bindings-http-webhook.md)」の「[承認キー](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)」をご覧ください。 |
4. **[OK]** をクリックして、関数プロジェクトと、HTTP でトリガーされる関数を作成します。

