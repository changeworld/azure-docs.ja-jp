<properties
	pageTitle="Azure AD Connect: 接続に関する問題のトラブルシューティング | Microsoft Azure"
	description="Azure AD Connect での接続に関する問題のトラブルシューティング方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="andkjell"/>

# Azure AD Connect での接続に関する問題のトラブルシューティング
この記事では、Azure AD Connect と Azure AD の間の接続のしくみと、接続に関する問題のトラブルシューティング方法について説明します。このような問題は、プロキシ サーバーを備えた環境において発生する可能性が最も高くなります。

## インストール ウィザードにおける接続に関する問題のトラブルシューティング
Azure AD Connect は、2 種類の構成方法によって Azure AD に接続します。インストール ウィザードと同期エンジンは、.NET アプリケーションであるため、machine.config を適切に構成する必要があります。また、サインイン アシスタントへの依存関係もあり、適切に動作するように winhttp を構成することが必要です。

この記事では、Fabrikam がプロキシを介して Azure AD に接続する方法について説明します。プロキシ サーバーは fabrikamproxy という名前で、ポート 8080 を使用しています。

まず、[**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) が正しく構成されていることを確認する必要があります。 ![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [AZURE.NOTE] 一部のブログでは、machine.config ではなく miiserver.exe.config に変更を加える必要があると記載されていますが、このファイルはアップグレードのたびに上書きされるため、初回インストール時には有効であっても、初回アップグレード時には機能しなくなります。このような理由から、machine.config を更新することをお勧めします。

次に、winhttp が構成されていることを確認する必要があります。これには、[**netsh**](active-directory-aadconnect-prerequisites.md#connectivity) を使用します。 ![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

プロキシ サーバーでは、必須となる URL を開いておくことも必要です。「[Office 365 の URL と IP アドレスの範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に公式の URL 一覧が記載されています。

その中でも、次の表に記載したものは Azure AD への接続に最低限必要な URL です。この一覧には、パスワード ライトバックや Azure AD Connect Health のようなオプション機能は含まれていません。ここには、初期構成に関するトラブルシューティングに役立つものが記載されています。

| URL | ポート | 説明 |
| ---- | ---- | ---- |
| mscrl.microsoft.com | HTTP/80 | CRL リストのダウンロードに使用します。 |
| **.verisign.com | HTTP/80 | CRL リストのダウンロードに使用します。 | | *.windows.net | HTTPS/443 | Azure AD へのログインに使用します。 | | *.microsoftonline.com | HTTPS/443 | Azure AD ディレクトリの構成およびデータのインポートとエクスポートに使用します。 |

## ウィザードでのエラー
インストール ウィザードでは、2 種類のセキュリティ コンテキストを使用しています。**[Azure AD に接続]** ページでは、現在サインインしているユーザーを使用します。**[構成]** ページでは、使用するセキュリティ コンテキストを[同期エンジンのサービスを実行しているアカウント](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)に変更します。ここで行うプロキシ構成はコンピューターに対してグローバルになるため、構成になんらかの問題があると、ほとんどの場合はすぐにウィザードの **[Azure AD に接続]** ページに反映されることになります。

インストール ウィザードに表示される最も一般的なエラーを次に示します。

### インストール ウィザードが正しく構成されていない
このエラーは、ウィザード自体がプロキシに接続できない場合に表示されます。 ![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- これが表示された場合は、[machine.config](active-directory-aadconnect-prerequisites.md#connectivity) が正しく構成されていることを確認します。
- 問題がないようなら、「[プロキシ接続を検証する](#verify-proxy-connectivity)」の手順に従って、ウィザード外部でも同じように問題が発生するかどうかを確認してください。

### サインイン アシスタントが正しく構成されていない
このエラーは、サインイン アシスタントがプロキシに接続できないか、プロキシが要求を許可していない場合に表示されます。 ![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- これが表示された場合は、[netsh](active-directory-aadconnect-prerequisites.md#connectivity) でプロキシ構成が正しいことを確認します。 ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- 問題がないようなら、「[プロキシ接続を検証する](#verify-proxy-connectivity)」の手順に従って、ウィザード外部でも同じように問題が発生するかどうかを確認してください。

### パスワードを確認できない
インストール ウィザードによる Azure AD への接続は成功したものの、パスワード自体を確認できない場合は、次のようなメッセージが表示されます。 ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- そのパスワードは一時パスワードで、変更が必要ではないでしょうか。 また、本当に正しいパスワードでしょうか。 (Azure AD Connect サーバーとは別のサーバーで) https://login.microsoftonline.com へのログインを試し、アカウントが使用可能であることを確認してください。
- ユーザーに対して MFA (Multi-Factor Authentication) が有効になっている場合は、 無効にしてください。

### プロキシ接続を検証する
Azure AD Connect サーバーがプロキシおよびインターネットと実際に接続できているかどうかを検証するには、いくつかの PowerShell を使用して、プロキシが Web 要求を許可しているかどうかを確認します。PowerShell プロンプトで、`Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` を実行します(厳密には、最初に呼び出すのは https://login.microsoftonline.com で、同様に機能しますが、応答が速いのは前述の URI です)。

PowerShell は、machine.config 内の構成を使用してプロキシに接続します。winhttp や netsh 内の設定値がこれらのコマンドレットに影響することはありません。

プロキシが正しく構成されていると、成功を示す次のようなステータスが表示されます。 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

"**リモート サーバーに接続できません**" というメッセージが表示された場合、PowerShell は、正しく構成されていないプロキシや DNS を使用せずに直接呼び出しを試みています。**machine.config** ファイルが正しく構成されていることを確認してください。 ![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

プロキシが正しく構成されていない場合、次のエラーが表示されます。 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png) ![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| エラー | エラー テキスト | コメント |
| ---- | ---- | ---- |
| 403 | 許可されていません | 要求された URL に対してプロキシが開かれていません。プロキシ構成を再検討し、[URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) が開かれていることを確認してください。 |
| 407 | プロキシの認証が必要です | プロキシ サーバーがログイン情報を要求しましたが、何も指定されていません。お使いのプロキシ サーバーで認証が必要な場合は、それが machine.config ファイル内で構成されているようにしてください。また、サービス アカウントだけでなく、ウィザードを実行しているユーザーにドメイン アカウントを使用していることを確認してください。 |

## Azure AD Connect と Azure AD の間の通信パターン
上記の手順をすべて実行しても接続できない場合は、その時点でネットワーク ログを確認することをお勧めします。このセクションでは、通常の成功を示す接続パターンについて記載しています。また、ネットワーク ログを確認する場合に無視できる情報についても一覧にまとめています。

- https://dc.services.visualstudio.com への呼び出しがあります。インストールを正常に実行するうえで、この URL をプロキシで開いておくことは必須ではないため、これらの呼び出しは無視できます。
- DNS 解決によって DNS 名前空間の nsatc.net、および microsoftonline.com に属していない他の名前空間に含まれる実際のホストが一覧表示されます。ただし、実際のサーバー名に対する Web サービス要求は発生しないため、これらをプロキシに追加する必要はありません。
- エンドポイントの adminwebservice と provisioningapi (下のログを参照) は検出エンドポイントであり、実際に使用するエンドポイントを見つけるために使用されるため、リージョンによって異なります。

### 参照用プロキシ ログ
実際のプロキシ ログのダンプと、その取得元のインストール ウィザード ページを次に示します (エンドポイントが重複する項目は削除してあります)。これは、お使いの環境でのプロキシおよびネットワーク ログの参照用としてご利用ください。実際のエンドポイントは環境によって異なる場合があります (特に*斜体*で示したもの)。

**Azure AD に接続**

時刻 | URL
--- | ---
1/11/2016 8:31 | connect://login.microsoftonline.com:443
1/11/2016 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:32 | connect://login.microsoftonline.com:443
1/11/2016 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connect://*bwsc02-relay*.microsoftonline.com:443

**構成**

時刻 | URL
--- | ---
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:43 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:43 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connect://*bba800-anchor*.microsoftonline.com:443
1/11/2016 8:44 | connect://login.microsoftonline.com:443
1/11/2016 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connect://*bwsc02-relay*.microsoftonline.com:443

**初期同期**

時刻 | URL
--- | ---
1/11/2016 8:48 | connect://login.windows.net:443
1/11/2016 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba900-anchor*.microsoftonline.com:443
1/11/2016 8:49 | connect://*bba800-anchor*.microsoftonline.com:443

<!---HONumber=AcomDC_0128_2016-->