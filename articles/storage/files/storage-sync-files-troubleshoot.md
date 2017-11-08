---
title: "Azure ファイル同期のトラブルシューティング (プレビュー) | Microsoft Docs"
description: "Azure ファイル同期における一般的な問題のトラブルシューティング"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Azure ファイル同期のトラブルシューティング (プレビュー)
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を損なわずに Azure Files で組織のファイル共有を一元化できます。 これは、Windows Server を Azure ファイル共有のクイック キャッシュに変換することで行います。 Windows Server で使用可能な任意のプロトコル (SMB、NFS、FTPS など) を使用してデータにローカル アクセスすることができ、世界中に必要な数だけキャッシュを持つことができます。

この記事は、Azure ファイル同期のデプロイで発生した問題のトラブルシューティングと解決を支援するように設計されています。 解決しない場合、このガイドでは、問題をさらに調査するのに役立つ重要なログをシステムから収集する方法を示します。 質問に対する回答がここで見つからない場合は、次のチャネルを通じて遠慮なくお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Storage フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft サポート: 新しいサポート ケースを作成するには、Azure Portal の [ヘルプとサポート] タブに移動し、[新しいサポート要求] をクリックします。

## <a name="agent-installation-and-server-registration"></a>エージェントのインストールとサーバー登録
<a id="agent-installation-failures"></a>**エージェントのインストール エラーのトラブルシューティングを行う方法**  
Azure ファイル同期エージェントのインストールに失敗する場合は、管理者特権でのコマンド プロンプトから次のコマンドを実行してエージェントのインストール時のログ記録を有効にします。

```
StorageSyncAgent.msi /l*v Installer.log
```

インストールに失敗した後で、installer.log をレビューして原因を特定します。 

> [!Note]  
> Microsoft Update を使用することを選択しており、Windows Update サービスが実行されていない場合、エージェントのインストールは失敗します。

<a id="server-registration-missing"></a>**Azure ポータルの [登録済みサーバー] にサーバーが表示されない**  
サーバーがストレージ同期サービスの [登録済みサーバー] に表示されない場合は、次の手順を実行します。
1. 登録するサーバーにログインします。
2. ファイル エクスプローラーを開き、ストレージ同期エージェントのインストール ディレクトリ (既定の場所は `C:\Program Files\Azure\StorageSyncAgent`) に移動します。 
3. ServerRegistration.exe を実行し、ウィザードに従ってストレージ同期サービスにサーバーを登録します。

<a id="server-already-registered"></a>**Azure File Sync エージェントのインストール後、[サーバーの登録] に、"This server is already registered" \(このサーバーは既に登録されています\) というメッセージが表示される**  
!["server is already registered" \(このサーバーは既に登録されています\) エラー メッセージが表示された [サーバーの登録] ダイアログのスクリーンショット](media/storage-sync-files-troubleshoot/server-registration-1.png)

このメッセージは、以前にサーバーがストレージ同期サービスに登録された場合に表示されます。 現在のストレージ同期サービスでサーバーを登録解除し、新しいストレージ同期サービスに登録するには、「[Unregister a server with Azure File Sync (Azure ファイル同期でのサーバーの登録解除)](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)」の手順に従います。

ストレージ同期サービスの [登録済みサーバー] にサーバーが一覧表示されていない場合、登録解除するサーバー上で次の PowerShell コマンドを実行します。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> サーバーがクラスターの一部である場合は、クラスター登録も削除するオプションの `Reset-StorageSyncServer -CleanClusterRegistration`パラメーターがあります。

<a id="web-site-not-trusted"></a>**サーバーの登録時に多数の "Web サイトが信頼されていない" という応答を受け取るのは、なぜですか。**  
サービスの登録中に **Enhanced Internet Explorer Security** ポリシーが有効になることが原因で、このエラーが発生します。 **Enhanced Internet Explorer Security** ポリシーを適切に無効にする方法の詳細については、「[Azure ファイル同期で使用する Windows Server の準備](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync)」および「[Azure ファイル同期をデプロイする方法 (プレビュー)](storage-sync-files-deployment-guide.md)」をご覧ください。

