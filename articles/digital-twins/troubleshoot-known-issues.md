---
title: 既知の問題のトラブルシューティング
titleSuffix: Azure Digital Twins
description: Azure Digital Twinds の既知の問題を認識し、軽減するのに役立ちます。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 10/6/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: b80da6d98a2b70f9c0d8d84922e9043bef114d80
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133616"
---
# <a name="troubleshooting-azure-digital-twins-known-issues"></a>Azure Digital Twins のトラブルシューティング: 既知の問題

この記事では、Azure Digital Twins に関連する既知の問題について説明します。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell の "400 Client Error:Bad Request"

**問題の説明:** *https://shell.azure.com* で実行されている Cloud Shell のコマンドが断続的に失敗して、エラー "400 Client Error:Bad Request for url http://localhost:50342/oauth2/token" を受け取り、その後で完全なスタック トレースが示される場合があります。

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| この問題は、&nbsp;Azure&nbsp;Digital&nbsp;Twins では次のコマンド グループに影響します。<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | これは、Cloud Shell での既知の問題の結果です。[Cloud Shell からトークンを取得すると、400 Client Erro: Bad Request で断続的に失敗します](https://github.com/Azure/azure-cli/issues/11749)。<br><br>これは、Azure Digital Twins インスタンスの認証トークンと、Cloud Shell の既定の[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) ベースの認証に問題があることを示しています。 <br><br>これは、`az dt` または `az dt endpoint` コマンド グループからの Azure Digital Twins コマンドには影響しません。なぜなら、これらのコマンドでは別の種類の認証トークン (Azure Resource Manager ベース) を使用し、Cloud Shell のマネージド ID 認証でも問題が発生しないからです。 | この問題を解決するための 1 つの方法は、Cloud Shell で `az login` コマンドを再実行し、その後のログイン手順を完了することです。 このアクションにより、マネージド ID 認証からセッションが切り替わり、根本的な問題が回避されます。 その後、コマンドを再実行できます。<br><br>または、Azure portal の [Cloud Shell] ペインを開き、そこから Cloud Shell 作業を完了することができます。<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure portal のアイコン バーにある Cloud Shell アイコンのスクリーンショット。" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>最終的には、別の解決策として、コンピューターに [Azure CLI をインストール](/cli/azure/install-azure-cli)して、Azure CLI コマンドをローカルで実行できるようにします。 ローカル CLI ではこの問題は発生しません。 |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure.Identity 1.2.0 での対話型ブラウザーの認証に関する問題

**問題の説明**: [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) ライブラリのバージョン **1.2.0** を使用して Azure Digital Twins アプリケーションで認証コードを記述すると、[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) メソッドで問題が発生することがあります。 この問題により、ブラウザー ウィンドウで認証を試みるときに "Azure.Identity.AuthenticationFailedException" というエラー応答が表示されます。 ブラウザー ウィンドウが完全に起動できなくなるか、一見ユーザーが正常に認証されたかのように見せてクライアント アプリケーションがエラーで失敗します。

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| 影響を受けるメソッドは、次の記事で使用されています。&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br><br>[クライアント アプリをコーディングする](tutorial-code.md)<br><br>[アプリ認証コードを作成する](how-to-authenticate-client.md)<br><br>[Azure Digital Twins API および SDK](concepts-apis-sdks.md) | 一部のユーザーは、`Azure.Identity` ライブラリのバージョン **1.2.0** でこの問題に直面しています。 | 解決するには、`Azure.Identity` の[新しいバージョン](https://www.nuget.org/packages/Azure.Identity)を使用するようにアプリケーションを更新します。 ライブラリのバージョンを更新すると、ブラウザーが想定どおりに読み込まれ、認証されるはずです。 |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure.Identity 1.3.0 での既定の Azure 資格情報認証に関する問題

**問題の説明**: [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) ライブラリのバージョン **1.3.0** を使用して認証コードを記述している場合、一部のユーザーに、これらの Azure Digital Twins ドキュメントを通じて多くのサンプルで使用される [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) メソッドに関する問題が発生しています。この問題により、コードから認証を試みるときに "Azure.Identity.AuthenticationFailedException: SharedTokenCacheCredential 認証に失敗しました" というエラー応答が表示されます。

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| `DefaultAzureCredential` は、認証を含むこのサービスのドキュメント例のほとんどで使用されます。 `Azure.Identity` ライブラリのバージョン 1.3.0 で `DefaultAzureCredential` を使用して認証コードを記述し、このエラー メッセージが表示される場合は、この問題の影響を受けます。 | これは、`Azure.Identity` の構成に何らかの問題があるためである可能性があります。 | この問題を解決する方法の 1 つは、現在 `Azure.Identity` に対して開かれているこの [DefaultAzureCredential イシュー](https://github.com/Azure/azure-sdk/issues/1970)で説明されているように、ご利用の資格情報から `SharedTokenCacheCredential` を除外することです。<br>別の方法として、[バージョン 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3) など、以前のバージョンの `Azure.Identity` を使用するようにアプリケーションを変更することもできます。 以前のバージョンの使用は、Azure Digital Twins に機能的な影響を与えないため、ソリューションとして受け入れることができます。 |

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)