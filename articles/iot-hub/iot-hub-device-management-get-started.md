<properties
	pageTitle="IoT Hub デバイス管理の概要 |Microsoft Azure"
	description="C# で Azure IoT Hub をデバイス管理に使用する方法についてわかりやすく説明します。Azure IoT Hub と C#、Microsoft Azure IoT SDK を使用してデバイス管理を実装します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# C# を使用した Azure IoT Hub デバイス管理の概要 (プレビュー)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## はじめに
Azure IoT Hub デバイス管理を利用するには、Azure IoT Hub を作成してその IoT Hub にデバイスをプロビジョニングし、複数のシミュレートされたデバイスを起動して、それらのデバイスをデバイス管理のサンプル UI で確認する必要があります。このチュートリアルでは、これらの手順について説明します。

> [AZURE.NOTE]  既存の IoT Hub にはまだデバイス管理機能がないため、既存の IoT Hub をお持ちの場合でもデバイス管理機能を有効にするために新しい IoT Hub を作成する必要があります。デバイス管理が一般公開されると、既存のすべての IoT Hub がアップグレードされ、デバイス管理機能が利用できるようになります。

## 前提条件

このチュートリアルでは、Windows の開発用コンピューターを使用することを前提としています。

手順を完了するには、次のものがインストールされている必要があります。

- Microsoft Visual Studio 2015

- Git

- CMake (バージョン 2.8 以降)。CMake は <https://cmake.org/download/> からインストールします。Windows PC では、Windows インストーラー (.msi) オプションを選択してください。CMake を現在のユーザーの PATH 変数に追加するチェック ボックスをオンにしてください。

- Node.js 6.1.0 以上。お使いのプラットフォーム向けの Node.js を <https://nodejs.org/> からインストールします。

- 有効な Azure サブスクリプションアカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。

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
  -   **[Enable Device Management (デバイス管理の有効化)]** チェック ボックスをオンにします。
  -   **[場所]** で、IoT Hub をホストする場所を選択します。IoT Hub デバイス管理を使用できるのは、パブリック プレビューの段階では米国東部、北ヨーロッパ、および東アジアでのみです。将来的には、すべてのリージョンで使用可能になる予定です。

    > [AZURE.NOTE]  **[Enable Device Management (デバイス管理の有効化)]** チェック ボックスをオンにしないと、サンプルは動作しません。

4.  必要な IoT Hub 構成オプションを選択したら、**[作成]** をクリックします。Azure が IoT Hub を作成するまでに数分かかる場合があります。状態を確認するには、**スタート画面**または **[通知]** パネルで進行状況を監視してください。

	![][img-monitor]

5.  IoT Hub が正常に作成されたら、新しい IoT Hub のブレードを開き、**[ホスト名]** を書き留めて**鍵**のアイコンをクリックします。

	![][img-keys]

6.  **[iothubowner]** ポリシーをクリックし、**[iothubowner]** ブレードで接続文字列をコピーして書き留めます。このチュートリアルの残りの部分を完了するために必要になるので、後でアクセスできる場所にコピーしておきます。

 	> [AZURE.NOTE] 運用環境のシナリオでは、**iothubowner** 資格情報を使用しないようにしてください。

	![][img-connection]

これで、デバイス管理が有効な IoT Hub が作成されました。このチュートリアルの残りの部分を完了するには、接続文字列が必要です。

## サンプルのビルドと IoT Hub へのデバイスのプロビジョニング

このセクションでは、シミュレートされたデバイスとサンプルをビルドし、IoT Hub のデバイス レジストリに一連の新しいデバイス ID をプロビジョニングするスクリプトを実行します。デバイスは、デバイス レジストリに登録されていないと、IoT Hub に接続できません。

サンプルをビルドし、IoT Hub にデバイスをプロビジョニングするには、次の手順を実行します。

1.  **開発者コマンド プロンプト for VS2015** を開きます。

2.  Github リポジトリを複製します。**スペースがないディレクトリに複製してください。**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  **azure-iot-sdks** リポジトリを複製したルート フォルダーから **\\azure-iot-sdks\\csharp\\service\\samples** フォルダーに移動して、次のスクリプトを実行します。プレースホルダーの値は、前のセクションの接続文字列に置き換えます。

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

このスクリプトでは、次の処理が実行されます。

1.  **cmake** を実行し、シミュレートされたデバイス用に Visual Studio 2015 ソリューションを作成します。このプロジェクト ファイルは、**azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj** です。ソース ファイルは **azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample** フォルダーにあることに注意してください。

2.  シミュレートされたデバイスの **iotdm\_simple\_sample.vcxproj** プロジェクトをビルドします。

3.  デバイス管理のサンプル **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln** をビルドします。

4.  **GenerateDevices.exe** を実行し、IoT Hub にデバイス ID をプロビジョニングします。デバイスは **sampledevices.json** (**azure-iot-sdks\\node\\service\\samples** フォルダー内) に記述されており、デバイスがプロビジョニングされると、資格情報が **devicecreds.txt** ファイル (**azure-iot-sdks\\csharp\\service\\samples\\bin** フォルダー内) に格納されます。

