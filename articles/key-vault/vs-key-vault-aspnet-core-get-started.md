---
title: Visual Studio での Key Vault 接続済みサービスの概要 (ASP.NET Core プロジェクト) | Microsoft Docs
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
ms.openlocfilehash: e591771ee9c9cb12d9ec2ff61ec7f5a76691c8c7
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143232"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Visual Studio での Key Vault 接続済みサービスの概要 (ASP.NET Core プロジェクト)

> [!div class="op_single_selector"]
> - [作業の開始](vs-key-vault-aspnet-core-get-started.md)
> - [変更内容](vs-key-vault-aspnet-core-what-happened.md)

この記事では、Visual Studio の **[接続済みサービスの追加]** コマンドを使用してキー コンテナーを ASP.NET Core プロジェクトに追加した後のガイダンスを提供します。 プロジェクトにまだサービスを追加していない場合は、「[Visual Studio 接続済みサービスを使用して Web アプリケーションに Key Vault を追加する](vs-key-vault-add-connected-service.md)」の手順に従って、いつでも追加することができます。

接続済みサービスを追加したときにプロジェクトに加えられる変更については、「[ASP.NET Core プロジェクトの変更点](vs-key-vault-aspnet-core-what-happened.md)」を参照してください。

## <a name="after-you-connect"></a>接続後

1. Azure 内のキー コンテナーにシークレットを追加します。 ポータルの適切な場所に移動するには、このキー コンテナーに格納される管理シークレット用のリンクをクリックします。 ページまたはプロジェクトが閉じている場合は、[Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択し、**[セキュリティ]** の下の **[Key Vault]** を選択します。次に、先ほど作成したキー コンテナーを選択することで、同じ場所に移動できます。

   ![ポータルに移動する](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 作成したキー コンテナーの [Key Vault] セクションで、**[シークレット]**、**[生成/インポート]** の順に選択します。

   ![シークレットを生成/インポートする](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 「MySecret」などのシークレットを入力し、テストとして任意の文字列値を指定した後、**[作成]** ボタンを選択します。

   ![シークレットの作成](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (省略可能) 別のシークレットを入力しますが、今回は「**Secrets--MySecret**」という名前を付けることでカテゴリに配置します。 この構文は、**MySecret** というシークレットを含む **Secrets** というカテゴリを指定します。
1. Visual Studio プロジェクトで、次の式を使用して、シークレットをコードで参照できるようになりました。
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

これで、安全に格納されているシークレットに Web アプリがキー コンテナーを使用してアクセスできるようになりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、キー コンテナーと関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、**[削除]** を選択します。

# <a name="next-steps"></a>次の手順

Key Vault での開発の詳細については、「[Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください