---
title: "Azure App Service へのローカル Git デプロイ"
description: "Azure App Service へのローカル Git デプロイを有効にする方法を説明します。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: cfowler
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 948c54a2e9be2260d0a7d2cce31b67ffbbd23d03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service へのローカル Git デプロイ

このチュートリアルでは、ローカル コンピューター上の Git リポジトリから [Azure Web Apps](app-service-web-overview.md) にアプリをデプロイする方法を説明します。 この方法は、App Service により、[Azure Portal] の **ローカル Git** デプロイ オプションでサポートされています。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Git インストール バイナリを[こちら](http://www.git-scm.com/downloads)でダウンロードできます。
* Git の基本的な知識。
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)にしてください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
>

## <a name="Step1"></a>手順 1: ローカル リポジトリを作成する

次のタスクを実行して、新しい Git リポジトリを作成します。

1. **Git Bash** (Windows) や **Bash** (Unix シェル) などのコマンドライン ツールを起動します。 OS X システムでは、**ターミナル** アプリケーションを使用してコマンドラインにアクセスできます。
1. デプロイするコンテンツが配置されるディレクトリに移動します。
1. 次のコマンドを使用して、新しい Git リポジトリを初期化します。

  ```bash
  git init
  ```

## <a name="Step2"></a>手順 2: コンテンツをコミットする

App Service では、さまざまなプログラミング言語で作成されたアプリケーションをサポートしています。

1. リポジトリにまだコンテンツが含まれていない場合は、次のように静的 .html ファイルを追加するか、このステップをスキップします。
   * テキスト エディターを使用して、Git リポジトリのルートに **index.html** という名前の新しいファイルを作成します。
   * index.html ファイルの内容として次のテキストを追加し、ファイルを保存します: "*Hello Git!*"
1. コマンド ラインで、Git リポジトリのルートにいることを確認します。 次のコマンドを使用して、リポジトリにファイルを追加します。

        git add -A 
1. 次に、次のコマンドを使用して、リポジトリへの変更をコミットします。

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>手順 3: App Service アプリのリポジトリを有効にする

次に示している手順を実行して、App Service アプリに対して Git リポジトリを有効にします。

1. [Azure Portal] にログインします。
1. App Service アプリのビューで、**[設定]、[展開元]** の順にクリックします。 **[ソースの選択]** をクリックし、**[ローカル Git リポジトリ]** をクリックして **[OK]** をクリックします。

    ![ローカルの Git リポジトリ](./media/app-service-deploy-local-git/local_git_selection.png)

1. Azure で初めてリポジトリを設定した場合、そのログイン資格情報を作成する必要があります。 それらを使用して、Azure リポジトリにログインし、ローカル Git リポジトリから変更をプッシュします。 Web アプリのビューで **[設定]、[デプロイ資格情報]** の順にクリックし、デプロイ用のユーザー名とパスワードを構成します。 終了したら **[保存]**をクリックします。

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>手順 4: プロジェクトをデプロイする

次に示している手順に従って、ローカル Git を使用してアプリを App Service に発行します。

1. Azure Portal の対象 Web アプリのビューで、**[設定]、[プロパティ]** の順にクリックし、**[Git の URL]** を確認します。

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Git URL** は、ローカル リポジトリからデプロイする際のリモート参照です。 次の手順では、この URL を使います。
1. コマンド ラインを使用して、現在の位置がローカル Git リポジトリのルートであることを確認します。
1. `git remote` を使用して、手順 1 の **[Git の URL]** にリストされたリモート参照を追加します。 コマンドは、次のようになります。

    ```bash
    git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git
    ```

   > [!NOTE]
   > この **remote** コマンドは、名前付きの参照をリモート リポジトリに追加します。 この例では、Web アプリのリポジトリに「azure」という名前の参照を作成しています。
   >

1. 作成した新しい **azure** リモートを使用して、コンテンツを App Service にプッシュします。

    ```bash
    git push azure master
    ```

    Azure Portal でデプロイの資格情報をリセットしたときに作成したパスワードの入力を求められます。 パスワードを入力します (パスワードを入力しても Gitbash によってコンソールにアスタリスクはエコーされません)。 
1. Azure Portal 内のアプリに戻ります。 最近使用したプッシュのログ エントリが **[デプロイ]** ビューに表示されます。

    ![](./media/app-service-deploy-local-git/deployment_history.png)

1. Web アプリのページ上部の **[参照]** ボタンをクリックして、コンテンツがデプロイされていることを確認します。

## <a name="Step5"></a>トラブルシューティング

Git を使用して Azure の App Service に発行する場合に発生する一般的なエラーまたは問題を以下に示します。

---
**症状**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**: このエラーはアプリが実行されていない場合に発生する可能性があります。

**解決策**: Azure Portal でアプリを起動します。 Web アプリが停止している間は、Git デプロイは利用できません。

---
**症状**: `Couldn't resolve host 'hostname'`

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生します。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

---
**症状**: 参照が共通していないか、指定されていません。未対応です。 『master』 などのブランチを指定する必要があります。

**原因**: このエラーは、git push 操作を実行するときにブランチを指定せず、Git で使用される push.default 値を設定していない場合に発生します。

**解決策**: master ブランチを指定して、もう一度 push 操作を実行します。 例: 

```bash
git push azure master
```

---
**症状**: `src refspec [branchname] does not match any.`

**原因**: このエラーは、"azure" リモートの master 以外のブランチにプッシュしようとした場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。 例: 

```bash
git push azure master
```

---
**症状**: `RPC failed; result=22, HTTP code = 5xx.`

**原因**: このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生します。

**解決策**: ローカル コンピューター上の Git 構成を変更して postBuffer を増やします。

```bash
git config --global http.postBuffer 524288000
```

---
**症状**: `Error - Changes committed to remote repository but your web app not updated.`

**原因**: このエラーは、必要な追加モジュールを指定する package.json ファイルを含む Node.js アプリをデプロイする場合に発生します。

**解決策**: "npm ERR!" を含む追加のメッセージが ログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。 このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。 メッセージ:

* **形式が正しくない package.json ファイル**: npm ERR! Couldn't read dependencies.
* **Windows 用のバイナリ配布がないネイティブ モジュール**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      または
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>その他のリソース

* [Git に関するドキュメント](http://git-scm.com/documentation)
* [Project Kudu に関するドキュメント](https://github.com/projectkudu/kudu/wiki)
* [Azure App Service への継続的なデプロイ](app-service-continuous-deployment.md)
* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)](/powershell/azure/overview)
* [Azure コマンド ライン インターフェイスの使用方法](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure Command-Line Interface]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
