<properties
 pageTitle="IoT Hub デバイス管理 UI の使用 | Microsoft Azure"
 description="Azure IoT Hub デバイス管理 UI の使用に関するチュートリアル"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/08/2016"
 ms.author="dobett"/>

# サンプル UI を使用した Azure IoT Hub デバイス管理の探究

デバイス管理サンプル UI を操作して、Azure IoT Hub デバイス管理の[概要][lnk-dm-overview]や[基本作業][lnk-get-started]の記事で取り上げた概念や機能についての理解を揺るぎないものにしましょう。この記事では、デバイス管理 Web UI サンプルを使って、デバイスの管理に伴う主要な 3 つの概念 (*デバイス ツイン*、*デバイス クエリ*、*デバイス ジョブ*) を 1 つずつ実際に検証していきます。

インタラクティブなデバイス管理機能を独自に構築したいと考えている開発者の方は、サンプル UI のコードベースをフォークし、カスタム プロジェクトの出発点としてご利用ください。その他、開発者向けの機能について詳しく記述した Readme ドキュメントと完全なプロジェクト コードは、[Azure IoT デバイス管理 UI][lnk-dm-github] の GitHub リポジトリでご覧いただけます。

## 前提条件

このチュートリアルに取り組む前に、[Azure IoT Hub デバイス管理の概要][lnk-get-started]の記事に記載されている手順を実行する必要があります。まだ実行していない場合は、同記事に戻ってすべての手順を実行してから先に進んでください。

"基本作業" のチュートリアルの手順を終えた時点で、テスト システムには、次のデバイスとアプリが動作しています。

- 6 つのシミュレーション対象デバイス (**iotdm\_simple\_sample**) がコンソール/ターミナル ウィンドウで実行され、それぞれ正常に登録されたことを示す "REGISTERED" メッセージが表示されます。

- デバイス管理サンプル UI アプリケーション。ビルド後、<http://127.0.0.1:3003> でローカルに実行されます。

## 既定のデバイス ビュー

デバイス管理サンプル UI の既定のホーム画面は **[Devices (デバイス)]** ビューで、次の 5 つのコンポーネントから成ります。

![][1]

1.  *ナビゲーション ボタン*: **[Devices (デバイス)]** ビュー (選択状態)、**[Job History (ジョブの履歴)]** ビュー、**[Add a Device (デバイスの追加)]** ビュー。

2. *デバイスの検索*: デバイス ID で単一のデバイスを検索して編集します。

3.  *デバイス アクション*: **[Edit (編集)]** アクション、**[Delete (削除)]** アクション、**[Export (エクスポート)]** アクション。

4.  *デバイス ジョブ*: デバイスの**再起動**、**ファームウェア更新**、**出荷時の設定へのリセット**。

5.  *デバイス グリッド フィルター*: デバイス グリッドのカスタム ビューを作成して保存するためのフィルター エディター。

6.  *デバイス グリッド*: IoT Hub インスタンスに登録されたすべてのデバイスを既定のプロパティ (**デバイス ID**、**ステータス**、**タグ**) と共に表示します。

[デバイス管理の概要][lnk-dm-overview]では、Azure IoT Hub 内の物理 (シミュレーション対象) デバイスを表す*デバイス ツイン*という概念を紹介しました。デバイス グリッドのデバイス一覧から、登録済みの任意のデバイスを選択すると、対応するデバイス ツインを表示して編集することができます。

最初にあるシミュレーション対象デバイス **Device11-7ce4a850** について、対応する行を選択し、**[Edit (編集)]** ボタンをクリックして以下の詳細表示に切り替えてください (対応するデバイス行をダブルクリックするか、検索ボックスにデバイス ID を入力して切り替えてもかまいません)。

デバイス ツイン コンポーネントの詳細な情報が表示され、書き込み可能なプロパティを更新するなど、各種のデバイス操作 (後述) を実行したりすることができます。

![][2]

1.  **[Edit a Device Twin (デバイス ツインの編集)]**: デバイスの**有効/無効**を切り替えるためのボタンがあります。

2.  **[Service Properties (サービスのプロパティ)]**: デバイスの**タグ**が表示されます。

3.  **[Device Properties (デバイスのプロパティ)]**: ここをクリックすると、このセクションが展開されます。

