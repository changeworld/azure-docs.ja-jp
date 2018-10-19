---
title: Azure Logic Apps から SFTP アカウントに接続する | Microsoft Docs
description: Azure Logic Apps を使用して、SFTP サーバーのファイルの監視、作成、管理および受信を行うタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064501"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Azure Logic Apps および SFTP-SSH コネクタを使用して SFTP ファイルの監視、作成、および管理を行う

Azure Logic Apps と SFTP-SSH コネクタを使用して、[SFTP](https://www.ssh.com/ssh/sftp/) サーバーでお使いのアカウントを通してファイルの管理、作成、送信および受信を行う自動化されたタスクとワークフローを作成できます。他にも、次のようなアクションを実行できます。

* ファイルの追加または変更を監視します。
* ファイルの取得、作成、コピー、名前変更、更新、一覧、および削除を行います。
* フォルダーを作成します。
* ファイルの内容とメタデータを取得します。
* アーカイブをフォルダーに抽出します。

SFTP サーバーから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 ロジック アプリでアクションを使用して、SFTP サーバー上でファイルのタスクを実行できます。 他のアクションに SFTP アクションからの出力を使用させることもできます。 たとえば、SFTP サーバーからファイルを定期的に取得する場合、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、それらのファイルとその内容に関する電子メールを送信できます。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH と SFTP の比較

ここでは、SFTP-SSH コネクタと [SFTP](../connectors/connectors-create-api-sftp.md) コネクタのいくつかの主な相違点について説明します。 SFTP-SSH コネクタは、次の機能を備えます。

* .NET の Secure Shell (SSH) オープン ソース ライブラリである <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET** </a>ライブラリを使用します。

* 最大 **1 GB** の大きなファイルをサポートします。 コネクタではサイズが 1 GB までのファイルの読み取りや書き込みができます。

* SFTP サーバーの指定されたパスにフォルダーを作成する**フォルダーの作成**アクションを提供します。

* SFTP サーバーのファイルの名前を変更する**ファイル名の変更**アクションを提供します。

* SFTP サーバーへの接続をキャッシュします。これによりパフォーマンスが向上し、サーバーに対して試行される接続の数が減ります。 

  SFTP サーバーの <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> プロパティを設定することによって、接続のキャッシュに費やされる期間を制御できます。 

## <a name="how-trigger-polling-works"></a>ポーリング動作をトリガーする方法

SFTP-SSH は、SFTP ファイル システムをポーリングし、前回のポーリング以降に変更されたファイルを検索することで動作をトリガーします。 いくつかのツールでは、ファイル変更時に timestamp を保持できます。そのためこの場合は、この機能を無効にしてトリガーが動作するようにする必要があります。 一般的な設定をいくつか次に示します。

| SFTP クライアント | アクションを表示します。 | 
|-------------|--------| 
| Winscp | [オプション] → [環境設定…] → [転送] → [編集...] → [Preserve timestamp]\(タイムスタンプの保持)\ → [無効] |
| FileZilla | [転送] → [転送したファイルのタイムスタンプを維持] → [無効] | 
||| 

トリガーによって新しいファイルが検出されると、トリガーはその新しいファイルが完了していて、すべて書き込まれていることを確認します。 たとえば、トリガーがファイル サーバーを確認している際に、ファイルの進行状態が変化する場合があります。 部分的に書き込まれたファイルが返されることなないように、トリガーは最新の変更を含むファイルのタイムスタンプをメモしますが、すぐにそのファイルを返すことはありません。 トリガーは、もう一度サーバーをポーリングしてファイルを返します。 場合によっては、この動作によって、最大でトリガーのポーリング間隔が 2 倍となる遅延が生じる場合があります。 

ファイルの内容を要求するときに、トリガーは 50 MB より大きいファイルを取得しません。 50 MB より大きいファイルをピックアップするには、次のパターンに従います。

* **ファイルが追加または変更されたとき (プロパティのみ)** といった、ファイルのプロパティを返すトリガーを使用します。 
* **パスを使用してファイルの内容を取得する**といた、完全なファイルを読み取るアクションをとるトリガーに従います。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* SFTP ホスト サーバーのアドレスとアカウント資格情報では、接続を作成してお使いの SFTP アカウントにアクセスしてよいという承認がロジック アプリに与えられます。

  複数行の形式で、SSH 秘密キーの完全な内容をコピーし、**SSH 秘密キー** プロパティに貼り付けます。 
  Notepad.exe を使用して、SSH 秘密キーを提供する方法を説明したサンプルの手順を次に示します。
    
  1. Notepad.exe で SSH 秘密キー ファイルを開く
  2. **[編集]** メニューで **[すべて選択]** を選択します。
  3. **[編集]** > **[コピー]** を選択します。
  4. 接続を作成するとき、**SSH 秘密キー** プロパティで、キーを貼り付けます。 **SSH 秘密キー**プロパティを手動で編集しないでください。

     コネクタは、これら SSH 秘密キー形式と MD5 指紋のみをサポートする SSH.NET ライブラリを使用します。

     * RSA 
     * DSA

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SFTP アカウントにアクセスするロジック アプリ。 SFTP トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 SFTP アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-sftp"></a>SFTP への接続

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合: 検索ボックスに、フィルターとして「SFTP」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

   または

   既存のロジック アプリの場合、アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。 
   検索ボックスに、フィルターとして「sftp」と入力します。 
   アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]**  を選択します。

1. 接続で必要な詳細を指定し、**[作成]** を選択します。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="examples"></a>例

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP トリガー: ファイルが追加または変更されたとき

このトリガーは、SFTP サーバー上でファイルの追加または変更が行われたことを検出したときに、ロジック アプリ ワークフローを開始します。 そのため、たとえばファイルの内容をチェックし、内容が特定の条件を満たしているかどうか基づいてその内容を取得するかどうかを決定する条件を追加できます。 最後に、ファイルの内容を取得するアクションを追加し、その内容を SFTP サーバー上のフォルダーに格納できます。 

**企業での使用例**: このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。 その後、**ファイルの内容を取得する**などの SFTP アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

### <a name="sftp-action-get-content"></a>SFTP アクション: 内容を取得する

このアクションは、SFTP サーバー上のファイルの内容を取得します。 そのため、たとえば前の例のトリガーと、ファイルの内容が満たす必要がある条件を追加できます。 条件が true であれば、内容を取得するアクションを実行できます。 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/sftpconnector/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。