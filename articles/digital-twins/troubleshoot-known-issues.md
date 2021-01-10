---
title: 既知の問題 - Azure Digital Twins
description: Azure Digital Twinds の既知の問題を認識し、軽減するのに役立ちます。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
ms.openlocfilehash: a9735e355244d51464c66c10e02f97f03d2e67cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673472"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins の既知の問題

この記事では、Azure Digital Twins に関連する既知の問題について説明します。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell の "400 Client Error:Bad Request"

**問題の説明:** *https://shell.azure.com* で実行されている Cloud Shell のコマンドが断続的に失敗して、エラー "400 Client Error:Bad Request for url http://localhost:50342/oauth2/token" を受け取り、その後で完全なスタック トレースが示される場合があります。

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| これは&nbsp;Azure&nbsp;Digital&nbsp;Twins では次のコマンド グループに影響します。<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | これは Cloud Shell の既知の問題の結果です。"[*Cloud Shell からのトークンの取得が断続的に失敗し、400 Client Error:Bad Request を受け取る*](https://github.com/Azure/azure-cli/issues/11749)"<br><br>これは、Azure Digital Twins インスタンスの認証トークンと、Cloud Shell の既定方式である[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) ベースの認証に問題があることを示しています。 <br><br>これは、`az dt` または `az dt endpoint` コマンド グループからの Azure Digital Twins コマンドには影響しません。なぜなら、これらのコマンドでは別のタイプの認証トークン (Azure Resource Manager ベース) を使用し、Cloud Shell のマネージド ID 認証でも問題が発生しないからです。 | 1 つの解決方法は、Cloud Shell で `az login` コマンドを再実行し、その後のログイン手順を完了することです。 これにより、マネージド ID 認証からセッションが切り替わり、根本的な問題が回避されます。 この後、コマンドを再実行できるようになります。<br><br>または、Azure portal の [Cloud Shell] ペインを開き、そこから Cloud Shell 作業を完了することができます。<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure portal のアイコン バーにある Cloud Shell アイコンの画像" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>最終的には、別の解決策として、コンピューターに [Azure CLI をインストール](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)して、Azure CLI コマンドをローカルで実行できるようにします。 ローカル CLI ではこの問題は発生しません。 |


## <a name="missing-role-assignment-after-scripted-setup"></a>スクリプトを使用したセットアップ後にロールの割り当てが存在しない

**問題の説明:** ユーザーによっては、[*方法: インスタンスと認証の設定 (スクリプト化) に関する記事のロールの割り当て部分で問題が発生することがあります。インスタンスと認証の設定 (スクリプト化)* ](how-to-set-up-instance-scripted.md) に関するページを参照してください。 このスクリプトには失敗と示されてはいませんが、"*Azure Digital Twins データ所有者*" ロールがユーザーに正常に割り当てられておらず、この問題は、今後その他のリソースを作成する機能に影響します。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| スクリプトの実行後にロールの割り当てが正常に設定されたかどうかを確認するには、セットアップの記事にある「[*ユーザー ロールの割り当てを確認する*](how-to-set-up-instance-scripted.md#verify-user-role-assignment)」の手順に従ってください。 ユーザーに対してこのロールが表示されていない場合、この問題による影響があります。 | 個人の [Microsoft アカウント (MSA)](https://account.microsoft.com/account) でログインしているユーザーの場合、このようなコマンドにおいてユーザーの識別に使用されるユーザーのプリンシパル ID が、ユーザーのサインイン メールとは異なる場合があります。そのため、スクリプトによってこれを検出し、使用してロールを適切に割り当てることが困難になります。 | これを解決するには、[CLI のインストラクション](how-to-set-up-instance-cli.md#set-up-user-access-permissions)または[Azure portal のインストラクション](how-to-set-up-instance-portal.md#set-up-user-access-permissions)を使用して、手動でロールの割り当てを設定します。 |

## <a name="issue-with-interactive-browser-authentication"></a>対話型ブラウザーの認証に関する問題

**問題の説明:** **[Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) ライブラリ** のバージョン **1.2.0** を使用して Azure Digital Twins アプリケーションで認証コードを記述すると、[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) メソッドで問題が発生することがあります。 これにより、ブラウザー ウィンドウで認証を試みるときに "Azure.Identity.AuthenticationFailedException" というエラー応答が表示されます。 ブラウザー ウィンドウが完全に起動できなくなるか、一見ユーザーが正常に認証されたかのように見せてクライアント アプリケーションがエラーで失敗します。

| 影響 | 原因 | 解決方法 |
| --- | --- | --- |
| 影響を受けるメソッドは、次の記事で使用されています。&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br><br>[*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)<br><br>[*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)<br><br>[*方法: Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md) | 一部のユーザーは、`Azure.Identity` ライブラリのバージョン **1.2.0** でこの問題に直面しています。 | 解決するには、`Azure.Identity` の[最新バージョン](https://www.nuget.org/packages/Azure.Identity)を使用するようにアプリケーションを更新します。 ライブラリのバージョンを更新すると、ブラウザーが想定どおりに読み込まれ、認証されるはずです。 |

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)