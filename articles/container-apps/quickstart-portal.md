---
title: 'クイックスタート: Azure portal を使用して最初のコンテナー アプリをデプロイする'
description: 最初のアプリケーションを Azure Container Apps プレビューにデプロイします。
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: v-bcatherine
ms.custom: ''
ms.openlocfilehash: e2f2bf2cafc60bca637bee3309f4b721965f30fd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710282"
---
# <a name="quickstart-deploy-your-first-container-app-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して最初のコンテナー アプリをデプロイする

Azure Container Apps プレビューを使用すると、サーバーレス プラットフォームでマイクロサービスとコンテナー化されたアプリケーションを実行できます。 Container Apps を使用すると、コンテナーを実行するメリットが得られますが、クラウド インフラストラクチャと複雑なコンテナー オーケストレーターを手動で管理するという問題が残ります。

このクイックスタートでは、セキュリティで保護された Container Apps環境を作成し、Azure portal を使用して最初のコンテナー アプリをデプロイします。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウントが必要です。 まだアカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ことができます。

## <a name="setup"></a>セットアップ

最初に [Azure portal](https://portal.azure.com) にサインインします。
<!--
Do we need to include steps to login?  Probably not..
-->

## <a name="create-a-container-app"></a>コンテナー アプリの作成

コンテナー アプリを作成するには、Azure portal ホーム ページから開始します。

1. 上部の検索バーで「**コンテナー アプリ**」を検索します。
1. 検索結果で **コンテナー アプリ** を選択します。
1. **[作成]** ボタンを選択します。

### <a name="basics-tab"></a>[基本] タブ

*[基本]* タブで、次の操作を行います。

#### <a name="enter-project-details"></a>プロジェクトの詳細を入力します。

| 設定 | アクション |
|---|---|
| サブスクリプション | Azure サブスクリプションを選択します。 |
| Resource group | **[新規作成]** を選択し、「**my-container-apps**」と入力します。 |
| コンテナー アプリ名 |  「**my-container-app**」と入力します。 |

#### <a name="create-an-environment"></a>環境の作成
 
1. *[Create Container App environment]\(コンテナー アプリ環境の作成\)* フィールドで、 **[新規作成]** を選択します。
1. *[基本]* タブの *[Create Container App environment]\(コンテナー アプリ環境の作成\)* タブで、次の値を入力します。

    | 設定 | 値 |
    |---|---|
    | 環境名 | 「**my-environment**」と入力します。 | 
    | リージョン | **[カナダ中部]** を選択します。 |

1. **[監視]** タブを選択して、Log Analytics ワークスペースを作成します。
1. **[Log Analytics ワークスペース]** で *[新規作成]* を選択します。
1. *[Log Analytics ワークスペースの新規作成]* ダイアログ ボックスの *[名前]* フィールドに、「**my-container-apps-logs**」と入力します。
  
    *[場所]* フィールドには、 *[カナダ中部]* があらかじめ入力されています。
1. **[OK]** を選択します。
1. *[Create Container App environment]\(コンテナーアプリ環境の作成\)* ページの下部にある **[作成]** ボタンを選択します。
1. ページの下部にある **[次へ: アプリ設定]** ボタンを選択します。

### <a name="app-settings-tab"></a>[アプリ設定] タブ

*[アプリ設定]* タブで、次の操作を実行します。

| 設定 | アクション |
|---|---|
| クイックスタート イメージを使用する | **チェック ボックス** をオフンにします。 |
| 名前 | 「**my-app**」と入力します。 <!-- I don't know what name to use --> |
| イメージのソース | **[Docker Hub またはその他のレジストリ]** を選択します。 |
| イメージの種類 | **[パブリック]** を選択します。 |
| レジストリ ログイン サーバー | 「`mcr.microsoft.com`」と入力します。 |  
| [Image and tag]\(イメージとタグ\) | 「 **/azuredocs/containerapps-helloworld:latest**」と入力します。 |

#### <a name="application-ingress-settings"></a>アプリケーション イングレス設定

| 設定 | アクション |
|---|---|
| イングレス | **[Enabled]** を選択します。 |
| イングレスの可視性 | **[External]** を選択します。 |
| ターゲット ポート | 「**80**」と入力します。 |

### <a name="deploying-the-container-app"></a>コンテナー アプリのデプロイ

1. ページの下部にある **[確認と作成]** ボタンを選択します。  

    次に、コンテナー アプリの設定が検証されます。 エラーが見つからなかった場合は、 *[作成]* ボタンが有効になります。  

    エラーが発生した場合は、エラーが含まれているタブに赤い点が表示されます。  適切なタブに移動します。エラーが含まれているフィールドは、赤で強調表示されます。  すべてのエラーを修正したら、 **[確認と作成]** をもう一度選択します。

1. **作成** を選択します。

    "*デプロイが進行中です*" というメッセージを含むページが表示されます。  デプロイが正常に完了すると、"*デプロイが完了しました*" というメッセージが表示されます。

### <a name="view-your-deployed-application"></a>デプロイされたアプリケーションを表示する

**[リソースに移動]** を選択して、新しいコンテナー アプリを表示します。  アプリケーションを表示するには、 *[アプリケーションの URL]* の横にあるリンクを選択します。 ブラウザーに次のメッセージが表示されます。

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Your first Azure Container Apps deployment. \(Azure Container Apps:の最初のデプロイ\)":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、リソース グループを削除することで、Azure Container Apps インスタンスと関連付けられているすべてのサービスを削除できます。

1. *[概要]* セクションで、**my-container-apps** リソース グループを選択します。
1. リソース グループ ページの *[概要]* の上部で **[リソース グループの削除]** ボタンを選択します。
1. ["my-container-apps" を削除しますか?] 確認ダイアログにリソース グループ名「**my-container-apps**」を入力します *。*
1. **[削除]** を選択します。  
    リソース グループを削除するプロセスが完了するまでに数分かかる場合があります。


> [!TIP]
> 問題がある場合は、 GitHub の [Azure Container Apps リポジトリ](https://github.com/microsoft/azure-container-apps)でイシューを開いて、お知らせください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Container Apps の環境](environment.md)
