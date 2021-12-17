---
title: Azure Arc 対応サーバー エージェントの接続に関する問題をトラブルシューティングする
description: この記事では、サービスに接続しようとしたときに Azure Arc 対応サーバーで発生する Connected Machine エージェントに関する問題をトラブルシューティングして解決する方法を示します。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 8547e66ee8915c356b345bceb3cc52bde63713b0
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390169"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Azure Arc 対応サーバー エージェントの接続に関する問題をトラブルシューティングする

この記事では、Windows または Linux 向けの Azure Arc 対応サーバー Connected Machine エージェントを構成しようとしている間に発生する可能性がある問題のトラブルシューティングと解決に関する情報を提供します。 サービスへの接続を構成するときの対話型インストール手法と大規模インストール手法の両方が含まれます。 一般的な情報については、[Arc 対応サーバーの概要](./overview.md)に関するページを参照してください。

## <a name="agent-error-codes"></a>エージェントのエラー コード

Azure Arc 対応サーバー エージェントを構成しているときにエラーが表示された場合は、可能性の高い原因と、問題を解決するための推奨される手順を識別するために次の表が役立ちます。 続行するには、コンソールまたはスクリプト出力に表示された `AZCM0000` ("0000" は任意の 4 桁の数字) エラーコードが必要になります。

