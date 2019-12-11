---
title: トラブルシューティング
description: Azure FarmBeats のトラブルシューティングの方法
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 22c304b26eddaee4444f6eb12957e2a6fedf7810
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793287"
---
# <a name="troubleshooting"></a>トラブルシューティング

以下のセクションでは、Azure FarmBeats の一般的な問題とその修正方法について説明します。

詳細については、farmbeatssupport@microsoft.com にお問い合わせください。メールには、deployer.log ファイルを含めてください。

 deployer.log ファイルをダウンロードするには、次の手順を使用します。

1. 強調表示されているアイコンをクリックし、ドロップダウンから **[ダウンロード]** オプションを選択します。

    ![プロジェクト (FarmBeats)](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. 次の画面で、deployer.log ファイルへのパスを入力します。 たとえば、farmbeats-deployer.log のようになります。

## <a name="sensor-telemetry"></a>センサー テレメトリ

### <a name="telemetry-not-seen"></a>テレメトリが見つからない

**現象**:デバイス/センサーをデプロイし、FarmBeats とデバイス パートナーをリンクさせましたが、FarmBeats でテレメトリ データの取得/表示ができません。

**是正措置**:Azure portal にアクセスして、次の手順を実行します。

1. FarmBeats データ ハブ リソース グループにアクセスします。   
2. **イベント ハブ** (DatafeedEventHubNamespace...) を選択し、受信メッセージの数を確認します。   
3. **受信メッセージがない**場合は、デバイス パートナーにお問い合わせください。  
4. **受信メッセージ**がある場合は、farmbeatssupport@microsoft.com に連絡してください。その際に、データ ハブと Accelerator のログ、取得したテレメトリを含めてください。

ログのダウンロード方法については、ドキュメントの[ログに関するセクション](#collect-logs-manually)を参照してください。  

### <a name="dont-have-the-eventhub-connection-string"></a>EventHub 接続文字列がない

**是正措置**:データ ハブ Swagger にアクセスし、次の手順を実行してください。
1. パートナー API に移動します
2. [GET] -> [Try it Out] -> [Execute] の順にクリックします
3. 関心のあるセンサー パートナーのパートナー ID をメモしておきます
4. パートナー API に戻り、[GET/{id}] をクリックします
5. 手順 3 の ID を指定し、[Execute] をクリックします
6. API の応答に、EventHub 接続文字列が含まれているはずです

### <a name="device-appears-offline"></a>デバイスがオフラインとして表示される

**現象**:デバイスをインストールし、FarmBeats とデバイス パートナーをリンクしています。 デバイスはオンラインであり、テレメトリ データを送信していますが、オフラインとして表示されます。

**是正措置**:このデバイスのレポート間隔が構成されていません。 レポート間隔を設定するには、デバイスの製造元に問い合わせてください。 

### <a name="error-deleting-a-resource"></a>リソースの削除エラー

デバイスを削除するときの一般的なエラー シナリオを次に示します。  

**メッセージ**:デバイスはセンサーで参照されています。デバイスに関連付けられているセンサーが 1 つ以上あります。 センサーを削除してから、デバイスを削除してください。  

**意味**:デバイスは、ファームにデプロイされている複数のセンサーに関連付けられています。   

**是正措置**:  

1. Accelerator を使用して、デバイスに関連付けられているセンサーを削除します。  
2. センサーを別のデバイスに関連付ける場合は、デバイス パートナーに依頼してこの操作を行ってもらいます。  
3. force パラメーターを "true" に設定し、DELETE API 呼び出しを使用してデバイスを削除します。  

**メッセージ**:デバイスは、他のデバイスから ParentDeviceId として参照されています。このデバイスには、子デバイスとして関連付けられているデバイスが 1 つ以上あります。 これらを削除してから、このデバイスを削除してください。  

**意味**:デバイスに他のデバイスが関連付けられています。  

**是正措置**:

1. 対象のデバイスに関連付けられているデバイスを削除します。  
2. 対象のデバイスを削除します。  

    > [!NOTE]
    > センサーが関連付けられている場合、デバイスを削除することはできません。 関連するセンサーを削除する方法の詳細については、センサー データの取得に関する章の[センサーの削除](get-sensor-data-from-sensor-partner.md)を参照してください。


## <a name="issues-with-jobs"></a>ジョブの問題

### <a name="farmbeats-internal-error"></a>FarmBeats の内部エラー

**メッセージ**:FarmBeats 内部エラー。詳細については、トラブルシューティング ガイドを参照してください。

**是正措置**:これは、データ パイプラインの一時的なエラーが原因である可能性があります。 もう一度ジョブを作成します。 引き続きエラーが発生する場合は、エラーについて FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com に連絡します。

## <a name="accelerator-troubleshooting"></a>Accelerator のトラブルシューティング

### <a name="access-control"></a>Access Control

**ロールの割り当てを追加する際のエラー**

**メッセージ**: 検索条件に一致するユーザーが見つかりません

**是正措置**:ロールの割り当てを実行しようとしているメール ID を確認します。 メール ID は、Active Directory でそのユーザーに登録されているものと完全に一致している必要があります。 引き続きエラーが発生する場合は、エラーについて FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com に連絡します。

### <a name="unable-to-log-in-to-accelerator"></a>Accelerator にログインできない

**メッセージ**:エラー:サービスを呼び出す権限がありません。 管理者に承認を依頼してください。

**是正措置**:FarmBeats のデプロイへのアクセスを承認するように管理者に依頼してください。 これを行うには、RoleAssignment API の POST を実行するか、Accelerator の [設定] ウィンドウのアクセス制御を使用します。  

既に追加されているにもかかわらずこのエラーが発生する場合は、ページを更新して再試行してください。  引き続きエラーが発生する場合は、エラーについて FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com に連絡します。

![プロジェクト (FarmBeats)](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accelerator - 不明なエラーの発生  

**メッセージ**:エラー:不明なエラーが発生しました

**是正措置**:これは、ページを長時間アイドル状態のままにした場合に発生します。 ページを更新します。  

引き続きエラーが発生する場合は、エラーについて FarmBeats フォーラムに投稿するか、FarmBeatsSupport@microsoft.com に連絡します。

**FarmBeatsDeployment をアップグレードした後でも、FarmBeats Accelerator で最新バージョンが表示されない**

**是正措置**:これは、ブラウザーでサービス ワーカーが永続化されていることによって発生します。
Accelerator が開いているすべてのブラウザーのタブを閉じ、ブラウザー ウィンドウを閉じます。 ブラウザーの新しいインスタンスを起動し、Accelerator URI を再度読み込みます。 これにより、新しいバージョンの Accelerator が読み込まれます。

## <a name="sentinel-imagery-related-issues"></a>Sentinel 画像関連の問題

### <a name="sentinel-wrong-username-or-password"></a>Sentinel のユーザー名またはパスワードが間違っている

**ジョブ エラー メッセージ**: このリソースにアクセスするには、完全な認証が必要です。

**是正措置**: インストーラーを再実行し、正しいユーザー名とパスワードでデータ ハブをアップグレードします。

**是正措置**: 失敗したジョブを再実行するか、5-7 日の範囲のサテライト インデックス ジョブを実行して、ジョブが成功したかどうかを確認します。

### <a name="sentinel-hub-wrong-url-or-not-accessible"></a>Sentinel ハブの URL は間違っているか、アクセスできない 

**ジョブ エラー メッセージ**: 問題が発生しました。 アクセスしようとしていたページは、(一時的に) 利用できません。 

**是正措置**:
1.  Sentinel URL (https://scihub.copernicus.eu/dhus/) ) をブラウザーで開き、Web サイトにアクセスできるかどうか確認します。 
2.  Web サイトにアクセスできない場合は、ファイアウォール/会社のネットワークなどで Web サイトがブロックされていないかを確認し、上記の URL を許可するために必要な手順を実行します。 
3.  失敗したジョブを再実行するか、5-7 日の範囲のサテライト インデックス ジョブを実行して、ジョブが成功したかどうかを確認します。  

### <a name="sentinel-server-down-for-maintenance"></a>メンテナンスのための Sentinel サーバー ダウン

**ジョブ エラー メッセージ**:Copernicus Open Access Hub は間もなく復旧します。 ご不便をおかけして申し訳ありません。現在、メンテナンスを行っています。 すぐにオンラインに戻ります。 

**是正措置**:

1.  この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。 
2.  上記の理由でジョブ/パイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。 
3.  ユーザーは https://scihub.copernicus.eu/news/ にアクセスして、計画済みまたは計画外の Sentinel メンテナンス作業に関する情報を確認できます。  
4.  失敗したジョブを再実行するか、5-7 日の範囲のサテライト インデックス ジョブを実行して、ジョブが成功したかどうかを確認します。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel が接続の最大数に到達した

**ジョブ エラー メッセージ**:ユーザー "<username>" が実行できる 2 つの同時実行フローの最大数 

**是正措置**:
1.  上記の理由でジョブが失敗した場合は、別のデプロイ/ソフトウェアで同じ Sentinel アカウントが使用されています。 
2.  ユーザーは新しい Sentinel アカウントを作成してインストーラーを再実行し、新しい Sentinel ユーザー名とパスワードでデータ ハブをアップグレードすることができます。  
3.  失敗したジョブを再実行するか、5-7 日の範囲のサテライト インデックス ジョブを実行して、ジョブが成功したかどうかを確認します。

### <a name="sentinel-server-refused-connection"></a>Sentinel サーバーが接続を拒否しました 

**ジョブ エラー メッセージ**:

サーバーが次の場所で接続を拒否しました: http://172.30.175.69:8983/solr/dhus 

**是正措置**:この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。 
1.  上記の理由でジョブ/パイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。 
2.  ユーザーは https://scihub.copernicus.eu/news/ にアクセスして、計画済みまたは計画外の Sentinel メンテナンス作業に関する情報を確認できます。  
3.  失敗したジョブを再実行するか、5-7 日の範囲のサテライト インデックス ジョブを実行して、ジョブが成功したかどうかを確認します。


## <a name="collect-logs-manually"></a>ログを手動で収集する

Azure Storage Explorer を[インストールしてデプロイします]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="how-to-collect-data-hub-adf-job-logs"></a>データ ハブの ADF ジョブ ログを収集する方法
1. [https://portal.azure.com](https://portal.azure.com ) にログインします。
2. **[検索]** テキストボックスで、FarmBeats データ ハブ リソース グループを探します。

    > [!NOTE]
    > FarmBeats のデプロイ時に指定したデータ ハブ リソース グループを選択します。

リソース グループ ダッシュボードで、(datahublogs….) ストレージ アカウントを探します。 たとえば、datahublogsmvxmq のようになります。  

1.  **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
2.  (datahubblogs….) ページで **[Explorer で開く]** を選択し、**Azure ストレージ エクスプローラー** アプリケーションを開きます。
3.  左側のパネルで (datahubblogs…)、 **[Blob コンテナー]** 、 **[job-logs] (ジョブログ)** を選択します。
4.  **[job-log] (ジョブログ)** タブで、 **[ダウンロード]** を選択します。
5.  コンピューター上のローカル フォルダーにログをダウンロードする場所を選択します。
6.  ダウンロードした zip ファイルをメールで farmbeatssupport@microsoft.com に送信します。

    ![プロジェクト (FarmBeats)](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Accelerator ADF ジョブ ログを収集する方法

1.  [https://portal.azure.com](https://portal.azure.com ) にログインします。
2.  **[検索]** テキストボックスで、FarmBeats Accelerator リソース グループを探します。

    > [!NOTE]
    > FarmBeats のデプロイ時に指定した Accelerator リソース グループを選択します。

3.  リソース グループのダッシュボードで、storage…. ストレージ アカウントを探します。 たとえば、storagedop4k のようになります。
4.  **[名前]** 列のストレージ アカウントを選択し、ストレージ アカウント ダッシュボードを表示します。
5.  (storage….) ページで **[Explorer で開く]** を選択し、Azure ストレージ エクスプローラー アプリケーションを開きます。
6.  左側のパネルで (storage….)、 **[Blob コンテナー]** 、 **[job-logs] (ジョブログ)** を選択します。
7.  **[job-log] (ジョブログ)** タブで、 **[ダウンロード]** を選択します。
8.  コンピューター上のローカル フォルダーにログをダウンロードする場所を選択します。
9.  ダウンロードした zip ファイルをメールで farmbeatssupport@microsoft.com に送信します。


### <a name="how-to-collect-data-hub-app-service-logs"></a>データ ハブ App Service ログを収集する方法

1.  [https://portal.azure.com](https://portal.azure.com ) にログインします。
2.  **[検索]** テキストボックスで、FarmBeats データ ハブ リソース グループを探します。

    > [!NOTE]
    > FarmBeats のデプロイ時に指定したデータ ハブ リソース グループを選択します。

3.  リソース グループで、(datahublogs….) ストレージ アカウントを探します。 たとえば、datahublogsmvxmq のようになります。
4.  **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5.  (datahubblogs….) ページで **[Explorer で開く]** を選択し、**Azure ストレージ エクスプローラー** アプリケーションを開きます。
6.  左側のパネルで (datahubblogs…)、 **[Blob コンテナー]** 、appinsights-logs を選択します。
7.  appinsights-logs タブで、 **[ダウンロード]** を選択します。
8.  コンピューター上のローカル フォルダーにログをダウンロードするパスを選択します。
9.  ダウンロードしたフォルダーをメールで farmbeatssupport@microsoft.com に送信します。

### <a name="how-to-collect-accelerator-app-service-logs"></a>Accelerator App Service ログを収集する方法

1.  [https://portal.azure.com](https://portal.azure.com ) にログインします。
2.  **[検索]** で、FarmBeats Accelerator リソース グループを探します。

    > [!NOTE]
    > FarmBeats のデプロイ時に提供される Farmbeats Accelerator リソース グループを選択します。

3.  リソース グループで、(storage….) ストレージ アカウントを探します。 たとえば、storagedop4k のようになります。
4.  **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5.  (storage….) ページで **[Explorer で開く]** を選択し、**Azure ストレージ エクスプローラー** アプリケーションを開きます。
6.  左側のパネルで (storage….)、 **[Blob コンテナー]** 、appinsights-logs を選択します。
7.  appinsights-logs タブで、 **[ダウンロード]** を選択します。
8.  コンピューター上のローカル フォルダーにログをダウンロードする場所を選択します。
9.  ダウンロードしたフォルダーをメールで farmbeatssupport@microsoft.com に送信します。

## <a name="known-issues"></a>既知の問題

## <a name="batch-related-issues"></a>バッチ関連の問題

**エラー**: 指定されたサブスクリプションで Batch アカウントのリージョンのアカウント クォータに達しました。

**是正措置**: クォータを増やすか未使用の Batch アカウントを削除し、デプロイを再実行します。

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory 関連の問題

**エラー**: 必要な設定を Azure AD アプリ d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0 に更新できませんでした。この操作を完了するのに十分な特権がありません。 Azure AD アプリに対して上記の設定が適切に構成されていることを確認します。

**意味**:Azure AD アプリの登録構成が正しく行われませんでした。  

**是正措置**: IT 管理者 (テナント読み取りアクセス権を持つ方) に依頼し、[スクリプト](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) を使用して Azure AD アプリ登録を作成してもらいます。 このスクリプトは、構成手順も自動的に処理します。

**エラー**: このテナントに新しい Active Directory アプリケーション "dummyname" を作成できませんでした。プロパティ識別子 URI と同じ値を持つ別のオブジェクトが既に存在します

**意味**:同じ名前の Azure AD アプリ登録が既に存在します。

**是正措置**:既存の Azure AD アプリ登録を削除するか、それを再利用してインストールします。 既存の Azure AD を再利用する場合は、アプリケーション ID とクライアント シークレットをインストーラーに渡して再デプロイします。

## <a name="inputjson-related-issues"></a>Input.json 関連の問題

json ファイルからの入力の読み取り中に**エラー**が発生しました

**是正措置**:この問題は、通常、正しい入力 json パスまたは名前をインストーラーに指定していないことが原因で発生します。 適切な修正を行い、再デプロイを再試行します。

**json 入力の解析エラー**

**是正措置**:この問題は、通常、入力 json ファイル内の値が正しくないことが原因で発生します。 適切な修正を行い、デプロイを再試行します。

## <a name="high-cpu-usage"></a>CPU 使用率が高い

**エラー**: CPU 使用率が高いことを伝えるメール アラートを受け取りました。 

**是正措置**: 
1.  FarmBeats データ ハブ リソース グループにアクセスします。
2.  App Service の名前を選択します。  
3.  [スケールアップ (App Service プラン)] に移動し、適切な[価格レベル](https://azure.microsoft.com/pricing/details/app-service/windows/)を選択します。

## <a name="next-steps"></a>次の手順

問題が解決しない場合は、[サポート フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)にお問い合わせください。
