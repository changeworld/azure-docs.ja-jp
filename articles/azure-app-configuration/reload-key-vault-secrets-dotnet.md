---
title: シークレットと証明書を自動的に再読み込みする
titleSuffix: Azure App Configuration
description: シークレットと証明書を Key Vault から自動的に再読み込みするようにアプリケーションを設定する方法について説明します。
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: 133c788b3bbe787f87cea3e5c72699c8dd0c4f6f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316187"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>Key Vault からシークレットと証明書を自動的に再読み込みする

App Configuration と Key Vault は、多くのアプリケーションで並列的に使用される相補的なサービスです。 Key Vault に格納されたシークレットまたは証明書を参照するキーを自分の App Config ストアに作成することで、App Configuration は両サービスを一緒に使用するのに役立ちます。 Key Vault に証明書の公開および秘密キーのペアがシークレットとして格納されるため、アプリケーションは任意の証明書をシークレットとして Key Vault から取得することができます。

セキュリティの観点から、[シークレット](../key-vault/secrets/tutorial-rotation.md)と[証明書](../key-vault/certificates/tutorial-rotate-certificates.md)を定期的にローテーションするのは良い習慣です。 それらが Key Vault でローテーションされたら、最新のシークレットと証明書の値をアプリケーションに取得させることになるでしょう。 これはアプリケーションを再起動しなくても、2 とおりの方法で実現できます。
- 番兵となるキーと値を更新して構成全体の更新をトリガーし、それによって Key Vault のすべてのシークレットと証明書を再読み込みします。 詳細については、[ASP.NET Core アプリで動的な構成を使用する](./enable-dynamic-configuration-aspnet-core.md)方法に関するページを参照してください。
- Key Vault から一部またはすべてのシークレットと証明書を定期的に再読み込みする。

1 つ目の方法では、Key Vault 内のシークレットと証明書をローテーションするときには必ず、番兵となるキーと値を App Configuration で更新する必要があります。 このアプローチは、アプリケーションへのシークレットと証明書の即時再読み込みを強制したい場合に効果的です。 ただし、Key Vault でシークレットと証明書が自動的にローテーションされても、番兵となるキーと値が時間内に更新されなければアプリケーションでエラーが発生します。 2 つ目の方法では、このプロセスを完全に自動化できます。 ローテーション後の経過時間として許容できる時間内に、シークレットと証明書を Key Vault から再読み込みするようにアプリケーションを構成することが可能です。 このチュートリアルでは、2 つ目の方法について説明します。

## <a name="prerequisites"></a>前提条件

- このチュートリアルでは、シークレットと証明書を Key Vault から自動的に再読み込みするようにアプリケーションを設定する方法について説明します。 これは、Key Vault の参照をコード内に実装するチュートリアルに基づいて作成されています。 先に進む前に、「[チュートリアル: ASP.NET Core アプリで Key Vault 参照を使用する](./use-key-vault-references-dotnet-core.md)」を先に済ませておいてください。

- [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) パッケージ v4.4.0 以降。


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>自動的にローテーションされる証明書を Key Vault に追加する

 「[チュートリアル: Key Vault における証明書の自動ローテーションを構成する](../key-vault/certificates/tutorial-rotate-certificates.md)」に従って、**ExampleCertificate** という自動的にローテーションされる証明書を、前のチュートリアルで作成したキー コンテナーに追加します。


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>Key Vault の証明書への参照を App Configuration に追加する

1. Azure portal で **[すべてのリソース]** を選択し、前のチュートリアルで作成した App Configuration ストア インスタンスを選択します。

1. **[構成エクスプローラー]** を選択します。

1. **[+ 作成]** 、 **[キー コンテナー参照]** の順に選択し、次の値を指定します。
    - **[キー]** : **TestApp:Settings:KeyVaultCertificate** を選択します。
    - **ラベル**:この値は空白のままにしておきます。
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[Key Vault]** : 前のチュートリアルで作成したキー コンテナーに対応する値を入力します。
    - **[シークレット]** : 前のセクションで作成した **ExampleCertificate** という名前のシークレットを選択します。
    - **[シークレットのバージョン]** : **最新バージョン**。

> [!Note]
> 特定のバージョンを参照した場合は、Key Vault からシークレットまたは証明書を再読み込みすることで常に同じ値が返されます。


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>Key Vault のシークレットと証明書を再読み込みするようにコードを更新する

*Program.cs* ファイルの `AddAzureAppConfiguration` メソッドを更新します。自分のキー コンテナーの証明書に対する更新間隔を、`SetSecretRefreshInterval` メソッドを使用して設定します。 この変更によりアプリケーションは、**ExampleCertificate** の公開および秘密キーのペアを 12 時間おきに再読み込みするようになります。

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

`SetSecretRefreshInterval` メソッドの第 1 引数は、App Configuration における Key Vault 参照のキーです。 この引数は省略可能です。 このキー パラメーターを省略した場合、個別の更新間隔が設定されていないすべてのシークレットと証明書に更新間隔が適用されます。

シークレットと証明書が Key Vault から再読み込みされる頻度は、更新間隔によって定義されます。Key Vault または App Configuration の値にどのような変更が加えられたかは関係ありません。 シークレットと証明書の値が App Configuration で変化したときにそれらを再読み込みしたい場合は、`ConfigureRefresh` メソッドを使用してそれらを監視できます。 詳細については、[ASP.NET Core アプリで動的な構成を使用する](./enable-dynamic-configuration-aspnet-core.md)方法に関するページを参照してください。

更新間隔は、シークレットと証明書が Key Vault で更新された後の経過時間として許容できる時間に基づいて選んでください。 スロットルを防ぐために、[Key Vault サービスの制限](../key-vault/general/service-limits.md)を考慮することも大切です。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、シークレットと証明書を Key Vault から自動的に再読み込みするようにアプリケーションを設定する方法について説明しました。 App Configuration と Key Vault へのアクセスを効率化するマネージド ID の使用方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