| エラー コード | 考えられる原因 | 推奨される修復方法 |
|------------|----------------|-----------------------|
| AZCM0000 | アクションは成功しました | 該当なし |
| AZCM0001 | 不明なエラーが発生しました | 詳細については、Microsoft サポートにお問い合わせください |
| AZCM0011 | ユーザーがアクションを取り消しました (CTRL+C) | 前のコマンドを再試行してください |
| AZCM0012 | 指定されたアクセス トークンは無効です | 新しいアクセス トークンを取得して、もう一度お試しください |
| AZCM0013 | 指定されたタグは無効です | タグが二重引用符で囲まれ、コンマで区切られていること、およびスペースを含む名前または値が単一引用符で囲まれていることを確認します: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | クラウドが無効です | サポートされているクラウド (`AzureCloud` または `AzureUSGovernment`) を指定してください |
| AZCM0015 | 指定された関連付け ID は有効な GUID ではありません | `--correlation-id` に有効な GUID を指定してください |
| AZCM0016 | 必須パラメーターがありません | 出力を確認して、欠落しているパラメーターを特定してください |
| AZCM0017 | このリソース名は無効です | 英数字、ハイフン、およびアンダースコアのみが使用されている名前を指定します。 名前の末尾には、ハイフンおよびアンダースコアは使用できません。 |
| AZCM0018 | コマンドは管理者特権なしで実行されました | 管理者特権でのコマンド プロンプトまたはコンソール セッションで、管理者またはルート権限を使用してコマンドを再試行してください。 |
| AZCM0041 | 指定された資格情報が無効です | デバイス ログインの場合は、指定されたユーザー アカウントに、サーバー リソースが作成されるテナントとサブスクリプションへのアクセス権があることを確認します。 サービス プリンシパルのログインの場合は、クライアント ID とシークレットが正しいこと、シークレットの有効期限、およびサービス プリンシパルがサーバー リソースの作成場所となる同じテナントからのものであることを確認します。 |
| AZCM0042 | Arc 対応サーバー リソースの作成に失敗しました | 指定されたユーザーまたはサービス プリンシパルに、指定されたリソース グループ内に Arc 対応サーバー リソースを作成するためのアクセス権があることを確認します。 |
| AZCM0043 | Arc 対応サーバー リソースの削除に失敗しました | 指定されたユーザーまたはサービス プリンシパルに、指定されたリソース グループ内の Arc 対応サーバー リソースを削除するためのアクセス権があることを確認します。 リソースが Azure に存在しなくなった場合は、`--force-local-only` フラグを使用して続行します。 |
| AZCM0044 | 同じ名前のリソースが既に存在します | `--resource-name` パラメーターに別の名前を指定するか、または Azure 内の既存の Arc 対応サーバーを削除し、再試行します。 |
| AZCM0061 | エージェント サービスに接続できません | 管理者特権のユーザー コンテキスト (管理者またはルート) でコマンドを実行していること、および HIMDS サービスがサーバー上で実行されていることを確認します。 |
| AZCM0062 | サーバーの接続中にエラーが発生しました | 詳細については、出力内の他のエラー コードを確認してください。 Azure リソースの作成後にエラーが発生した場合は、再試行する前に、リソース グループから Arc サーバーを削除する必要があります。 |
| AZCM0063 | サーバーの切断中にエラーが発生しました | 詳細については、出力内の他のエラー コードを確認してください。 このエラーが引き続き発生する場合は、Azure 内のリソースを削除してから、サーバーで `azcmagent disconnect --force-local-only` を実行してエージェントを切断できます。 |
| AZCM0064 | エージェント サービスが応答していない | `himds` サービスの状態を調べて、実行中であることを確認してください。 実行中でない場合は、サービスを開始します。 実行中の場合は、しばらく待ってからもう一度やり直してください。 |
| AZCM0065 | エージェントの内部通信エラーが発生しました | Microsoft サポートに対処方法をお問い合わせください |
| AZCM0066 | エージェントの Web サービスが応答していないか、使用できません | Microsoft サポートに対処方法をお問い合わせください |
| AZCM0067 | エージェントは既に Azure に接続されています | [エージェントの切断](manage-agent.md#unregister-machine)に関する記事の手順に従って、もう一度やり直してください。 |
| AZCM0068 | Azure からサーバーを切断しているときに内部エラーが発生しました | Microsoft サポートに対処方法をお問い合わせください |
| AZCM0081 | Azure Active Directory マネージド ID 証明書のダウンロード中にエラーが発生しました | サーバーを Azure に接続しようとしているときにこのメッセージが表示された場合、エージェントは Azure Arc サービスと通信できません。 Azure 内のリソースを削除してから、接続を再試行してください。 |
| AZCM0101 | コマンドが正常に解析されませんでした | `azcmagent <command> --help` を実行して、正しいコマンド構文を確認してください |
| AZCM0102 | コンピューターのホスト名を取得できません | `hostname` を実行してシステムレベルのエラー メッセージがないか確認してから、Microsoft サポートにお問い合わせください。 |
| AZCM0103 | RSA キーの生成中にエラーが発生しました | Microsoft サポートに対処方法をお問い合わせください |
| AZCM0104 | システム情報を読み取れませんでした | `azcmagent` の実行に使用された ID が、システムに対する管理者またはルート権限を持っていることを確認してから、操作をやり直してください。 |

## <a name="agent-verbose-log"></a>エージェントの詳細ログ

この記事の後半で説明するトラブルシューティングの手順に従う前に、必要になる最小限の情報は詳細ログです。 詳細 (-v) 引数が使用されている場合は、**azcmagent** ツール コマンドの出力が含まれます。 ログ ファイルは Windows の場合は `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` に、Linux の場合は `/var/opt/azcmagent/log/azcmagent.log` に書き込まれます。

### <a name="windows"></a>Windows

次は、対話型インストールを実行するとき、Windows 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例です。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

次は、サービス プリンシパルを使用して大規模インストールを実行するとき、Windows 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例です。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

次に、対話型インストールを実行するとき、Linux 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例を示します。

>[!NOTE]
>**Azcmagent** を実行するには、Linux マシンに対する *ルート* アクセス許可が必要です。

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

次に、サービス プリンシパルを使用して大規模インストールを実行するとき、Linux 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例を示します。

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>サービスへのエージェントの接続の問題

次の表は、既知のエラーとそれらを解決する方法に関する提案をいくつかまとめたものです。

|Message |エラー |考えられる原因 |解決策 |
|--------|------|---------------|---------|
|認証トークン デバイス フローを取得できませんでした |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |`login.windows.net` エンドポイントに到達できません | エンドポイントへの接続を確認します。 |
|認証トークン デバイス フローを取得できませんでした |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |プロキシまたはファイアウォールによって `login.windows.net` エンドポイントへのアクセスがブロックされています。 | エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |
|認証トークン デバイス フローを取得できませんでした  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | グループ ポリシー オブジェクト *[コンピューターの構成]\[管理用テンプレート]\[システム]\[ユーザー プロファイル]\[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]* が有効です。 | GPO が有効になっていること、および影響を受けるコンピューターがターゲットになっていることを確認します。 詳細については、脚注 <sup>[1](#footnote1)</sup> を参照してください。 |
|SPN から認証トークンを取得できませんでした |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |プロキシまたはファイアウォールによって `login.windows.net` エンドポイントへのアクセスがブロックされています。 |エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |
|SPN から認証トークンを取得できませんでした |`Invalid client secret is provided` |サービス プリンシパル シークレットが間違っているか、無効です。 |サービス プリンシパル シークレットを確認します。 |
| SPN から認証トークンを取得できませんでした |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |サービス プリンシパルまたはテナント ID が間違っています。 |サービス プリンシパルまたはテナント ID を確認します。|
|ARM リソース応答を取得する |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |資格情報またはアクセス許可が間違っています |本人またはサービス プリンシパルが **Azure Connected Machine のオンボード** ロールに属していることを確認します。 |
|AzcmagentConnect ARM リソースに接続できませんでした |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure リソース プロバイダーが登録されていません。 |[リソース プロバイダー](./agent-overview.md#register-azure-resource-providers)を登録します。 |
|AzcmagentConnect ARM リソースに接続できませんでした |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |プロキシ サーバーまたはファイアウォールによって `management.azure.com` エンドポイントへのアクセスがブロックされています。 |エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |

<a name="footnote1"></a><sup>1</sup> この GPO が有効であり、Connected Machine エージェントがあるコンピューターに適用されている場合は、*himds* サービスに対して指定された組み込みアカウントに関連付けられているユーザー プロファイルが削除されます。 その結果、ローカル証明書ストアに 30 日間キャッシュされている、サービスとの通信に使用される認証証明書も削除されます。 30 日間の制限を超える前に、証明書の更新が試行されます。 この問題を解決するには、[コンピューターの登録を解除する](manage-agent.md#unregister-machine)ための手順に従った後、`azcmagent connect` を実行しているサービスに再登録します。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Microsoft Q&A](/answers/topics/azure-arc.html) を通じて、Azure 専門家からの回答を得ることができます。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。

* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
