---
title: "Azure ファイル同期のトラブルシューティング (プレビュー) | Microsoft Docs"
description: "Azure File Sync の一般的な問題をトラブルシューティングします。"
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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 5558a69756075dd83f890d5e9e00c9944d841591
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Azure ファイル同期のトラブルシューティング (プレビュー)
Azure File Sync (プレビュー) を使用して、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま、Azure Files で組織のファイル共有を一元化します。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事は、Azure File Sync のデプロイで発生する可能性がある問題のトラブルシューティングと解決を支援することを目的としています。 問題をさらに調査する必要がある場合に、システムから重要なログを収集する方法についても説明します。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Storage フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。 
4. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、**[ヘルプとサポート]** ボタンを選択し、**[新しいサポート要求]** を選択します。

## <a name="storage-sync-service-object-management"></a>Storage 同期サービスオブジェクトの管理
1 つのサブスクリプションから別のサブスクリプションにリソースを移動する場合、ファイル同期 (Storage Sync Service) リソースは移動をブロックされます。 

## <a name="agent-installation-and-server-registration"></a>エージェントのインストールとサーバー登録
<a id="agent-installation-failures"></a>**エージェントのインストール エラーをトラブルシューティングする**  
管理者特権でのコマンド プロンプトで Azure File Sync エージェントのインストールに失敗した場合は、次のコマンドを実行してエージェントのインストール時のログ記録を有効にします。

```
StorageSyncAgent.msi /l*v Installer.log
```

installer.log をレビューして、インストールが失敗した原因を特定します。 

> [!Note]  
> コンピューターが Microsoft Update を使用するように設定されているときに Windows Update サービスが実行されていない場合、エージェントのインストールは失敗します。

<a id="agent-installation-on-DC"></a>**Active Directory ドメイン コントローラーにエージェントをインストールできない** 同期エージェントを Active Directory ドメイン コントローラーにインストールしようとしたが、PDC ロール所有者が Windows Server 2008R2 (またはそれより前のバージョン) に存在する場合は、同期エージェントのインストールが失敗するという問題が発生することがあります。

この問題を解決するには、Windows Server 2012R2 以降を実行している別のドメイン コントローラーに PDC ロールを転送してから、同期エージェントをインストールします。

<a id="agent-installation-websitename-failure"></a>**エージェントのインストールが "Storage Sync Agent Wizard ended prematurely"(Storage Sync Agent ウィザードが中断されました) というエラーで失敗する**  
この問題は、IIS Web サイトの既定の名前が変更された場合に発生する可能性があります。 この問題を回避するには、IIS の既定の Web サイト名を "既定の Web サイト" に変更して、インストールを再試行してください。 この問題は、エージェントの今後の更新プログラムで修正される予定です。 

<a id="server-registration-missing"></a>**Azure Portal の [登録済みサーバー] にサーバーが表示されない**  
サーバーがストレージ同期サービスの **[登録済みサーバー]** に表示されない場合:
1. 登録するサーバーにログインします。
2. ファイル エクスプローラーを開き、ストレージ同期エージェントのインストール ディレクトリ (既定の場所は C:\Program Files\Azure\StorageSyncAgent) に移動します。 
3. ServerRegistration.exe を実行し、ストレージ同期サービスにサーバーを登録するウィザードを完了します。



<a id="server-already-registered"></a>**Azure File Sync エージェントのインストール中に [サーバー登録] ダイアログに「このサーバーは既に登録されています」というメッセージが表示される** 

!["server is already registered" (このサーバーは既に登録されています) エラー メッセージが表示された [サーバーの登録] ダイアログのスクリーンショット](media/storage-sync-files-troubleshoot/server-registration-1.png)

