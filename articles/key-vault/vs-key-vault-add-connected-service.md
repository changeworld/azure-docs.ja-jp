---
title: Visual Studio を使用して ASP.NET プロジェクトに Key Vault のサポートを追加する | Microsoft Docs
description: このチュートリアルを使用すると、ASP.NET または ASP.NET Core Web アプリケーションに Key Vault のサポートを追加する方法を学習できます。
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 5b3cea87e7762e492432722c54a1a8aaa342b84a
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146763"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 接続済みサービスを使用して Web アプリケーションに Key Vault を追加する

このチュートリアルでは、Azure Key Vault を使用して Web プロジェクト用のシークレットを管理するために必要なもののすべてを、Visual Studio で簡単に追加する方法を学習します。使用するプロジェクトは、ASP.NET Core プロジェクトでも、その他の種類の ASP.NET プロジェクトでもかまいません。 Visual Studio 2017 の接続済みサービスの機能を使用することで、Azure 内の Key Vault に接続するために必要なすべての NuGet パッケージと構成設定の追加を Visual Studio に自動的に実行させることができます。 

接続済みサービスが Key Vault を有効にするためにプロジェクト内で実行する変更の詳細については、[Key Vault 接続済みサービス - ASP.NET 4.7.1 プロジェクトで何が起こるか](vs-key-vault-aspnet-what-happened.md)、または[Key Vault 接続済みサービス - ASP.NET Core プロジェクトで何が起こるか](vs-key-vault-aspnet-core-what-happened.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- **Visual Studio 2017 バージョン 15.7**(**Web 開発**ワークロードもインストールされます)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。
- ASP.NET (Core ではありません) では .NET Framework 4.7.1 開発ツールが必要ですが、これは既定ではインストールされません。 インストールするには、Visual Studio インストーラーを起動し、**[変更]** を選択します。次に、**[個々 のコンポーネント]** を選択した後、右側にある **[ASP.NET と Web 開発]** を展開し、**[.NET Framework 4.7.1 開発ツール]** を選択します。
- ASP.NET 4.7.1 または ASP.NET Core 2.0 Web プロジェクトが開きます。

## <a name="add-key-vault-support-to-your-project"></a>Key Vault のサポートをプロジェクトに追加する

1. **ソリューション エクスプローラー**で、**[追加]** > **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。
1. 使用可能なサービスのメニューから、**[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\)** を選択します。

   ![[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\) を選択する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。
1. 使用するサブスクリプションを選択し、新規または既存の Key Vault を選択するか、自動的に生成された名前を変更するために [編集] リンクを選択します。

   ![サブスクリプションを選択します。](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Key Vault で使用する名前を入力します。

   ![Key Vault の名前を変更し、リソース グループを選択する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 既存のリソース グループを選択するか、自動的に生成された一意の名前を持つ新しいパーティションの作成を選択します。  新しいグループを別の名前で作成する場合は、[Azure Portal](https://portal.azure.com) を使用できます。その後、ページを閉じ、再起動を行ってリソース グループの一覧を再度読み込みます。
1. Key Vault を作成するリージョンを選択します。 Web アプリケーションが Azure でホストされる場合は、Web アプリケーションを最適なパフォーマンスでホストするリージョンを選択します。
1. 価格レベルを選択します。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。
1. [OK] を選択して、構成の選択を受け入れます。
1. **[追加]** を選択して Key Vault を作成します。 既に使用されている名前を選択した場合、作成処理は失敗することがあります。  これが発生した場合は、**[編集]** リンクを使用して Key Vault の名前を変更し、もう一度やり直してください。

   ![接続済みサービスをプロジェクトに追加する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 次に、Azure 内の Key Vault にシークレットを追加します。 ポータルの適切な場所に移動するには、この Key Vault に格納される管理シークレット用のリンクをクリックします。 ページまたはプロジェクトが閉じている場合は、[Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択し、**[セキュリティ]** の下の **[Key Vault]** を選択します。次に、先ほど作成したキー コンテナーを選択することで、同じ場所に移動できます。

   ![ポータルに移動する](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 作成したキー コンテナーの [Key Vault] セクションで、**[シークレット]**、**[生成/インポート]** の順に選択します。

   ![シークレットを生成/インポートする](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 「MySecret」などのシークレットを入力し、テストとして任意の文字列値を指定した後、**[作成]** ボタンを選択します。

   ![シークレットの作成](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (省略可能) 別のシークレットを入力しますが、今回は「Secrets--MySecret」 という名前を付けることでカテゴリに配置します。 この構文は、"MySecret" というシークレットを含む "Secrets" というカテゴリを指定します。
 
これで、コードでシークレットにアクセスできます。 次の手順は、ASP.NET 4.7.1 または ASP.NET Core のどちらを使用しているかに応じて異なります。

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>コードでシークレットにアクセスする (ASP.NET Core プロジェクト)

1. Visual Studio では、ASP.NET Core プロジェクトの中で、次の式を使用して、シークレットをコードで参照できるようになりました。
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. .cshtml ページ (たとえば About.cshtml) で、@inject ディレクティブをファイルの先頭付近に追加して、Key Vault 構成にアクセスするために使用できる変数を設定します。

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. テストとして、いずれかのページにシークレットの値を表示することで、それが使用可能であることを確認できます。 構成変数を参照するには、@config を使用します。
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Web アプリケーションをビルドして実行します。[バージョン情報] ページに移動してシークレットの値を確認します。

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>コードでシークレットにアクセスする (ASP.NET 4.7.1 プロジェクト)

1. 次のように web.config を変更します。 キーは、AzureKeyVault ConfigurationBuilder によって Key Vault 内のシークレットの値に置き換えられるプレースホルダーです。

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. HomeController の About コントローラー メソッドに、シークレットを取得して ViewBag に格納する次の行を追加します。
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. About.cshtml ビューに、シークレットの値を表示する次の行を追加します (テスト目的専用)。

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

これで、Web アプリが Key Vault を使用して安全に格納されているシークレットにアクセスできることが確認されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、キー コンテナーと関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

Key Vault の開発の詳細については、「[Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください