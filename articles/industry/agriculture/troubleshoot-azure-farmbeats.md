---
title: Azure FarmBeats のトラブルシューティング
description: この記事では、Azure FarmBeats のトラブルシューティングの方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: f017b19ef8bd8e4c44d9e2885da6fbaf172808a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476829"
---
# <a name="troubleshoot"></a>[トラブルシューティング]

この記事では、Azure FarmBeats の一般的な問題の解決策を示します。

さらに支援が必要な場合は、farmbeatssupport@microsoft.com にお問い合わせください。 メールには、必ず **deployer.log** ファイルを含めてください。

**deployer.log** ファイルをダウンロードするには、次の手順を実行します。

1. **Azure portal** にサインインし、自分のサブスクリプションと Azure AD テナントを選択します。
2. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。
3. 使用する Cloud Shell エクスペリエンスとして **[Bash]** を選択します。
4. 強調表示されているアイコンを選択し、ドロップダウン リストから **[ダウンロード]** を選択します。

    ![プロジェクト FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 次のペインで、**deployer.log** ファイルへのパスを入力します。 たとえば、「**farmbeats-deployer.log**」と入力します。

## <a name="sensor-telemetry"></a>センサー テレメトリ

### <a name="cant-view-telemetry-data"></a>テレメトリ データを表示できない

**現象**:デバイスまたはセンサーをデプロイし、FarmBeats とデバイス パートナーをリンクさせましたが、FarmBeats でテレメトリ データの取得や表示ができません。

**是正措置**:

1. FarmBeats Datahub リソース グループにアクセスします。   
2. **イベント ハブ** (DatafeedEventHubNamespace) を選択し、受信メッセージの数を確認します。
3. 以下のいずれかを実行します。   
   - "*受信メッセージがない*" 場合は、デバイス パートナーにお問い合わせください。  
   - "*受信メッセージがある*" 場合は、farmbeatssupport@microsoft.com にお問い合わせください。 Datahub と Accelerator のログ、およびキャプチャされたテレメトリを添付してください。

ログのダウンロード方法の詳細については、「[ログを手動で収集する](#collect-logs-manually)」セクションを参照してください。  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Azure Event Hubs の接続文字列がない

**是正措置**:

1. Datahub Swagger でパートナー API にアクセスします。
2. **[Get]\(取得\)**  >  **[Try it out]\(試してみる\)**  >  **[Execute]\(実行\)** の順に選択します。
3. 関心のあるセンサー パートナーのパートナー ID をメモします。
4. パートナー API に戻り、 **[Get/\<ID>]\(<ID> を取得\)** を選択します。
5. 手順 3. のパートナー ID を指定して、 **[Execute]\(実行\)** を選択します。

   API の応答に、Event Hubs の接続文字列が含まれているはずです。

### <a name="device-appears-offline"></a>デバイスがオフラインとして表示される

**現象**:デバイスをインストールし、FarmBeats とデバイス パートナーをリンクしています。 デバイスはオンラインであり、テレメトリ データを送信していますが、オフラインとして表示されます。

**是正措置**:このデバイスのレポート間隔が構成されていません。 レポート間隔を設定するには、お使いのデバイスの製造元に問い合わせてください。 

### <a name="error-deleting-a-device"></a>デバイスの削除エラー

デバイスの削除中に、次の一般的なエラー シナリオのいずれかが発生することがあります。  

**メッセージ**:"Device is referenced in sensors: (デバイスはセンサーで参照されています。) There are one or more sensors associated with the device. (デバイスに関連付けられているセンサーが 1 つ以上あります。) Delete the sensors and then delete the device. (センサーを削除してから、デバイスを削除してください。)"  

**意味**:デバイスが、ファームにデプロイされている複数のセンサーに関連付けられています。   

**是正措置**:  

1. Accelerator を使用して、デバイスに関連付けられているセンサーを削除します。  
2. センサーを別のデバイスに関連付ける場合は、デバイス パートナーに依頼してこの操作を行ってもらいます。  
3. `DELETE API` 呼び出しを使用してデバイスを削除し、force パラメーターを *true* に設定します。  

**メッセージ**:"Device is referenced in devices as ParentDeviceId: (デバイスは、他のデバイスから ParentDeviceId として参照されています。) このデバイスには、子デバイスとして関連付けられているデバイスが 1 つ以上あります。 Delete them, and then delete this device. (これらを削除してから、このデバイスを削除してください。)"  

**意味**:自分のデバイスに他のデバイスが関連付けられています。  

**是正措置**:

1. 対象のデバイスに関連付けられているデバイスを削除します。  
2. 対象のデバイスを削除します。  

    > [!NOTE]
    > センサーが関連付けられている場合、デバイスを削除することはできません。 関連付けられているセンサーの削除方法の詳細については、「[センサー パートナーからセンサー データを取得する](get-sensor-data-from-sensor-partner.md)」の「センサーの削除」セクションを参照してください。


## <a name="issues-with-jobs"></a>ジョブの問題

### <a name="farmbeats-internal-error"></a>FarmBeats の内部エラー

**メッセージ**:"FarmBeats internal error, see troubleshooting guide for more details. (FarmBeats の内部エラーです。詳細については、トラブルシューティング ガイドを参照してください。)"

**是正措置**:この問題の原因は、データ パイプラインの一時的な障害である可能性があります。 もう一度ジョブを作成してください。 引き続きエラーが発生する場合は、エラー メッセージを FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com にお問い合わせください。

## <a name="accelerator-troubleshooting"></a>Accelerator のトラブルシューティング

### <a name="access-control"></a>アクセス制御

**問題点**: ロールの割り当ての追加中にエラーが発生しました。

**メッセージ**:"検索条件に一致するユーザーが見つかりません。"

**是正措置**:ロールの割り当てを追加しようとしている、対象のメール ID を確認します。 メール ID は、Active Directory でそのユーザーに登録されている ID と完全に一致している必要があります。 引き続きエラーが発生する場合は、エラー メッセージを FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com にお問い合わせください。

### <a name="unable-to-log-in-to-accelerator"></a>Accelerator にログインできない

**メッセージ**:"エラー: サービスを呼び出す権限がありません。 Contact the admin for authorization. (管理者に承認を依頼してください。)"

**是正措置**:FarmBeats のデプロイへのアクセスを承認するように管理者に依頼してください。 これを行うには、RoleAssignment API シリーズの POST を実行するか、Accelerator の **[設定]** ペインのアクセス制御を使用します。  

既にアクセスを付与されているにもかかわらずこのエラーが発生する場合は、ページを最新の情報に更新して再試行してください。 引き続きエラーが発生する場合は、エラー メッセージを FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com にお問い合わせください。

![プロジェクト FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator の問題  

**問題点**: 原因不明の Accelerator エラーが発生しました。

**メッセージ**:"エラー: 不明なエラーが発生しました。"

**是正措置**:このエラーは、ページを長時間アイドル状態のままにした場合に発生します。 ページを更新します。  

引き続きエラーが発生する場合は、エラー メッセージを FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com にお問い合わせください。

**問題点**: FarmBeatsDeployment をアップグレードした後でも、FarmBeats Accelerator で最新バージョンが表示されません。

**是正措置**:このエラーは、ブラウザーでサービス ワーカーが永続化されていることによって発生します。 次の操作を行います。
1. Accelerator が開いているすべてのブラウザーのタブを閉じ、ブラウザー ウィンドウを閉じます。
2. ブラウザーの新しいインスタンスを起動し、Accelerator URI を再度読み込みます。 この操作により、新バージョンの Accelerator が読み込まれます。

## <a name="sentinel-imagery-related-issues"></a>Sentinel: 画像関連の問題

### <a name="wrong-username-or-password"></a>ユーザー名またはパスワードが間違っている

**ジョブ エラー メッセージ**:"Full authentication is required to access this resource. (このリソースにアクセスするには完全な認証が必要です。)"

**是正措置**:

次のいずれかの操作を行います。
- 正しいユーザー名とパスワードで Datahub をアップグレードするために、インストーラーを再実行します。
- 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel ハブ: URL が間違っているか、サイトにアクセスできない 

**ジョブ エラー メッセージ**:"Oops, something went wrong. (問題が発生しました。) The page you were trying to access is (temporarily) unavailable. (アクセスしようとしていたページは、(一時的に) 利用できなくなっています。)" 

**是正措置**:
1. ブラウザーで [Sentinel](https://scihub.copernicus.eu/dhus/) を開き、Web サイトにアクセスできるかどうかを確認します。 
2. Web サイトにアクセスできない場合は、ファイアウォール、社内ネットワーク、またはその他のアクセス制限ソフトウェアにより、Web サイトへのアクセスが妨げられていないかどうかを確認してから、Sentinel URL の許可に必要な手順を実行します。 
3. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel サーバー: メンテナンスのためのダウン

**ジョブ エラー メッセージ**:"The Copernicus Open Access Hub will be back soon! (Copernicus Open Access Hub は間もなく復旧します。) ご不便をおかけして申し訳ありません。しばらくの間、いくつかのメンテナンスを行っています。 We'll be back online shortly! (すぐにオンラインに戻ります。)" 

**是正措置**:

この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。

1. メンテナンスが実施されていることが原因でジョブまたはパイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。 

   Sentinel のメンテナンス活動の詳細については、[Copernicus Open Access Hub のニュース](https://scihub.copernicus.eu/news/)に関するサイトを参照してください。  
2. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: 接続数の上限に到達した

**ジョブ エラー メッセージ**:"Maximum number of two concurrent flows achieved by the user '\<username>'. (ユーザー "<ユーザー名>" の同時実行フロー数が上限の 2 個に達しました。)"

**意味**:接続数の上限に到達したためにジョブが失敗した場合、同じ Sentinel アカウントが別のソフトウェアのデプロイで使用されています。

**是正措置**:次のいずれかを試します。
* 新しい Sentinel アカウントを作成してから、新しい Sentinel のユーザー名とパスワードを使用してインストーラを再び実行し、Datahub をアップグレードします。  
* 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="sentinel-server-refused-connection"></a>Sentinel サーバー: 接続の拒否 

**ジョブ エラー メッセージ**:"Server refused connection at: http://172.30.175.69:8983/solr/dhus. (サーバーが次の場所で接続を拒否しました: http://172.30.175.69:8983/solr/dhus。)" 

**是正措置**:この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。 
1. メンテナンスが実施されていることが原因でジョブまたはパイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。 

   Sentinel のメンテナンス活動の詳細については、[Copernicus Open Access Hub のニュース](https://scihub.copernicus.eu/news/)に関するサイトを参照してください。  
2. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

## <a name="collect-logs-manually"></a>ログを手動で収集する

[Azure Storage Explorer をインストールしてデプロイします]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Datahub 内の Azure Data Factory ジョブのログを収集する
1. [Azure portal](https://portal.azure.com) にサインインする
2. **[検索]** ボックスで、FarmBeats Datahub リソース グループを探します。

    > [!NOTE]
    > FarmBeats のインストール中に指定した Datahub リソース グループを選択します。

3. **[リソース グループ]** ダッシュボードで、*datahublogs\** ストレージ アカウントを探します。 たとえば、**datahublogsmvxmq** を探します。  
4. **[名前]** 列でストレージ アカウントを選択し、 **[ストレージ アカウント]** ダッシュボードを表示します。
5. **[datahubblogs\*]** ペインで **[Explorer で開く]** を選択し、 **[Azure Storage Explorer を開く]** アプリケーションを表示します。
6. 左側のペインで、 **[BLOB コンテナー]** 、 **[job-logs]** の順に選択します。
7. **[job-logs]** ペインで、 **[ダウンロード]** を選択します。
8. 自分のマシン上のローカル フォルダーにログをダウンロードします。
9. ダウンロードした .zip ファイルをメールで farmbeatssupport@microsoft.com に送信します。

    ![プロジェクト FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Accelerator 内の Azure Data Factory ジョブのログを収集する

1. [Azure portal](https://portal.azure.com) にサインインする
2. **[検索]** ボックスで、FarmBeats Accelerator リソース グループを探します。

    > [!NOTE]
    > FarmBeats のインストール中に指定した Accelerator リソース グループを選択します。

3. **[リソース グループ]** ダッシュボードで、*storage\** ストレージ アカウントを探します。 たとえば、**storagedop4k\*** を探します。
4. **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5. **[storage\*]** ペインで **[Explorer で開く]** を選択し、Azure Storage Explorer アプリケーションを開きます。
6. 左側のペインで、 **[BLOB コンテナー]** 、 **[job-logs]** の順に選択します。
7. **[job-logs]** ペインで、 **[ダウンロード]** を選択します。
8. 自分のマシン上のローカル フォルダーにログをダウンロードします。
9. ダウンロードした .zip ファイルをメールで farmbeatssupport@microsoft.com に送信します。


### <a name="collect-datahub-app-service-logs"></a>Datahub の App Service ログを収集する

1. [Azure portal](https://portal.azure.com) にサインインする
2. **[検索]** ボックスで、FarmBeats Datahub リソース グループを探します。

    > [!NOTE]
    > FarmBeats のインストール中に指定した Datahub リソース グループを選択します。

3. リソース グループで、*datahublogs\** ストレージ アカウントを探します。 たとえば、**fordatahublogsmvxmq\*** を探します。
4. **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5. **[datahubblogs\*]** ペインで **[Explorer で開く]** を選択し、Azure Storage Explorer を開きます。
6. 左側のペインで、 **[BLOB コンテナー]** 、 **[appinsights-logs]** を選択します。
7. **[appinsights-logs]** ペインで、 **[ダウンロード]** を選択します。
8. 自分のマシン上のローカル フォルダーにログをダウンロードします。
9. ダウンロードした .zip ファイルをメールで farmbeatssupport@microsoft.com に送信します。

### <a name="collect-accelerator-app-service-logs"></a>Accelerator の App Service ログを収集する

1. [Azure portal](https://portal.azure.com) にサインインする
2. **[検索]** ボックスで、FarmBeats Accelerator リソース グループを探します。

    > [!NOTE]
    > FarmBeats のインストール中に指定した FarmBeats Accelerator リソース グループを選択します。

3. リソース グループで、*storage\** ストレージ アカウントを探します。 たとえば、**storagedop4k\*** を探します。
4. **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5. **[storage\*]** ペインで **[Explorer で開く]** を選択し、Azure Storage Explorer アプリケーションを開きます。
6. 左側のペインで、 **[BLOB コンテナー]** 、 **[appinsights-logs]** を選択します。
7. **[appinsights-logs]** ペインで、 **[ダウンロード]** を選択します。
8. 自分のマシン上のローカル フォルダーにログをダウンロードします。
9. ダウンロードしたフォルダーをメールで farmbeatssupport@microsoft.com に送信します。

## <a name="known-issues"></a>既知の問題

## <a name="batch-related-issues"></a>バッチ関連の問題

**エラー メッセージ**:"The regional account quota of Batch Accounts for the specified subscription has been reached. (指定されたサブスクリプションで Batch アカウントのリージョンのアカウント クォータに達しました。)"

**是正措置**:クォータを増やすか、未使用の Batch アカウントを削除してデプロイを再実行します。

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory (Azure AD) 関連の問題

**エラー メッセージ**:"Could not update required settings to Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: (必要な設定を Azure AD アプリ d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0 に更新できませんでした。) この操作を完了するのに十分な特権がありません。 Ensure that above settings are configured properly for the Azure AD App. (Azure AD アプリに対して上記の設定が適切に構成されていることを確認してください。)"

**意味**:Azure AD アプリの登録構成が正しく行われていません。  

**是正措置**:IT 管理者 (テナント読み取りアクセスを持つユーザー) に依頼し、[スクリプト](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)を使用して Azure AD アプリ登録を作成してもらいます。 このスクリプトは、構成手順も自動的に処理します。

**エラー メッセージ**:"Could not create new Active Directory Application '\<application name\>' in this tenant: ("このテナントに新しい Active Directory アプリケーション "<アプリケーション名>" を作成できませんでした。)Another object with the same value for property identifier URIs already exists. (プロパティ識別子 URI の値が同じである別のオブジェクトが既に存在します。)"

**意味**:同じ名前の Azure AD アプリ登録が既に存在します。

**是正措置**:既存の Azure AD アプリ登録を削除するか、それを再利用してインストールします。 既存の Azure AD アプリ登録を再利用する場合は、アプリケーション ID とクライアント シークレットをインストーラに渡して再デプロイします。

## <a name="issues-with-the-inputjson-file"></a>input.json ファイルの問題

**Error**: *input.json* ファイルからの入力の読み取り中にエラーが発生しました。

**是正措置**:この問題は、通常、正しい *input.json* ファイルのパスまたは名前をインストーラに指定していないことが原因で発生します。 適切な修正を行い、デプロイを再試行します。

**Error**: *input.json* ファイルの値の解析中にエラーが発生しました。

**是正措置**:この問題は、通常、*input.json* ファイル内の値が正しくないことが原因で発生します。 適切な修正を行い、デプロイを再試行します。

## <a name="high-cpu-usage"></a>CPU 使用率が高い

**Error**: "**高 CPU 使用率アラート**" メールを受け取りました。 

**是正措置**: 
1. FarmBeats Datahub リソース グループにアクセスします。
2. **App Service** を選択します。  
3. [App Service の価格のスケールアップ](https://azure.microsoft.com/pricing/details/app-service/windows/)に関するページにアクセスし、適切な価格レベルを選択します。

## <a name="next-steps"></a>次のステップ

FarmBeats の問題が解決しない場合は、[サポート フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)にお問い合わせください。
