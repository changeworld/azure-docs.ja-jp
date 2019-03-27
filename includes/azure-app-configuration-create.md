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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885125"
---
1. 新しいアプリ構成ストアを作成するには、まず [Azure portal](https://aka.ms/azconfig/portal) にサインインします。 ページの左上で、**[+ リソースの作成]** をクリックします。 **[Marketplace を検索]** テキスト ボックスに「**App Configuration**」と入力し、**Enter** キーを押します。

    ![App Configuration を検索する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. 検索結果の **[アプリの構成]** をクリックし、**[作成]** をクリックします。

3. **[アプリの構成]** > **[作成]** ページで、次の設定を入力します。

    | Setting | 推奨値 | 説明 |
    |---|---|---|
    | **リソース名** | グローバルに一意の名前 | アプリ構成ストア リソースに使用する一意のリソース名を入力します。 名前は 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 名前の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  |
    | **サブスクリプション** | 該当するサブスクリプション | App Configuration のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、**[サブスクリプション]** ドロップダウンは表示されません。 |
    | **リソース グループ** | *AppConfigTestResources* | アプリ構成ストア リソースのリソース グループを選択または作成します。 このグループは複数のリソースを整理し、そのリソース グループを削除することで複数のリソースを同時削除できるため便利です。 詳細については、[リソース グループを使用した Azure リソースの管理](/azure/azure-resource-manager/resource-group-overview)に関するページを参照してください。 |
    | **場所** | *米国中部* | **[場所]** を使用して、アプリ構成ストアがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成することをお勧めします。 |

    ![アプリ構成ストア リソースを作成する](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. **Create** をクリックしてください。 デプロイが完了するまでに数分かかる場合があります。

5. デプロイが完了したら、**[設定]** > **[アクセス キー]** をクリックします。 プライマリ読み取り専用キー接続文字列またはプライマリ読み取り/書き込みキー接続文字列を書き留めます。 これは、作成したアプリ構成ストアと通信するための構成を後でアプリケーションに対して行う際に使用します。

6. **[キー/値のエクスプローラー ]** と **[+ 作成]** をクリックして、次のキーと値のペアを追加します。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 白 |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | black |
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておいてください。
