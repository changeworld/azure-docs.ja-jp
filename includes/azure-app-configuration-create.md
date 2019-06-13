---
title: インクルード ファイル
description: インクルード ファイル
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393288"
---
1. 新しい App Configuration ストアを作成するには、[Azure portal](https://portal.azure.com) にサインインします。 ウィンドウの左上隅にある **[+ リソースの作成]** を選択します。 **[Marketplace を検索]** ボックスに「**App Configuration**」と入力し、Enter キーを押します。

    ![App Configuration を検索する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. 検索結果の **[アプリ構成]** を選択し、 **[作成]** を選択します。

1. **[アプリ構成]**  >  **[作成]** ウィンドウで、次の設定を入力します。

    | Setting | 推奨値 | 説明 |
    |---|---|---|
    | **リソース名** | グローバルに一意の名前 | App Configuration ストア リソースに使用する一意のリソース名を入力します。 名前は 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 名前の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  |
    | **サブスクリプション** | 該当するサブスクリプション | App Configuration のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、 **[サブスクリプション]** の一覧は表示されません。 |
    | **リソース グループ** | *AppConfigTestResources* | App Configuration ストア リソースのリソース グループを選択または作成します。 このグループで複数のリソースをまとめておくと、そのリソース グループを削除することで複数のリソースを同時に削除できるため、便利です。 詳細については、[リソース グループを使用した Azure リソースの管理](/azure/azure-resource-manager/resource-group-overview)に関するページを参照してください。 |
    | **場所** | *米国中部* | **[場所]** を使用して、アプリ構成ストアがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成します。 |

    ![App Configuration ストア リソースを作成する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. **作成** を選択します。 デプロイが完了するまでに数分かかることがあります。

1. デプロイが完了したら、 **[設定]**  >  **[アクセス キー]** の順に選択します。 プライマリ読み取り専用キー接続文字列またはプライマリ読み取り/書き込みキー接続文字列を書き留めます。 この接続文字列は、後で、作成した App Configuration ストアと通信するようにアプリケーションを構成する際に使用します。