このメッセージは、以前にサーバーがストレージ同期サービスに登録されていた場合に表示されます。 現在のストレージ同期サービスからサーバーを登録解除し、新しいストレージ同期サービスに登録するには、[Azure File Sync でのサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関する記事に記載されている手順を完了します。

ストレージ同期サービスの **[登録済みサーバー]** にサーバーが一覧表示されていない場合は、登録解除するサーバー上で次の PowerShell コマンドを実行します。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> サーバーがクラスターの一部である場合は、省略可能な *Reset-StorageSyncServer -CleanClusterRegistration* パラメーターを使用して、クラスターの登録を削除することもできます。

<a id="web-site-not-trusted"></a>**サーバーの登録時に多数の "Web サイトが信頼されていない" という応答が表示されます。なぜですか?**  
このエラーは、サーバーの登録中に **Enhanced Internet Explorer Security** ポリシーが有効になった場合に発生します。 **Enhanced Internet Explorer Security** ポリシーを適切に無効にする方法の詳細については、「[Azure File Sync で使用する Windows Server を準備する](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)」および「[Azure File Sync (プレビュー) をデプロイする方法](storage-sync-files-deployment-guide.md)」をご覧ください。

## <a name="sync-group-management"></a>同期グループ管理
<a id="cloud-endpoint-using-share"></a>**クラウド エンドポイントの作成が "The specified Azure FileShare is already in use by a different CloudEndpoint"\(指定された Azure ファイル共有は別の CloudEndpoint で既に使用されています\) というエラーで失敗する**  
この問題は、Azure ファイル共有が別のクラウド エンドポイントによって既に使用されている場合に発生します。 

このメッセージが表示されたときに、Azure ファイル共有が現在クラウド エンドポイントで使用されていない場合は、次の手順を完了して、Azure ファイル共有上の Azure File Sync メタデータをクリアします。

> [!Warning]  
> 現在クラウド エンドポイントによって使用されている Azure ファイル共有上のメタデータを削除すると、Azure File Sync の操作は失敗します。 

1. Azure ポータルで、Azure ファイル共有に移動します。  
2. Azure ファイル共有を右クリックし、**[メタデータの編集]** を選択します。
3. **[SyncService]** を右クリックし、**[削除]** を選択します。

<a id="cloud-endpoint-authfailed"></a>**クラウド エンドポイントの作成が "AuthorizationFailed" エラーで失敗する**  
この問題は、ユーザー アカウントがクラウド エンドポイントを作成するための十分な権限を持っていない場合に発生します。 

クラウド エンドポイントを作成するには、次の Microsoft 承認アクセス許可を持つユーザー アカウントが必要です。  
* 読み取り: ロール定義の取得
* 書き込み: カスタムのロール定義の作成または更新
* 読み取り: ロール割り当ての取得
* 書き込み: ロール割り当ての作成

次の組み込みロールには、必要な Microsoft 承認アクセス許可が付与されています。  
* Owner
* ユーザー アクセス管理者: 現在のユーザー アカウントのロールに必要なアクセス許可が付与されているかどうかを確認するには:  
1. Azure ポータルで、**[リソース グループ]** を選択します。
2. ストレージ アカウントのあるリソース グループを選択し、**[アクセス制御 (IAM)]** を選択します。
3. ユーザー アカウントに割り当てる**[ロール]** (所有者や共同作成者など) を選択します。
4. **[リソース プロバイダー]** 一覧で、**[Microsoft 承認]** を選択します。 
    * **[ロールの割り当て]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。
    * **[ロール定義]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。

<a id="server-endpoint-createjobfailed"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134375898) というエラーで失敗する**                                                                                                                    
この問題は、サーバー エンドポイントのパスがシステム ボリューム上にあり、クラウドの階層化が有効な場合に発生します。 システム ボリュームでは、クラウドの階層化はサポートされていません。 システム ボリュームにサーバー エンドポイントを作成するには、サーバー エンドポイントを作成するときにクラウドの階層化を無効にします。

