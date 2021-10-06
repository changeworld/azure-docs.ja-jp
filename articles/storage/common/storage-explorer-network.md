---
title: Azure Storage Explorer でのネットワーク接続 | Microsoft Docs
description: Azure Storage Explorer でのネットワークへの接続に関するドキュメント
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: 2731f4473b726e7433167fe15c9e1f29c775d9cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627883"
---
# <a name="network-connections-in-storage-explorer"></a>Storage Explorer でのネットワーク接続

Storage Explorer では、ローカル エミュレーターに接続していない場合にネットワークを使用してストレージ リソースや他の Azure および Microsoft サービスに要求を行います。

## <a name="hostnames-accessed-by-storage-explorer"></a>Storage Explorer によってアクセスされるホスト名

Storage Explorer は、使用中にさまざまなエンドポイントに対して要求を行います。 次の一覧は、Storage Explorer で要求する一般的なホスト名の詳細を示しています。

- ARM エンドポイント:
  - `management.azure.com` (グローバル Azure)
  - `management.chinacloudapi.cn` (Azure China)
  - `management.microsoftazure.de` (Azure Germany)
  - `management.usgovcloudapi.net` (Azure US Government)
- ログイン エンドポイント:
  - `login.microsoftonline.com` (グローバル Azure)
  - `login.chinacloudapi.cn` (Azure China)
  - `login.microsoftonline.de` (Azure Germany)
  - `login.microsoftonline.us` (Azure US Government)
- Graph エンドポイント:
  - `graph.windows.net` (グローバル Azure)
  - `graph.chinacloudapi.cn` (Azure China)
  - `graph.cloudapi.de` (Azure Germany)
  - `graph.windows.net` (Azure US Government)
- Azure Storage エンドポイント:
  - `(blob|file|queue|table|dfs).core.windows.net` (グローバル Azure)
  - `(blob|file|queue|table|dfs).core.chinacloudapi.cn` (Azure China)
  - `(blob|file|queue|table|dfs).core.cloudapi.de` (Azure Germany)
  - `(blob|file|queue|table|dfs).core.usgovcloudapi.net` (Azure US Government)
- Storage Explorer の更新: `storageexplorerpublish.blob.core.windows.net`
- Microsoft リンク転送:
  - `aka.ms`
  - `go.microsoft.com`
- リソースが背後にある、カスタム ドメイン、プライベート リンク、または Azure Stack インスタンス固有のエンドポイント
- リモート エミュレーターのホスト名

## <a name="proxy-sources"></a>プロキシ ソース

Storage Explorer には、プロキシへの接続に必要な情報を入手する方法と場所に関して、いくつかのオプションがあります。 使用されているオプションを変更するには、 **[設定]** (左側の垂直ツール バーの歯車アイコン) > **[アプリケーション]**  >  **[プロキシ]** の順に移動します。 設定のプロキシ セクションに移動したら、Storage Explorer がプロキシ設定を入手する方法と場所を選択できます。
- [プロキシを使用しない]
- 環境変数を使用する
- アプリのプロキシ設定を使用する
- システム プロキシを使用する (プレビュー)

### <a name="do-not-use-proxy"></a>[プロキシを使用しない]

このオプションを選択すると、Storage Explorer はプロキシへの接続を試行しません。 [プロキシを使用しない] は既定のオプションです。

### <a name="use-environment-variables"></a>環境変数を使用する

このオプションを選択すると、Storage Explorer は特定の環境変数からプロキシ情報を検索します。 これらの変数を次に示します。
- `HTTP_PROXY`
- `HTTPS_PROXY`

両方の変数が定義されている場合、Storage Explorer は `HTTPS_PROXY` からプロキシ情報を入手します。

これらの環境変数の値は、次の形式の URL である必要があります。

`(http|https)://(username:password@)<hostname>:<port>`

プロトコル (`http|https`) とホスト名のみが必要です。 ユーザー名がある場合は、パスワードを指定する必要はありません。

### <a name="use-app-proxy-settings"></a>アプリのプロキシ設定を使用する