## <a name="sync-group-management"></a>同期グループ管理
<a id="cloud-endpoint-using-share"></a>**"The specified Azure FileShare is already in use by a different CloudEndpoint"\(特定の Azure FileShare が、別の CloudEndpoint で既に使用されています。\) というエラーで、クラウド エンドポイントの作成に失敗する**  
このエラーは、Azure ファイル共有が別のクラウド エンドポイントによって既に使用されている場合に発生します。 

このエラーを受信し、Azure ファイル共有が現在クラウド エンドポイントで使用されていない場合は、次の手順を実行して、Azure ファイル共有上の Azure ファイル同期のメタデータをクリアします。

> [!Warning]  
> 現在クラウド エンドポイントによって使用されている Azure ファイル共有上のメタデータを削除すると、Azure ファイル同期の操作は失敗します。 

1. Azure Portal で、お使いの Azure ファイル共有に移動します。  
2. Azure ファイル共有を右クリックし、**[メタデータの編集]** を選択します。
3. SyncService を右クリックし、**[削除]** を選択します。

<a id="cloud-endpoint-authfailed"></a>**クラウド エンドポイントの作成が AuthorizationFailed エラーで失敗する**  
この問題は、ユーザー アカウントがクラウド エンドポイントを作成するための十分な権限を持っていない場合に発生します。 

クラウド エンドポイントを作成するには、次の Microsoft 承認アクセス許可を持つユーザー アカウントが必要です。  
* 読み取り: ロール定義の取得
* 書き込み: カスタムのロール定義の作成または更新
* 読み取り: ロール割り当ての取得
* 書き込み: ロール割り当ての作成

次の組み込みロールには、適切な Microsoft 承認アクセス許可が付与されています。  
* 所有者
* ユーザーアクセスの管理者

自分のユーザー アカウントのロールに適切なアクセス許可をあるかどうかは、次の手順で調べられます。  
* Azure ポータルで、 **[リソース グループ]**をクリックします。
* ストレージ アカウントのあるリソース グループを選択し、**[アクセス制御 (IAM)]** をクリックします。
* ユーザー アカウントの **[ロール]** (所有者、共同作成者) をクリックします。
* **[リソース プロバイダー]** 一覧で、**[Microsoft 承認]** を選択します。 
* **[ロールの割り当て]** が **[読み取り]** および **[書き込みのアクセス許可]** になっている必要があります。
* **[ロール定義]** が **[読み取り]** および **[書き込みのアクセス許可]** になっている必要があります。

<a id="cloud-endpoint-deleteinternalerror"></a>**エンドポイントの削除が MgmtInternalError エラーで失敗する**  
この問題は、クラウド エンドポイントを削除する前に、Azure ファイル共有アカウントまたはストレージ アカウントが削除された場合に発生することがあります。 この問題は、今後の更新プログラムで修正され、クラウド エンドポイントを削除することができるようになる予定です。

この問題の発生を防ぐには、Azure のファイル共有またはストレージ アカウントを削除する前に、クラウド エンドポイントを削除します。

