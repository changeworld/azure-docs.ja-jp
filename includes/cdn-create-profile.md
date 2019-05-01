---
title: インクルード ファイル
description: インクルード ファイル
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c58b226c0f3bd63cb2a54bd3d8c91eb750a26f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684659"
---
## <a name="create-a-new-cdn-profile"></a>新しい CDN プロファイルを作成する

CDN プロファイルは、CDN エンドポイントのコンテナーであり、価格レベルを指定します。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。 
    
    **[新規]** ウィンドウが表示されます。
   
2. **[Web + モバイル]**、**[CDN]** の順に選択します。
   
    ![CDN リソースの選択](./media/cdn-create-profile/cdn-new-resource.png)

    **[CDN のプロファイル]** ウィンドウが表示されます。

3. CDN プロファイルの設定については、次の表で指定されている値を使います。
   
    | Setting  | 値 |
    | -------- | ----- |
    | **Name** | プロファイル名として「*my-cdn-profile-123*」と入力します。 この名前はグローバルに一意でなければなりません。この名前が既に使用されている場合は、別の名前を入力してください。 |
    | **サブスクリプション** | ドロップダウン リストから Azure サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択し、リソース グループ名として「*my-resource-group-123*」と入力します。 この名前はグローバルに一意である必要があります。 既に使用している場合は、別の名前を入力するか、**[既存のものを使用]** を選択し、ドロップダウン リストから **[my-resource-group-123]** を選択できます。 | 
    | **リソース グループの場所** | ドロップダウンから **[米国中部]** を選択します。 |
    | **[価格レベル]** | ドロップダウンから **[Standard Verizon]** を選択します。 |
    | **今すぐ新しい CDN エンドポイントを作成する** | オフのままにします。 |  
   
    ![新しい CDN プロファイル](./media/cdn-create-profile/cdn-new-profile.png)

4. プロファイルを作成後にダッシュボードに保存するには、**[ダッシュボードにピン留め]** をオンにします。
    
5. **[作成]** を選択してプロファイルを作成します。 

    **Azure CDN Standard from Microsoft** プロファイルのみの場合、プロファイル完了は通常 2 時間以内で完了します。 

