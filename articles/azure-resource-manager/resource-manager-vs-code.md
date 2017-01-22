---
title: "VS Code で Resource Manager テンプレートを操作する | Microsoft Docs"
description: "Visual Studio Code を設定し、Azure Resource Manager テンプレートを作成する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: cmatskas
manager: timlt
editor: tysonn
ms.assetid: 78f2aa22-df1d-41bd-92ec-dabd1175db88
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: chmatsk;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 10c7051c9b1218081d95cb10403006bfd95126ba
ms.openlocfilehash: 2ac1c2cce7a9e045990894b0bbaa045df3d48954


---
# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Visual Studio Code で Azure Resource Manager テンプレートを操作する
Azure Resource Manager テンプレートは、リソースおよび関連する依存関係を記述する JSON ファイルです。 これらのファイルは大きく複雑になることがあるため、ツールのサポートが重要です。 Visual Studio Code は新しく軽量なオープン ソースのクロスプラットフォーム コード エディターです。 [新たな拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)で Resource Manager テンプレートの作成と編集をサポートします。 VS Code はどこでも実行でき、インターネット アクセスを必要とするのは Resource Manager テンプレートを Azure サブスクリプションにデプロイするときのみです。

まだ VS Code を入手していない場合は、 [https://code.visualstudio.com/](https://code.visualstudio.com/)からインストールできます。

## <a name="install-the-resource-manager-extension"></a>Resource Manager 拡張機能のインストール
VS Code で JSON テンプレートを操作するには、拡張機能のインストールが必要です。 次の手順で Resource Manager JSON テンプレートの言語サポートをダウンロードおよびインストールします。

1. VS Code を起動します。 
2. クイック オープンを行います (Ctrl + P キーを押します)。 
3. 次のコマンドを実行します。 
   
        ext install azurerm-vscode-tools
4. 拡張機能を有効にするように求められたら、VS Code を再起動します。 
   
   これで完了です。

## <a name="set-up-resource-manager-snippets"></a>Resource Manager のスニペットの設定
前の手順でツールのサポートをインストールしましたが、次は JSON テンプレートのスニペットが使えるように、VS Code を構成する必要があります。

1. ファイルの内容を [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) リポジトリからクリップボードにコピーします。
2. VS Code を起動します。 
3. VS Code では、2 とおりの方法で JSON スニペットを開くことができます。1 つ目の方法は、**[ファイル]** -> **[ユーザー設定]** -> **[User Snippets (ユーザー スニペット)]** -> **[JSON]** の順に移動します。 2 つ目の方法は、**F1** キーを押し、"**Preferences: Snippets**" を選択できるようになるまで「**preferences**」と入力します。
   
    ![preference snippets](./media/resource-manager-vs-code/preferences-snippets.png)
   
    オプションから **JSON**を選択します。
   
    ![select json](./media/resource-manager-vs-code/select-json.png)
4. 手順 1. のファイルの内容を、ユーザー スニペット ファイルの最後の "}" の前に貼り付けます。 
5. JSON が正しく記述されており、どこにも波線が表示されていないことを確認します。 
6. ユーザー スニペット ファイルを保存して閉じます。

Resource Manager スニペットの使用を開始するために必要な作業は以上です。 次に、この設定をテストします。

## <a name="work-with-template-in-vs-code"></a>VS Code でのテンプレートの操作
テンプレートの操作を手軽に開始するには、 [GitHub](https://github.com/Azure/azure-quickstart-templates) にあるいずれかのクイック スタート テンプレートを取得するか、独自のテンプレートを使用してください。 どのリソース グループでも、ポータルを通じて簡単に [テンプレートをエクスポート](resource-manager-export-template.md) できます。 

1. リソース グループからテンプレートをエクスポートした場合は、抽出したファイルを VS Code で開きます。
   
    ![show files](./media/resource-manager-vs-code/show-files.png)
2. template.json ファイルを開き、編集とリソースの追加ができるようにします。 `"resources": [` の後で Enter キーを押し、新しい行を開始します。 「**arm**」と入力すると、オプションの一覧が表示されます。 これらのオプションは、インストールしたテンプレートのスニペットです。 
   
    ![show snippets](./media/resource-manager-vs-code/type-snippets.png)
3. 使用するスニペットを選択してください。 この記事では、新しいパブリック IP アドレスを作成するために " **arm-ip** " を選択します。 新しく作成されたリソースの右中かっこ `}` の後ろにコンマを入力し、テンプレートの構文が有効であることを確認します。
   
     ![add comma](./media/resource-manager-vs-code/add-comma.png)
4. VS Code には IntelliSense が組み込まれています。 テンプレートの編集の際、VS Code は使用可能な値を提案します。 たとえば、テンプレートに変数のセクションを追加するには、`""` (2 つの二重引用符) を追加し、この引用符の間で **Ctrl + Space** キーを押します。 "**variables**" を含むオプションが表示されます。
   
    ![add variables](./media/resource-manager-vs-code/add-variables.png)
5. IntelliSense は、使用可能な値または関数も提示します。 パラメーターの値にプロパティを設定するには、`"[]"` と **Ctrl + Space** キーを使って式を作成します。 関数名の入力を開始します。 使用する関数を見つけたら、 **Tab** キーを押します。
   
    ![add parameter](./media/resource-manager-vs-code/select-parameters.png)
6. テンプレートで使用可能なパラメーターの一覧を表示するには、関数内でもう一度 **Ctrl + Space** キーを押します。
   
    ![add parameter](./media/resource-manager-vs-code/select-avail-parameters.png)
7. テンプレートのスキーマ検証で問題があった場合、エディターにはいつもの波線が表示されます。 エラーと警告の一覧を見るには、 **Ctrl + Shift + M** キーを押すか、左下のステータス バーのグリフを選択します。
   
    ![errors](./media/resource-manager-vs-code/errors.png)
   
    テンプレートを検証すると、構文の問題の検出に役立ちますが、無視できるエラーも表示される場合があります。 場合によっては、エディターがテンプレートを最新ではないスキーマと比較し、正しい箇所もエラーとして報告する場合があります。 たとえば、最近 Resource Manager に関数が 1 つ追加されたにもかかわらず、スキーマが更新されていなかったとします。 エディターは、デプロイ中に関数が正常に機能したにもかかわらず、エラーを報告します。
   
    ![error message](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>新しいリソースのデプロイ
テンプレートの準備が整ったら、次の手順で新しいリソースをデプロイできます。 

### <a name="windows"></a>Windows
1. PowerShell コマンド プロンプトを開きます。 
2. ログインするには、次のように入力します。 
   
  ```powershell
  Login-AzureRmAccount
  ```

3. 複数のサブスクリプションがある場合は、次のように入力して、サブスクリプションの一覧を取得します。

  ```powershell 
  Get-AzureRmSubscription
  ```
   
    その後、使用するサブスクリプションを選択します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionId <Subscription Id>
  ```

4. parameters.json ファイルのパラメーターを更新します。
5. Deploy.ps1 を実行して、Azure 上にテンプレートをデプロイします。

### <a name="osxlinux"></a>OSX/Linux
1. ターミナル ウィンドウを開きます。 
2. ログインするには、次のように入力します。

  ```azurecli
  azure login
  ```

3. 複数のサブスクリプションがある場合は、次のようにして適切なサブスクリプションを選択します。

  ```azurecli
  azure account set <subscriptionNameOrId> 
  ```

4. parameters.json ファイルのパラメーターを更新します。
5. 次のコマンドを実行して、テンプレートをデプロイします。

  ```azurecli 
  azure group deployment create -f <PathToTemplate>
  ``` 

## <a name="next-steps"></a>次のステップ
* テンプレートの詳細については、「 [Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* テンプレート関数の詳細については、「 [Azure Resource Manager のテンプレートの関数](resource-group-template-functions.md)」を参照してください。
* Visual Studio Code を使用した作業の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) [2015 Connect](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) のデモで紹介された「[Build cloud apps with Visual Studio Code (Visual Studio Code を使用したクラウド アプリの構築)](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code)」を参照してください。 HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。




<!--HONumber=Jan17_HO1-->