## シミュレートされたデバイスの起動

デバイスがデバイス レジストリに追加されたので、シミュレートされた管理デバイスを起動できます。Azure IoT Hub でプロビジョニングされたデバイス ID ごとに、1 つのシミュレートされたデバイスが起動されます。

開発者コマンド プロンプトを使用して、**\\azure-iot-sdks\\csharp\\service\\samples\\bin** フォルダーで次のスクリプトを実行します。

  ```
  simulate.bat
  ```

このスクリプトは、**devicecreds.txt** ファイルに記載されているデバイスごとに、**iotdm\_simple\_sample.exe** のインスタンスを 1 つ実行します。シミュレートされたデバイスは、コマンド ウィンドウを閉じるまで実行され続けます。

**iotdm\_simple\_sample** サンプル アプリケーションは、C 用の Azure IoT Hub デバイス管理クライアント ライブラリを使用してビルドされます。これにより、Azure IoT Hub によって管理できる IoT デバイスの作成が可能になります。デバイスの製造元は、このライブラリを使用して、デバイスのプロパティをレポートし、デバイスのジョブに必要な実行アクションを実装することができます。このライブラリは、オープン ソース Azure IoT Hub SDK の一部として提供されるコンポーネントです。

**simulate.bat** を実行すると、出力ウィンドウにデータのストリームが表示されます。この出力には、受信トラフィックと送信トラフィックだけでなく、アプリケーション固有のコールバック関数の **printf** ステートメントも表示されます。そのため、受信トラフィックと送信トラフィックと共に、デコードされたパケットをサンプル アプリケーションがどのように処理しているかも確認できます。デバイスが IoT Hub に接続すると、サービスがデバイス上のリソースの監視を自動的に開始します。その後、IoT Hub DM クライアント ライブラリは、デバイス コールバックを呼び出して、デバイスから最新の値を取得します。

**iotdm\_simple\_sample** サンプル アプリケーションからの出力を次に示します。上部には、成功を示す **REGISTERED** メッセージと、IoT Hub に接続しているデバイス (ID: **Device11-7ce4a850**) が表示されます。

> [AZURE.NOTE]  出力から詳細を省くには、製品版構成をビルドして実行します。

![][img-output]

以下のセクションを完了するまで、シミュレートされたデバイスはすべて実行状態にしておいてください。

## デバイス管理サンプル UI を実行する

IoT Hub のプロビジョニングが完了し、複数のシミュレートされたデバイスを実行して管理用に登録したので、デバイス管理サンプル UI をデプロイすることができます。このデバイス管理サンプル UI では、デバイス管理 API を利用して対話型の UI エクスペリエンスを構築する方法を示す実際の例が提供されます。[既知の問題](https://github.com/Azure/azure-iot-device-management#knownissues)を含むデバイス管理サンプル UI の詳細については、[Azure IoT デバイス管理 UI][lnk-dm-github] の GitHub リポジトリを参照してください。

デバイス管理サンプル UI を取得、ビルド、実行するには、次の手順に従います。

1. **コマンド プロンプト**を開きます。

2. 「`node --version`」と入力し、前提条件のセクションに従って Node.js 6.1.0 以上がインストールされていることを確認します。

3. 次のコマンドを実行して、Azure IoT デバイス管理 UI の GitHub リポジトリを複製します。

	```
	git clone https://github.com/Azure/azure-iot-device-management.git
	```
	
4. 複製した Azure IoT デバイス管理 UI リポジトリのコピーのルート フォルダーで、次のコマンドを実行して依存パッケージを取得します。

	```
	npm install
	```

5. npm インストール コマンドが完了したら、次のコマンドを実行してコードをビルドします。

	```
	npm run build
	```

6. テキスト エディターで、複製したフォルダーのルートにある user-config.json ファイルを開きます。テキスト "&lt;YOUR CONNECTION STRING HERE&gt;" を前のセクションで確認した IoT Hub の接続文字列に置き換え、ファイルを保存します。

7. コマンド プロンプトで次のコマンドを実行して、デバイス管理 UX アプリを起動します。

	```
	npm run start
	```

8. コマンド プロンプトに "Services have started" と表示されたら、Web ブラウザー (現時点では Edge/IE 11 以降/Safari/Chrome がサポートされています) を開き、次の URL にあるデバイス管理アプリに移動して、シミュレートされたデバイスを確認します: <http://127.0.0.1:3003>。

	![][img-dm-ui]

次のデバイス管理チュートリアルに進むので、シミュレートされたデバイスとデバイス管理アプリは実行状態のままにしておいてください。


## 次のステップ

Azure IoT Hub デバイス管理機能について学習を継続するには、「[Explore Azure IoT Hub device management using the sample UI (サンプル UI を使用して Azure IoT Hub デバイス管理について確認する)][lnk-sample-ui]」チュートリアルを参照してください。

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure ポータル]: https://portal.azure.com/
[リソース グループを使用した Azure リソースの管理]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md

<!---HONumber=AcomDC_0615_2016-->