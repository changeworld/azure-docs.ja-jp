<properties
	pageTitle="IoT Hub デバイス管理の概要 |Microsoft Azure"
	description="C# で Azure IoT Hub をデバイス管理に使用する方法についてわかりやすく説明します。Azure IoT Hub と C#、Microsoft Azure IoT SDK を使用してデバイス管理を実装します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# node.js を使用した Azure IoT Hub デバイス管理の概要 (プレビュー)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## はじめに
Azure IoT Hub デバイス管理を利用するには、Azure IoT Hub を作成し、その IoT Hub にデバイスをプロビジョニングして、複数のシミュレートされたデバイスを起動する必要があります。このチュートリアルでは、これらの手順について説明します。

> [AZURE.NOTE]  既存の IoT Hub があっても、既存の IoT Hub にはまだデバイス管理機能がないため、デバイス管理機能を有効にするために新しい IoT Hub を作成する必要があります。デバイス管理が一般公開されると、既存のすべての IoT Hub がアップグレードされ、デバイス管理機能が利用できるようになります。

## 前提条件

手順を完了するには、次のものがインストールされている必要があります。

- Git
- ノード
- npm
- CMake (バージョン 2.8 以降)。CMake は <https://cmake.org/download/> からインストールします。CMake を現在のユーザーの PATH 変数に追加するチェック ボックスをオンにしてください。
- 有効な Azure サブスクリプション

	アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。

## デバイス管理が有効な IoT Hub の作成

シミュレートされたデバイスの接続先として、デバイス管理が有効な IoT Hub を作成する必要があります。次の手順では、この作業を Azure ポータルで行う方法を示しています。

1.  [Azure ポータル]にサインインします。
2.  ジャンプバーで、**[新規]**、**[モノのインターネット]**、**[Azure IoT Hub]** の順にクリックします。

	![][img-new-hub]

3.  **[IoT Hub]** ブレードで、IoT Hub の構成を選択します。

	![][img-configure-hub]

  -   **[名前]** ボックスに IoT Hub の名前を入力します。その**名前**が有効で利用できる場合、**[名前]** ボックスに緑色のチェック マークが表示されます。
  -   **[価格とスケール レベル]** を選択します。このチュートリアルでは特定のレベルは必要ありません。
  -   **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを選択します。詳細については、[リソース グループを使用した Azure リソースの管理]に関するページを参照してください。
  -   **[Enable Device Management]** (デバイス管理の有効化) チェック ボックスをオンにします。
  -   **[場所]** で、IoT Hub をホストする場所を選択します。IoT Hub デバイス管理を使用できるのは、パブリック プレビューの段階では米国東部、北ヨーロッパ、および東アジアでのみです。将来的には、すべてのリージョンで使用可能になる予定です。

  > [AZURE.NOTE]  **[Enable Device Management]** (デバイス管理の有効化) チェック ボックスをオンにしないと、サンプルは動作しません。

4.  必要な IoT Hub 構成オプションを選択したら、**[作成]** をクリックします。Azure が IoT Hub を作成するまでに数分かかる場合があります。状態を確認するには、**スタート画面**または**通知**パネルで進行状況を監視してください。

	![][img-monitor]

5.  IoT Hub が正常に作成されたら、新しい IoT Hub のブレードを開き、**[ホスト名]** を書き留めて**鍵**のアイコンをクリックします。

	![][img-keys]

6.  **[iothubowner]** ポリシーをクリックし、**[iothubowner]** ブレードで接続文字列をコピーして書き留めます。このチュートリアルの残りの部分を完了するために必要になるので、後でアクセスできる場所にコピーしておきます。

 	> [AZURE.NOTE] 運用環境シナリオでは、**iothubowner** 資格情報の使用を避けるようにしてください。

	![][img-connection]

これで、デバイス管理が有効な IoT Hub が作成されました。このチュートリアルの残りの部分を完了するには、接続文字列が必要です。

## サンプルのビルドと IoT Hub へのデバイスのプロビジョニング

このセクションでは、シミュレートされたデバイスとサンプルをビルドし、IoT Hub のデバイス レジストリに一連の新しいデバイス ID をプロビジョニングするスクリプトを実行します。デバイスは、デバイス レジストリに登録されていないと、IoT Hub に接続できません。

