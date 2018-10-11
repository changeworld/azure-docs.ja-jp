---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: fb521fc9d4927a953cdd66948101969dfc102de4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843390"
---
## <a name="register-your-application"></a>アプリケーションの登録
2 つのどちらかの方法でアプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1: 簡易モード
次の手順を実行すると、アプリケーションをすばやく登録できます。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)に移動します。

2. **[アプリの追加]** を選択します。

3. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。

4. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオンになっていることを確認し、**[作成]** を選択します。

5. 手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-advanced-mode"></a>オプション 2: 詳細モード
アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
1. アプリケーションをまだ登録していない場合は、[Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動します。

2. **[アプリの追加]** を選択します。

3. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。 

4. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。

5. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。

6. **[アプリケーション ID]** ボックスで、GUID をコピーします。

7. Visual Studio に移動し、*App.xaml.cs* ファイルを開き、`your_client_id_here` を登録してコピーしたアプリケーション ID に置き換えます。

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
