<properties 
   pageTitle="StorSimple Virtual Array の Web UI による管理 | Microsoft Azure"
   description="StorSimple Virtual Array の Web UI を使用して、基本的なデバイス管理タスクを実行する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Web UI を使用した StorSimple Virtual Array の管理 (プレビュー)

![セットアップ プロセス フロー](./media/storsimple-ova-web-ui-admin/manage4.png)

## 概要

この記事のチュートリアルは、v 1.1.1.0 (プレビュー) を実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスとも呼ばれます) に適用されます。この記事では、StorSimple Virtual Array で実行できる一部の複雑なワークフローと管理タスクについて説明します。StorSimple Virtual Array の管理には、StorSimple Manager サービス UI (ポータル UI とも呼ばれます) とデバイスのローカル Web UI の 2 種類の UI を使用できます。この記事では、Web UI を使用して実行できるタスクについて説明します。

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array はプレビュー段階であり、評価とデプロイ計画策定を目的としています。このプレビューの運用環境へのインストールはサポートされていません。 
>- StorSimple Virtual Array で問題が発生した場合は、[StorSimple MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple)に問題を投稿してください。

この記事には次のチュートリアルが含まれています。

- サービス データ暗号化キーの取得
- Web UI のセットアップ エラーのトラブルシューティング
- ログ パッケージの生成
- デバイスのシャットダウンと再起動

## サービス データ暗号化キーの取得

StorSimple Manager サービスに最初のデバイスを登録すると、サービス データ暗号化キーが生成されます。このキーは、StorSimple Manager サービスに追加のデバイスを登録する際にサービス登録キーと共に必要になります。

サービス データ暗号化キーを紛失し、取得する必要がある場合は、サービスに登録されているデバイスのローカル Web UI で次の手順を実行します。

#### サービス データ暗号化キーを取得するには

1. ローカル Web UI に接続します。**[Configuration]** > **[Cloud Settings]** に移動します。

    ![サービス データ暗号化キーの取得 1](./media/storsimple-ova-web-ui-admin/image27.png)

2. ページの下部にある **[Get service data encryption key]** をクリックします。キーが表示されます。このキーをコピーして保存します。

    ![サービス データ暗号化キーの取得 2](./media/storsimple-ova-web-ui-admin/image28.png)


## Web UI のセットアップ エラーのトラブルシューティング

ローカル Web UI を使用してデバイスを構成する際に、エラーが発生する場合があります。このようなエラーの診断とトラブルシューティングを行うには、診断テストを実行します。

#### 診断テストを実行するには

1. ローカル Web UI で、**[Troubleshooting]** > **[Diagnostic tests]** に移動します。

    ![診断の実行 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. ページの下部にある **[Run Diagnostic Tests]** をクリックします。これにより、ネットワーク、デバイス、Web プロキシ、時刻、またはクラウドの設定で発生する可能性のある問題を診断するためのテストが開始されます。デバイスでテストを実行中であることが通知されます。

3. テストが完了すると、結果が表示されます。次の例は、診断テストの結果を示しています。このデバイスでは Web プロキシ設定が構成されていないため、Web プロキシのテストは実行されていません。ネットワーク設定、DNS サーバー、および時刻設定を調べる他のテストはすべて正常に完了しています。

    ![診断の実行 2](./media/storsimple-ova-web-ui-admin/image30.png)

## ログ パッケージの生成

ログ パッケージは、Microsoft サポートがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログで構成されます。このリリースでは、ログ パッケージはローカル Web UI を使用して生成できます。

#### ログ パッケージを生成するには

1. ローカル Web UI で、**[Troubleshooting]** > **[System logs]** に移動します。

    ![ログ パッケージの生成 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. ページの下部にある **[Create log package]** をクリックします。システム ログのパッケージが作成されます。作成には数分かかります。

    ![ログ パッケージの生成 2](./media/storsimple-ova-web-ui-admin/image32.png)

    パッケージが正常に作成されると通知されます。ページが更新され、パッケージの作成日時が示されます。

    ![ログ パッケージの生成 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. **[Download log package]** をクリックします。圧縮されたパッケージがシステムにダウンロードされます。

    ![ログ パッケージの生成 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. ダウンロードしたログ パッケージを解凍すると、システム ログ ファイルを表示できます。

## デバイスのシャットダウンと再起動

ローカル Web UI を使用して、仮想デバイスをシャットダウンおよび再起動できます。

#### 仮想デバイスをシャットダウンするには

1. ローカル Web UI で、**[Maintenance]** > **[Power settings]** に移動します。

2. ページの下部にある **[Shutdown]** をクリックします。

    ![デバイスのシャットダウン 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. デバイスをシャットダウンすると、実行中のすべての IO が中断され、ダウンタイムが発生することを示す警告が表示されます。チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-web-ui-admin/image3.png) をクリックします。

    ![デバイスのシャットダウンの警告](./media/storsimple-ova-web-ui-admin/image37.png)

    シャットダウンが開始されたことが通知されます。

    ![デバイスのシャットダウンの開始](./media/storsimple-ova-web-ui-admin/image38.png)

    デバイスがシャットダウンされます。デバイスを起動する場合は、Hyper-V マネージャーを使用してこれを実行する必要があります。

#### 仮想デバイスを再起動するには

1. ローカル Web UI で、**[Maintenance]** > **[Power settings]** に移動します。

2. ページの下部にある **[Restart]** をクリックします。

    ![デバイスの再起動](./media/storsimple-ova-web-ui-admin/image36.png)

3. デバイスを再起動すると、実行中のすべての IO が中断され、ダウンタイムが発生することを示す警告が表示されます。チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-web-ui-admin/image3.png) をクリックします。

    ![再起動の警告](./media/storsimple-ova-web-ui-admin/image37.png)

    再起動が開始されたことが通知されます。

    ![再起動の開始](./media/storsimple-ova-web-ui-admin/image39.png)

    再起動の実行中は UI に接続できなくなります。UI を定期的に更新することで、再起動を監視できます。また、Hyper-V マネージャーを使用して、デバイスの再起動の状態を監視することもできます。

![video\_icon](./media/storsimple-ova-web-ui-admin/video_icon.png) **ビデオ**

StorSimple Virtual Array 上で共有を作成し、共有をバックアップして、データを復元する方法については、このビデオをご覧ください。

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## 次のステップ

[StorSimple Manager サービスを使用してデバイスを管理する](storsimple-manager-service-administration.md)方法を確認します。

<!---HONumber=AcomDC_0121_2016-->