4.  **[Refresh (最新の情報に更新)]** ボタン: デバイス ツインの最新のプロパティと値を取得します。

先に進むには、このビューの右下隅にある **[Cancel (キャンセル)]** をクリックして既定のデバイス一覧ページに戻ってください。

## デバイス クエリによるデバイス ビューのフィルタリング

特定のプロパティ (特定のタグなど) を備えているデバイスまたはデバイスのグループをすばやく特定するには、デバイス クエリを使用します。サンプル UI の既定のデバイス一覧ページでは、デバイス クエリを使用してデバイス一覧の内容を表示しています。サンプル UI では、サービスのプロパティをテーブルに追加したり、テーブルから削除したりすることができます。そのいくつかのプロパティについては、フィルタリングに使用することができます。

"bacon" というサービス プロパティ タグを持ったデバイスを抽出するカスタム フィルターを作成するには、次の手順を実行します。

1.  じょうごのアイコンをクリックして、デバイス クエリの編集ビューを開きます。

    ![][3]

2.  **[+ Add a filter (+ フィルターの追加)]** をクリックしてエディターを展開します。プロパティ ドロップダウンから **[Tags (タグ)]** を選択し、テキスト フィールドに「**bacon**」と入力して **[Apply (適用)]** をクリックします。これで、"bacon" というタグを含んだ 3 つのデバイスだけが一覧に表示されます。

    ![][4]

3.  クエリ タイトル フィールド (じょうごアイコンの横) で、クエリに「**Only Bacon**」という名前を付けて **[Save (保存)]** をクリックします。

    ![][5]

4.  じょうごアイコンをクリックして、デバイス クエリ エディターを終了します。

## デバイス ジョブを使ったデバイス再起動のシミュレーション 

デバイス管理の概要で説明したように、デバイス ジョブを使用すると、1 つまたは複数の物理デバイスで単純な操作や複雑な操作を連係させることができます。このセクションでは、"bacon" タグを含んだすべてのシミュレーション デバイスに対して再起動操作を実行するデバイス ジョブをサンプル UI で作成します。

1.  **[Only Bacon]** デバイス クエリ リストで各デバイス行をクリックし、再起動ジョブ操作の対象に指定します。

    ![][6]

2.  デバイス ジョブ ツール バーの **[Reboot (再起動)]** ボタンをクリックして、再起動ジョブを作成します。**[はい]** で確定した後、**[Job for Device has started (デバイスに対するジョブが開始されました)]** のダイアログで **[Job History (ジョブの履歴)]** ハイパーリンクをクリックして [Device Jobs (デバイス ジョブ)] ビューに移動します。

    ![][7]

ここで作成したのは単一の親ジョブです。"bacon" タグを含んだ 3 つのデバイスに対してそれぞれ再起動操作を実行する 3 つの子ジョブが、この親ジョブから生成されます。

![][8]

しばらくしてからこの画面を更新すると、親ジョブと 3 つの子ジョブのステータスが **completed (完了)** に変化し、再起動操作が正常に実行されたこと、またシミュレーション対象デバイスによってそれが確認されたことがわかります。どのジョブがどのデバイスに関連付けられているかは、**[Device ID (デバイス ID)]** 列を使用して確認します。


> [AZURE.NOTE] 子ジョブから "failed (失敗)" ステータスが返された場合は、シミュレーション対象デバイスがテスト システムで実行されていることを確認してください。実行されていない場合は、simulate.bat または simulate.sh スクリプトをもう一度実行して、前述したデバイス ジョブの再起動手順を最初からやり直します。

## 次のステップ

ここでは、デバイス管理の概念をサンプル UI を使って検証してきました。デバイス管理 API に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

- [デバイス ツインの使用方法][lnk-tutorial-twin]
- [クエリを使用したデバイス ツインの検索方法][lnk-tutorial-queries]
- [デバイス ジョブを使用して、デバイスのファームウェアを更新する方法][lnk-tutorial-jobs]
- [IoT ゲートウェイの内側で管理されたデバイスを有効にする][lnk-dm-gateway]
- [Azure IoT Hub デバイス管理クライアント ライブラリの概要][lnk-library-c]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [開発者ガイド][lnk-devguide]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]
- [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->