## <a name="sync"></a>同期
<a id="afs-change-detection"></a>**SMB またはポータル経由で Azure ファイル共有に直接ファイルを作成しました。同期グループのサーバーにファイルが同期されるまでどのくらいの時間がかかりますか。**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**サーバーで同期が機能しない場合のトラブルシューティングを行う方法**  
サーバーで同期が失敗する場合は、次の手順を実行します。
- Azure Portal で Azure ファイル共有と同期するディレクトリのサーバー エンドポイントが存在することを確認します。
    
    ![Azure Portal にクラウドとサーバー両方のエンドポイントが表示されている同期グループのスクリーンショット](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- `Applications and Services\Microsoft\FileSync\Agent` にあるイベント ビューアーで、操作および診断イベント ログをレビューします。
- サーバーにインターネット接続があることを確認します。
- サービス MMC スナップインを開いて、サーバーで実行されている Azure ファイル同期サービスを確認し、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。

<a id="replica-not-ready"></a>**同期が 0x80c8300f - レプリカでは、必要な操作を実行する準備ができていません - エラーで失敗する**  
クラウド エンドポイントを作成してデータが格納されている Azure ファイル共有を使用すると、このエラーが発生することがあります。 Azure ファイル共有に対する変更の検出が完了 (最大 24 時間がかかる場合があります) すると、同期は正常に動作します。

<a id="broken-sync-files"></a>**同期に失敗する個々のファイルのトラブルシューティングを行う方法**  
個々のファイルが同期に失敗する場合は、次の手順を実行します。
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- ファイルに開いているハンドルがないことを確認します
    - 注: Azure ファイル同期は、開いているハンドルがあるファイルを同期するために VSS スナップショットを定期的に取得します

## <a name="cloud-tiering"></a>クラウドの階層化 
<a id="files-fail-tiering"></a>**階層化に失敗するファイルのトラブルシューティングを行う方法**  
ファイルが Azure Files への階層化に失敗する場合は、次の手順を実行します。

- ファイルが Azure ファイル共有に存在することを確認します
    - 注: 階層化の前に、ファイルが Azure ファイル共有と同期されている必要があります
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- サーバーにインターネット接続があることを確認します 
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて `fltmc` を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

<a id="files-fail-recall"></a>**再呼び出しに失敗するファイルのトラブルシューティングを行う方法**  
ファイルの再呼び出しが失敗する場合は、次の手順を実行します。
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- ファイルが Azure ファイル共有に存在することを確認します
- サーバーにインターネット接続があることを確認します 
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて `fltmc` を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

<a id="files-unexpectedly-recalled"></a>**サーバーで予期せず再呼び出しされるファイルのトラブルシューティングを行う方法**  
ウイルス対策、バックアップ、および多数のファイルを読み取るその他のアプリケーションは、オフライン属性を尊重し、それらのファイルのコンテンツの読み取りをスキップする場合を除き、望ましくない再呼び出しの原因となります。 サポート対象のこれらの製品のオフライン ファイルをスキップすると、ウイルス対策スキャンやバックアップ ジョブなどの操作を実行するときに、望ましくない呼び出しを回避できます。

オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法については、ソフトウェア ベンダーにお問い合わせください。

ファイル エクスプローラーでのファイルの参照のようなその他のシナリオで、望ましくない呼び出しがさらに発生する可能性があります。 サーバーのファイル エクスプローラーでクラウド階層化ファイルを含むフォルダーを開くと、望ましくない再呼び出しが発生する可能性があります。サーバーでウイルス対策が有効になっている場合は、その可能性がさらに高くなります。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング
サーバーで Azure ファイル同期の問題が発生する場合は、次の手順を実行します。
- イベント ビューアーで診断および操作イベント ログをレビューします
    - 同期、階層化、再呼び出しの問題は、`Applications and Services\Microsoft\FileSync\Agent` にある診断および操作イベント ログに記録されます
    - サーバーの管理 (たとえば、構成設定) に関する問題は、`Applications and Services\Microsoft\FileSync\Management` にある診断および操作イベント ログに記録されます
- Azure ファイル同期サービスがサーバーで実行されていることを確認します
    - サービス MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて fltmc を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

上記の手順を実行しても問題が解決しない場合は、次の手順を実行して AFSDiag ツールを実行します。
1. AFSDiag 出力を保存するために使用するディレクトリを作成します (例: c:\output)。
2. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Azure ファイル同期のカーネル モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) 1 を入力し、Enter キーを押します。
4. Azure ファイル同期のユーザー モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) 1 を入力し、Enter キーを押します。
5. 問題を再現させ、終了したら D キーを押します。
6. ログ ファイルとトレース ファイルを含む .zip ファイルが指定した出力ディレクトリに作成されます。

## <a name="see-also"></a>関連項目
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
- [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