<a id="server-endpoint-deletejobexpired"></a>**サーバー エンドポイントの削除が "MgmtServerJobExpired" エラーで失敗する**                
この問題は、サーバーがオフラインの場合、またはネットワークに接続できない場合に発生します。 サーバーを使用できなくなったら、ポータルでサーバーの登録を解除します。これで、サーバー エンドポイントが削除されます。 サーバー エンドポイントを削除するには、[Azure File Sync 使用したサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関するセクションで説明されている手順を実行します。

<a id="server-endpoint-provisioningfailed"></a>**[サーバー エンドポイントのプロパティ] ページが開かない、またはクラウドの階層化ポリシーを更新できない**

この問題は、サーバー エンドポイントでの管理操作が失敗する場合に発生することがあります。 Azure Portal で [サーバー エンドポイントのプロパティ] ページが開かない場合は、サーバーから PowerShell コマンドでサーバー エンドポイント を更新すると、この問題が解決する場合があります。 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>同期
<a id="afs-change-detection"></a>**SMB またはポータルを使用して Azure ファイル共有内にファイルを直接作成した場合、ファイルが同期グループ内のサーバーと同期されるまでどのくらい時間がかかりますか?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**サーバーで同期が失敗する**  
サーバーで同期が失敗する場合:
1. Azure Portal で、Azure ファイル共有と同期するディレクトリ用のサーバー エンドポイントが存在することを確認します。
    
    ![Azure Portal のクラウド エンドポイントとサーバー エンドポイントの両方が表示されている同期グループのスクリーンショット](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. イベント ビューアーで、Applications と Services\Microsoft\FileSync\Agent に配置されている操作イベント ログと診断イベント ログをレビューします。
    1. サーバーにインターネット接続があることを確認します。
    2. Azure File Sync サービスがサーバーで実行されていることを確認します。 これを行うには、サービスの MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。

<a id="replica-not-ready"></a>**同期が "0x80c8300f - レプリカでは、必要な操作を実行する準備ができていません" エラーで失敗する**  
この問題は、クラウド エンドポイントを作成してデータが格納されている Azure ファイル共有を使用した場合に発生することが予期されます。 Azure ファイル共有での変更検出ジョブの実行が終了すると (最大 24 時間がかかることがあります)、同期は正常に動作を開始します。


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

現在、別のサブスクリプションへのリソースの移動、または異なる Azure AD テナントへの移動は、サポートされていません。  サブスクリプションが別のテナントに移動すると、所有権が変化するためにサービスは Azure ファイル共有にアクセスできなくなります。 テナントが変更された場合は、サーバー エンドポイントとクラウド エンドポイントを削除し (再利用される Azure ファイル共有のクリーニング方法に関する同期グループ管理のセクションを参照)、同期グループを再作成する必要があります。

<a id="doesnt-have-enough-free-space"></a>**エラー "この PC には十分な空き領域がありません"**  
ポータルに、"この PC には十分な空き領域がありません" という状態が表示されている場合、その原因は、ボリュームに残っている空き領域が 1 GB 未満である可能性があります。  たとえば、1.5 GB のボリュームがある場合、同期で利用できるのは 0.5 GB のみです。この問題が発生した場合は、サーバーのエンドポイントに使用できるボリュームのサイズを拡張してください。

## <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化の障害パスは 2 つあります。

- ファイルを階層化できないことがあります。これは、Azure File Sync がファイルを Azure Files に階層化しようとして失敗することを意味します。
- ファイルを再呼び出しできません。これは、ユーザーが階層化されたファイルにアクセスしようとしたときに、Azure File Sync ファイル システム フィルター (StorageSync.sys) がデータのダウンロードに失敗することを意味します。

いずれかの障害パスで発生する可能性がある障害には 2 つの主要なクラスがあります。

- クラウド ストレージの障害
    - "*一時的なストレージ サービスの可用性の問題*"。 詳しくは、[Azure Storage のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) に関するページをご覧ください。
    - "*アクセスできない Azure ファイル共有*"。 この障害は通常、Azure ファイル共有がまだ同期グループ内のクラウド エンドポイントであるときにこれを削除した場合に発生します。
    - "*アクセスできないストレージ アカウント*"。 この障害は通常、同期グループ内のクラウド エンドポイントである Azure ファイル共有がまだあるときにストレージ アカウントを削除した場合に発生します。 
- サーバーの障害 
    - "*Azure File Sync ファイル システム フィルター (StorageSync.sys) が読み込まれていない*"。 階層化/再呼び出し要求に応答するためには、Azure File Sync ファイル システム フィルターが読み込まれている必要があります。 フィルターが読み込まれない理由はいくつかありますが、最も一般的な理由は、管理者が手動でアンロードしたことです。 Azure File Sync が正常に機能するためには、Azure File Sync ファイル システム フィルターが常に読み込まれている必要があります。
    - "*不足、破損、切断している再解析ポイント*"。 再解析ポイントは、ファイルの特別なデータ構造であり、次の 2 つの部分で構成されています。
        1. Azure File Sync ファイル システム フィルター (StorageSync.sys) がファイルへの IO になんらかのアクションを行う必要があることをオペレーティング システムに示す再解析タグ。 
        2. 関連付けられているクラウド エンドポイント (Azure ファイル共有) 上のファイルの URI をファイル システムがフィルターすることを示す再解析データ。 
        
        再解析ポイントが破損する最も一般的な原因は、管理者がタグまたはそのデータを変更しようとしたことです。 
    - "*ネットワーク接続性の問題*"。 ファイルを階層化または再呼び出しするには、サーバーにインターネット接続が必要です。

次の各セクションでは、クラウド階層化の問題のトラブルシューティングを行い、問題がクラウド ストレージの問題かサーバーの問題かを確認する方法を示します。

<a id="files-fail-tiering"></a>**階層化に失敗するファイルをトラブルシューティングする**  
ファイルが Azure ファイルへの階層化に失敗する場合:

1. ファイルが Azure ファイル共有に存在することを確認します。

    > [!NOTE]
    > 階層化の前に、ファイルが Azure ファイル共有と同期されている必要があります。
2. イベント ビューアーで、Applications と Services\Microsoft\FileSync\Agent に配置されている操作イベント ログと診断イベント ログをレビューします。
    1. サーバーにインターネット接続があることを確認します。 
    2. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
        - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

<a id="files-fail-recall"></a>**再呼び出しに失敗するファイルをトラブルシューティングする**  
ファイルが再呼び出しに失敗する場合:
1. イベント ビューアーで、Applications と Services\Microsoft\FileSync\Agent に配置されている操作イベント ログと診断イベント ログをレビューします。
    1. ファイルが Azure ファイル共有に存在することを確認します。
    2. サーバーにインターネット接続があることを確認します。 
    3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
        - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

<a id="files-unexpectedly-recalled"></a>**サーバーで予期せず再呼び出しされるファイルをトラブルシューティングする**  
ウイルス対策、バックアップ、および多数のファイルを読み取るその他のアプリケーションは、オフライン スキップ属性を尊重してそれらのファイルのコンテンツの読み取りをスキップする場合を除いて、意図しない再呼び出しの原因となります。 オフライン ファイルをスキップするオプションをサポートしている製品でスキップを実行すると、ウィルス対策スキャンやバックアップ ジョブなどの操作中の意図しない再呼び出しを回避できます。

オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法については、ソフトウェア ベンダーにお問い合わせください。

意図しない呼び出しは、ファイル エクスプローラーでファイルを参照する場合などの他のシナリオで発生することもあります。 サーバー上でクラウド階層化されたファイルがあるフォルダーをファイル エクスプローラーで開くと、意図しない再呼び出しが発生することがあります。 ウイルス対策ソリューションがサーバーで有効になっている場合よりも、こちらのほうが発生する可能性が高まります。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング
サーバーで Azure File Sync の問題が発生する場合は、次の手順を完了します。
1. イベント ビューアーで、操作イベント ログと診断イベント ログをレビューします。
    - 同期、階層化、および再呼び出しの問題は、Applications と Services\Microsoft\FileSync\Agent の下の診断イベント ログと操作イベント ログに記録されます。
    - サーバーの管理 (構成設定など) に関連する問題は、Applications と Services\Microsoft\FileSync\Management の下の診断イベント ログと操作イベント ログに記録されます。
2. サーバーで Azure File Sync サービスが実行されていることを確認します。
    - サービスの MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。
3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
    - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

問題が解決されない場合は、AFSDiag ツールを実行します。
1. AFSDiag の出力が保存されるディレクトリを作成します (例: C:\Output)。
2. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure File Sync のカーネル モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
4. Azure File Sync のユーザー モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
5. 問題を再現します。 操作が終了したら、**[D]** を入力します。
6. ログファイルとトレース ファイルを含む .zip ファイルが、指定した出力ディレクトリに保存されます。

## <a name="see-also"></a>関連項目
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
- [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
