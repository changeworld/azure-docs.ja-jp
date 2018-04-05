---
title: Azure Cosmos DB を Azure Storage Explorer で管理する
description: Azure Cosmos DB を Azure Storage Explorer で管理する方法を説明します。
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Storage Explorer の Azure Cosmos DB に関するトラブルシューティング ガイドの概要

[Azure Storage Explorer の Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) は、Windows、macOS、または Linux から、Azure およびソブリン クラウドでホストされている Azure Cosmos DB アカウントに接続できるスタンドアロン アプリです。 これを使用すると、ストレージ BLOB やキューなど、他の Azure エンティティと一緒に Azure Cosmos DB エンティティの管理、データの操作、ストアド プロシージャとトリガーの更新を実行できます。

このガイドでは、Storage Explorer の Azure Cosmos DB でよく見られる問題の解決策をまとめています。

- [サインインの問題](#Sign-in-issues)
  - [証明書チェーンの自己署名証明書](#Self-signed-certificate-in-certificate-chain)
  - [サブスクリプションを取得できない](#Unable-to-retrieve-subscriptions)
  - [認証ページが表示されない](#Unable-to-see-the-authentication-page)
  - [アカウントを削除できない](#Cannot-remove-account)
- [HTTP/HTTPS プロキシの問題](#Http/Https-proxy-issue)
- ["ローカルで接続済み" ノードの下の "開発" ノードに関する問題](#Development-node-under-Local-and-Attached-node-issue)
- ["ローカルで接続済み" ノードの Azure Cosmos DB アカウントのアタッチに関するエラー](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Azure Cosmos DB ノードの展開に関するエラー](#Expand-Azure-Cosmos-DB-node-error)
- [次のステップ](#Next-steps)

<h2 id="Sign-in-issues">サインインの問題</h2>

先に進む前に、アプリケーションを再起動して問題が解決されるかどうかを確認してください。

<h2 id="Self-signed-certificate-in-certificate-chain">証明書チェーンの自己署名証明書</h2>

このエラーが表示されるにはいくつかの理由があります。最も一般的な理由は次の 2 つです。

1. "透過型プロキシ" の内側にいる。この場合、(IT 部門などの) 他のユーザーが、HTTPS トラフィックを遮断してその暗号化を解除し、自己署名証明書を使ってそれを暗号化しています。

2. 受信した HTTPS メッセージに自己署名 SSL 証明書を挿入するソフトウェアが実行されている (ウイルス対策ソフトウェアなど)。

これらのいずれかの "自己署名証明書" を検出した場合、Storage Explorer は受信した HTTPS メッセージが改ざんされているかどうかを識別できなくなります。 ただし、自己署名証明書のコピーがある場合は、Storage Explorer にそのコピーを信頼させることができます。 証明書の挿入元がわからない場合、次の手順を実行して自分で確認できます。

1. Open SSL をインストールします。
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (簡易バージョンでかまいません)
     - Mac と Linux: オペレーティング システムに付属
2. Open SSL を実行します。
    - Windows: インストール ディレクトリ、**/bin/** の順に移動して、**openssl.exe** をダブルクリックします。
    - Mac と Linux: ターミナルで **openssl** を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します
4. 自己署名証明書を検索します。 どれが自己署名かわからない場合は、証明書の発行先 ("s:") と証明書の発行者 ("i:") が同じものを検索します。
5.  自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6.  Storage Explorer を開いて、**[編集]** > **[SSL 証明書]** > **[証明書のインポート]** の順に移動します。 ファイル ピッカーを使用して、作成された .cer ファイルを検索し、選択して開きます。

上記の手順で自己署名証明書が見つからない場合、フィードバックを送信してお問い合わせください。

<h2 id="Unable-to-retrieve-subscriptions">サブスクリプションを取得できない</h2>

正常にサインインした後にサブスクリプションを取得できない場合:

- [Azure Portal](http://portal.azure.com/) にサインインして、お使いのアカウントにサブスクリプションへのアクセス権があることを確認します
- 適切な環境 ([Azure](http://portal.azure.com/)、[Azure China](https://portal.azure.cn/)、[Azure Germany](https://portal.microsoftazure.de/)、[Azure US Government](http://portal.azure.us/)、またはカスタム環境/Azure Stack) を使用してサインインしていることを確認します
- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します
- アカウントを削除してから再度追加します
- ホーム ディレクトリ (C:\Users\ContosoUser など) から次のファイルを削除して、アカウントを再度追加します。
  - .adalcache
  - .devaccounts
  - .extaccounts
- サインイン時にエラー メッセージが表示される場合は、開発者ツール コンソール (F12 キー) を開きます

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">認証ページが表示されない</h2>  

認証ページが表示されない場合:

- 接続速度によっては、サインイン ページの読み込みに時間がかかる場合があります。認証ダイアログを閉じる前に少なくとも 1 分はお待ちください
- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します
- F12 キーを押して開発者コンソールを表示します。 開発者コンソールからの応答を確認して、認証が機能しない原因の手がかりを見つけられるかどうかを調べます

<h2 id="Cannot-remove-account">アカウントを削除できない</h2>

アカウントを削除できない場合、または再認証のリンクが役に立たない場合

- ホーム ディレクトリから次のファイルを削除して、アカウントを再度追加します。
  - .adalcache
  - .devaccounts
  - .extaccounts
- SAS にアタッチされたストレージ リソースを削除する場合、次を削除します。
  - %AppData%/StorageExplorer フォルダー (Windows の場合)
  - /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer (Mac の場合)
  - ~/.config/StorageExplorer (Linux の場合)
  - これらのファイルを削除すると、**すべての資格情報を再入力する必要があります**


<h2 id="Http/Https-proxy-issue">HTTP/HTTPS プロキシの問題</h2>

ASE で HTTP/HTTPS プロキシを構成する際に、左側のツリーで Azure Cosmos DB ノードを一覧表示できません。 これは既知の問題であり、次のリリースで修正される予定です。 現時点では、Azure Portal の Azure Cosmos DB データ エクスプローラーを回避策として使用できます。 

<h2 id="Development-node-under-Local-and-Attached-node-issue">"ローカルで接続済み" ノードの下の "開発" ノードに関する問題</h2>

左側のツリーにある "ローカルで接続済み" ノードの下の "開発" ノードをクリックした後、応答がありません。  これは期待される動作です。 Azure Cosmos DB ローカル エミュレーターは、次のリリースでサポートされる予定です。

![開発ノード](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">"ローカルで接続済み" ノードの Azure Cosmos DB アカウントのアタッチに関するエラー</h2>

"ローカルで接続済み" ノードの Azure Cosmos DB アカウントをアタッチした後に以下のエラーが表示される場合、正しい接続文字列を使用していることをチェックします。

![ローカルで接続済みの Azure Cosmos DB のアタッチに関するエラー](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Azure Cosmos DB ノードの展開に関するエラー</h2>

左側のツリー ノードを展開しようとしたときに、以下のエラーが表示される場合があります。 

![展開のエラー](./media/troubleshoot-cosmosdb/expand-error.png)

次の推奨事項を試してください。

- Azure Cosmos DB アカウントのプロビジョニングが進行中であるかどうかをチェックし、アカウントが正常に作成されている場合は再試行します。
- アカウントが "クイック アクセス" ノードまたは "ローカルで接続済み" ノードにある場合、アカウントが削除されていることをチェックします。 その場合、ノードを手動で削除する必要があります。

<h2 id="Next-steps">次の手順</h2>

どの解決策も機能しない場合、問題を修正するために、問題の詳細を記載したメールを Azure Cosmos DB 開発ツール チーム ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) にお送りください。