このオプションを選択すると、Storage Explorer はアプリのプロキシ設定を使用します。 設定は次のとおりです。
- プロトコル
- Hostname (ホスト名)
- Port
- 資格情報を使用するまたはしない
- 資格情報

資格情報以外のすべての設定は、次のいずれかで管理できます。
- **[設定]** (左側の垂直ツール バーの歯車アイコン) > **[アプリケーション]**  >  **[プロキシ]**  >  **[資格情報を使用する]** 。
- [プロキシの設定] ダイアログ ( **[編集]**  >  **[プロキシの構成]** )。

資格情報を設定するには、[プロキシの設定] ダイアログ ( **[編集]**  >  **[プロキシの構成]** ) に移動する必要があります。

### <a name="use-system-proxy-preview"></a>システム プロキシを使用する (プレビュー)

このオプションを選択すると、Storage Explorer で OS のプロキシ設定が使用されます。 具体的には、Chromium ネットワーク スタックを使用してネットワーク呼び出しが行われることになります。 Chromium ネットワーク スタックは、Storage Explorer で通常使用されている NodeJS ネットワーク スタックよりもはるかに堅牢です。 次の [Chromium のドキュメント](https://www.chromium.org/developers/design-documents/network-settings)からのスニペットは、何ができるかを示しています。

> Chromium ネットワーク スタックではシステムのネットワーク設定が使用され、ユーザーと管理者はすべてのアプリケーションのネットワーク設定を簡単に制御できます。 ネットワーク設定には、次のようなものがあります。
> - プロキシの設定
> - SSL および TLS の設定
> - 証明書失効の確認の設定
> - 証明書ストアとプライベート キー ストア

プロキシ サーバーで資格情報が必要であり、それらの資格情報が OS 設定で構成されていない場合は、資格情報の使用を有効にして、Storage Explorer 内で資格情報を設定する必要があります。 資格情報の使用は、次のいずれかの方法で切り替えることができます。
- **[設定]** (左側の垂直ツール バーの歯車アイコン) > **[アプリケーション]**  >  **[プロキシ]**  >  **[資格情報を使用する]** 。
- [プロキシの設定] ダイアログ ( **[編集]**  >  **[プロキシの構成]** )。

資格情報を設定するには、[プロキシの設定] ダイアログ ( **[編集]**  >  **[プロキシの構成]** ) に移動する必要があります。

現在、一部の機能ではシステム プロキシがサポートされていないので、このオプションはプレビュー段階です。 機能の完全な一覧については、「[システム プロキシがサポートされている機能](#features-that-support-system-proxy)」を参照してください。 システム プロキシが有効になっている場合、システム プロキシがサポートされていない機能では、プロキシへの接続は試行されません。

サポートされている機能でシステム プロキシを使用している間に問題が発生した場合は、[GitHub で問題をオープン](https://github.com/Microsoft/AzureStorageExplorer/issues/new)してください。

## <a name="proxy-server-authentication"></a>プロキシ サーバー認証

プロキシ設定を **環境変数** または **アプリのプロキシ設定** から入手するように Storage Explorer を構成した場合は、基本認証を使用するプロキシ サーバーのみがサポートされます。

**システム プロキシ** を使用するように Storage Explorer を構成した場合は、以下のいずれかの認証方法を使用するプロキシ サーバーがサポートされます。
- 基本
- ダイジェスト
- NTLM
- ネゴシエート

## <a name="which-proxy-source-should-i-choose"></a>プロキシ ソースの選択方法

[システム プロキシをサポートしている機能](#features-that-support-system-proxy)のみを使用している場合は、まず、[**システム プロキシ**](#use-system-proxy-preview)の使用を試行する必要があります。 サポートされている機能でシステム プロキシを使用している間に問題が発生した場合は、[GitHub で問題をオープン](https://github.com/Microsoft/AzureStorageExplorer/issues/new)してください。

システム プロキシをサポートしていない機能を使用している場合は、おそらく [**アプリ設定**](#use-app-proxy-settings)が次善のオプションです。 プロキシ構成を行うための GUI ベースのエクスペリエンスは、プロキシ情報を誤って入力する可能性を減らすのに役立ちます。 ただし、プロキシ環境変数が既に構成されている場合は、[**環境変数**](#use-environment-variables)を使用する方が良い場合があります。

## <a name="azcopy-proxy-usage"></a>AzCopy プロキシの使用

Storage Explorer では、ほとんどのデータ転送操作に AzCopy を使用します。 AzCopy は、Storage Explorer とは異なるテクノロジのセットを使用して記述されています。そのため、プロキシ機能のセットが若干異なります。

Storage Explorer が **プロキシを使用しない** ように構成されているか、**システム プロキシ** を使用するように構成されている場合、AzCopy は、プロキシへの要求を行うかどうかと実行方法を独自の自動検出プロキシ機能を使用して判断するように指示されます。 しかし、**環境変数** または **アプリのプロキシ設定** からプロキシ設定を取得するように Storage Explorer を構成した場合、Storage Explorer は AzCopy に同じプロキシ設定を使用するように指示します。

どのような場合でも AzCopy でプロキシを使用しない場合は、 **[設定]** (左側の垂直ツール バーの歯車アイコン) > **[転送]**  >  **[AzCopy]**  >  **[Disable AzCopy Proxy Usage]\(AzCopy プロキシの使用を無効にする\)** を切り替えることによって、プロキシの使用を無効にできます。

現在、AzCopy では、**基本認証** を使用するプロキシ サーバーのみがサポートされています。

## <a name="ssl-certificates"></a>SSL 証明書の数

既定では、Storage Explorer では NodeJS ネットワーク スタックが使用されます。 NodeJS には、信頼された SSL 証明書の定義済みの一覧が付属しています。 プロキシ サーバーやウイルス対策ソフトウェアなどの一部のネットワーク テクノロジでは、独自の SSL 証明書がネットワーク トラフィックに挿入されます。 これらの証明書は、多くの場合、NodeJS の証明書の一覧に含まれていません。 NodeJS は、このような証明書が含まれている応答を信頼しません。 NodeJS が応答を信頼しない場合、Storage Explorer はエラーを受信します。

このようなエラーを解決するには、複数のオプションがあります。
- プロキシ ソースとして [**システム プロキシ**](#use-system-proxy-preview)を使用します。
- エラーの原因となっている SSL 証明書のコピーをインポートします。
- SSL 証明書を無効にします。 (**非推奨**)

## <a name="features-that-support-system-proxy"></a>システム プロキシをサポートしている機能

以下は、**システム プロキシ** をサポートしている機能の一覧です。

- 更新プログラムの確認とダウンロード
- サブスクリプションの一覧表示
- ストレージ アカウントの機能
  - リスト
- BLOB の機能
  - Containers
    - 作成
    - リスト
    - 保存されているアクセス ポリシーの管理
    - パブリック アクセス レベルの変更
    - リース
    - Properties
    - 削除
  - BLOB
    - リスト
    - 統計
    - 削除の取り消し
  - ADLS Gen2 BLOB
    - リスト
    - 統計
    - ACL の管理 (既存のエンティティの表示および変更のみ)
    - ACL の伝達
    - 詳細ビュー
    - 名前の変更
    - フォルダーの作成
- キューの機能
  - キュー
    - 作成
    - リスト
    - アクセス ポリシーの管理
    - Properties
    - 削除
    - Clear
  - キューのメッセージ
    - リスト
    - 詳細ビュー
    - 追加
    - Dequeue
- ファイル共有の機能
  - ファイルとフォルダー
    - [新しいフォルダー]
    - Properties
- ディスクの機能
  - リソース グループの一覧表示
  - ディスクの一覧表示
  - ディスクのアップロード
  - ディスクのダウンロード
  - ディスクのコピー
  - スナップショットの作成
  - ディスクの削除

## <a name="next-steps"></a>次のステップ

- [プロキシの問題をトラブルシューティングする](./storage-explorer-troubleshooting.md#proxy-issues)
- [証明書に関する問題をトラブルシューティングする](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
