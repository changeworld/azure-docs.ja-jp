<properties 
   pageTitle="StorSimple Manager Virtual Array の管理 | Microsoft Azure"
   description="Azure クラシック ポータルで StorSimple Manager サービスを使用して、StorSimple のオンプレミスの仮想アレイを管理する方法を説明します。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="v-sharos" />

# StorSimple Manager サービスを使用した StorSimple Virtual Array の管理 (プレビュー)

![セットアップ プロセス フロー](./media/storsimple-ova-manager-service-administration/manage4.png)

## 概要

この記事では、StorSimple Manager サービス インターフェイスについて、それへの接続方法と使用可能な各種オプションなどを説明し、この UI を使用して実行できる特定のワークフローへのリンクを示します。

この記事を読むと、次の方法がわかります。

- StorSimple Manager サービスへの接続
- StorSimple Manager UI のナビゲーション
- StorSimple Manager サービスを使用した StorSimple Virtual Array の管理

> [AZURE.NOTE] StorSimple 8000 シリーズ デバイスで使用可能な管理オプションを確認するには、「[StorSimple Manager サービスを使用した StorSimple デバイスの管理](storsimple-manager-service-administration.md)」を参照してください。

## StorSimple Manager サービスへの接続

StorSimple Manager サービスは Microsoft Azure で実行され、複数の StorSimple Virtual Array に接続します。これらのデバイスを管理するには、中央の Microsoft Azure クラシック ポータルをブラウザーで実行して使用します。StorSimple Manager サービスに接続するには、次の手順を実行します。

#### サービスに接続するには

1. [https://manage.windowsazure.com/](https://manage.windowsazure.com/) に移動します。

2. Microsoft アカウントの資格情報を使用して、ウィンドウの右上にある Microsoft Azure クラシック ポータルにログオンします。

3. 左側のナビゲーション ウィンドウで下へスクロールして、StorSimple Manager サービスにアクセスします。

    ![サービスへのスクロール](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## StorSimple Manager サービス UI のナビゲーション

StorSimple Manager サービス UI のナビゲーション階層を次の表に示します。

- **StorSimple Manager** のランディング ページからは、サービス内のすべての仮想アレイに適用可能な UI サービス レベルのページに移動できます。

- **[デバイス]** ページからは、特定の仮想アレイに適用可能なデバイス レベルの UI ページに移動できます。

#### StorSimple Manager サービスのナビゲーション階層

|ランディング ページ|サービス レベルのページ|デバイス レベルのページ|
|---|---|---|
|StorSimple Manager サービス|ダッシュ ボード (サービス)|ダッシュ ボード (デバイス)|
|デバイス →|監視|
|バックアップ カタログ|共有 (ファイル サーバー) または</br>ボリューム (iSCSI サーバー)|
|構成 (サービス)|構成 (デバイス)|
|ジョブ|メンテナンス|
|アラート|

## StorSimple Manager サービスを使用した管理タスクの実行

次の表に、StorSimple Manager サービス UI 内で実行できるすべての一般的な管理タスクと複雑なワークフローの概要を示します。これらのタスクは、タスクが開始される UI ページに基づいてまとめられています。

各ワークフローの詳細については、表内の適切な手順をクリックしてください。

#### StorSimple Manager のワークフロー

|目的の操作|移動先の UI ページ|実行する手順|
|---|---|---|
|サービスを作成する</br>サービスを削除する</br>サービス登録キーを取得する</br>サービス登録キーを再生成する|StorSimple Manager サービス|[StorSimple Manager サービスをデプロイする](storsimple-ova-manage-service.md)|
|仮想アレイを非アクティブ化する</br>仮想アレイを削除する|StorSimple Manager サービス → [デバイス]|[仮想アレイを非アクティブ化または削除する](storsimple-ova-deactivate-and-delete-device.md)|
|障害復旧とデバイスのフェールオーバー</br>フェールオーバーの前提条件</br>仮想デバイスにフェールオーバーする</br>ビジネス継続性障害復旧 (BCDR)</br>障害復旧時のエラー|StorSimple Manager サービス → [デバイス]|[StorSimple Virtual Array の障害復旧とデバイスのフェールオーバー](storsimple-ova-failover-dr.md)|
|共有やボリュームをバックアップする</br>手動バックアップを実行する</br>バックアップ スケジュールを変更する</br>既存のバックアップを表示する|StorSimple Manager サービス → [バックアップ カタログ]|[StorSimple Virtual Array をバックアップする](storsimple-ova-backup.md)|
|バックアップ セットから共有を復元する</br>バックアップ セットからボリュームを復元する</br>項目レベルの回復 (ファイル サーバーのみ)|StorSimple Manager サービス → [バックアップ カタログ]|[StorSimple Virtual Array のバックアップから復元する](storsimple-ova-restore.md)|
|ストレージ アカウントについて</br>ストレージ アカウントを追加する</br>ストレージ アカウントを編集する</br>ストレージ アカウントを削除する|StorSimple Manager サービス → [構成]|[StorSimple Virtual Array のストレージ アカウントを管理する](storsimple-ova-manage-storage-accounts.md)|
|アクセス制御レコードについて</br>アクセス制御レコードを追加または変更する</br>アクセス制御レコードを削除する|StorSimple Manager サービス → [構成]|[StorSimple Virtual Array のアクセス制御レコードを管理する](storsimple-ova-manage-acrs.md)|
|アラート設定を構成する</br>アラート通知を受け取る</br>アラートを管理する</br>アラートを確認する|StorSimple Manager サービス → [アラート]|[StorSimple Virtual Array のアラートを表示し、管理する](storsimple-ova-manage-alerts.md)
|デバイス管理者のパスワードを変更する|StorSimple Manager サービス → [デバイス] → [構成]|[StorSimple Virtual Array デバイス管理者パスワードを変更する](storsimple-ova-change-device-admin-password.md)|
|ソフトウェアの更新プログラムをインストールする|StorSimple Manager サービス → [デバイス] → [メンテナンス]|[仮想アレイを更新する](storsimple-ova-update.md)|

>[AZURE.NOTE] 次のタスクには、[ローカル Web UI](storsimple-ova-web-ui-admin.md) を使用する必要があります。
>
>- [サービス データ暗号化キーを取得する](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [サポート パッケージを作成する](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [仮想アレイを停止し、再起動する](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##次のステップ
Web UI とその使用方法については、「[StorSimple Web UI を使用した StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)」をご覧ください。

<!---HONumber=AcomDC_0218_2016-->