サンプルをビルドし、IoT Hub にデバイスをプロビジョニングするには、次の手順を実行します。

1.  ターミナルを開きます。

2.  Github リポジトリを複製します。**スペースがないディレクトリに複製してください。**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  **azure-iot-sdks** リポジトリを複製したルート フォルダーから **azure-iot-sdks/node/service/samples** ディレクトリに移動して、次のスクリプトを実行します。プレースホルダーの値は、前のセクションの接続文字列に置き換えます。

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

このスクリプトでは、次の処理が実行されます。

1.  **cmake** を実行し、シミュレートされたデバイスを構築するために必要なメイクファイルを作成します。実行可能ファイルは、**azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample** にあります。ソース ファイルは **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample** フォルダーにあることに注意してください。

2.  シミュレートされたデバイスの実行可能ファイル **iotdm\_simple\_sample** をビルドします。

3.  ``` npm install ``` を実行して、必要なパッケージをインストールします。

4.  ```node generate_devices.js``` を実行して、IoT Hub にデバイス ID をプロビジョニングします。デバイスは **sampledevices.json** に記述されています。デバイスがプロビジョニングされると、資格情報は **devicecreds.txt** ファイル (**azure-iot-sdks/node/service/samples** ディレクトリ内) に格納されます。

## シミュレートされたデバイスの起動

デバイスがデバイス レジストリに追加されたので、シミュレートされた管理デバイスを起動できます。Azure IoT Hub でプロビジョニングされたデバイス ID ごとに、シミュレートされたデバイスを 1 つ起動する必要があります。

ターミナルを使用して、**azure-iot-sdks/node/service/samples** ディレクトリで次のスクリプトを実行します。

  ```
  simulate.sh
  ```

このスクリプトは、**devicecreds.txt** ファイルに記述されているデバイスごとに、**iotdm\_simple\_sample** を開始するために実行する必要があるコマンドを出力します。シミュレートされたデバイスごとに、別々のターミナル ウィンドウからコマンドを個別に実行してください。シミュレートされたデバイスは、コマンド ウィンドウを閉じるまで実行され続けます。

**iotdm\_simple\_sample** アプリケーションは、C 用の Azure IoT Hub デバイス管理クライアント ライブラリを使用してビルドされます。これにより、Azure IoT Hub によって管理できる IoT デバイスの作成が可能になります。デバイスの製造元は、このライブラリを使用して、デバイスのプロパティをレポートし、デバイスのジョブに必要な実行アクションを実装することができます。このライブラリは、オープン ソース Azure IoT Hub SDK の一部として提供されるコンポーネントです。

**simulate.sh** を実行すると、出力ウィンドウにデータのストリームが表示されます。この出力には、受信トラフィックと送信トラフィックだけでなく、アプリケーション固有のコールバック関数の **printf** ステートメントも表示されます。そのため、受信トラフィックと送信トラフィックと共に、デコードされたパケットをサンプル アプリケーションがどのように処理しているかも確認できます。デバイスが IoT Hub に接続すると、サービスがデバイス上のリソースの監視を自動的に開始します。その後、IoT Hub DM クライアント ライブラリは、デバイス コールバックを呼び出して、デバイスから最新の値を取得します。

**iotdm\_simple\_sample** サンプル アプリケーションからの出力を次に示します。上部には、成功した **REGISTERED** メッセージが表示され、ID が **Device11-7ce4a850** のデバイスが IoT Hub に接続していることを示します。

> [AZURE.NOTE]  出力から詳細を省くには、製品版構成をビルドして実行します。

![][img-output]

「次のステップ」のチュートリアルを完了するまで、シミュレートされたすべてのデバイスは実行された状態にしておいてください。

## 次のステップ

Azure IoT Hub デバイス管理機能の詳細については、次のチュートリアルに進んでください。

- [デバイス ツインの使用方法][lnk-tutorial-twin]

- [クエリを使用したデバイス ツインの検索方法][lnk-tutorial-queries]

- [デバイス ジョブを使用して、デバイスのファームウェアを更新する方法][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure ポータル]: https://portal.azure.com/
[リソース グループを使用した Azure リソースの管理]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0511_2016-->