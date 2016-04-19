<properties 
	pageTitle="MyDriving Azure IoT の例 - クイック スタート | Microsoft Azure" 
	description="Microsoft Azure で Stream Analytics、Machine Learning、Event Hubs などの IoT システムを設計する方法を総合的に示すアプリを初めて使用する。" 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>

# MyDriving IoT システム: クイック スタート

MyDriving は、デバイスからテレメトリを収集し、そのデータをクラウドで処理して、機械学習を適用することで状況に適した応答を返すという、典型的な[モノのインターネット](iot-suite-overview.md) (IoT) ソリューションの設計と実装を示すシステムです。このシステムは、ユーザーの携帯電話と、自動車の制御システムから情報を取得するオンボード診断 (OBD) アダプターの両方を使用して、車の走行に関するデータを記録します。また、このデータを基に、ユーザーの運転スタイルを他のユーザーと比較してフィードバックします。


![](./media/iot-solution-get-started/image5.png)

MyDriving の真の目的は、皆さんが独自の IoT ソリューションを作成する出発点になることです。しかしその前に、私たちのテスト ユーザー チームの一員として、MyDriving アプリそのものをご覧いただくことしましょう。そうすることで、アーキテクチャを詳しく検討する前に、コンシューマーとして、このアプリケーションとシステムに触れることができます。加えて、テスト ユーザーに対するアルファ版やベータ版のアプリ配布を管理する優れたツール、HockeyApp をご紹介します。

## モバイル機器での利用

**前提条件**:

Android、iOS、または Windows 10 デバイス。

## Android または Windows Phone 10 でのインストール

ご利用のデバイスで以下を実行してください。

1.  **開発アプリを許可します**。

    -   Android: **[設定]** 内の **[セキュリティ]** に移動し、**[提供元不明のアプリ]** でアプリを許可します。

    -   Windows 10: **[設定]**、**[更新]**、**[開発者向け]** の順に移動し、**[開発者モード]** を設定します。

2.  **ベータ テスト チームに参加します**。

    HockeyApp を使用すると、簡単にアプリの最新リリースがテスト ユーザーに配布されます。

    ご利用のモバイル デバイスで以下を実行します。

    -   <https://rink.hockeyapp.net> で HockeyApp に**サインアップまたはサインイン**します。
    
        (Windows 10 をお使いの場合は、Edge ブラウザーをご使用ください。)

        *Build 2016 参加者* – いずれかの Microsoft ボタンを押して、カンファレンスに登録したものと同じ MSA 電子メールでサインインします。HockeyApp へのサインアップは済んでいます。

        ![](./media/iot-solution-get-started/image1.png)

3.  次の場所からアプリを**ダウンロードしてインストールします**。

    -   Android: <http://rink.io/spMyDrivingAndroid>

    -   Windows 10 Phone: <http://rink.io/spMyDrivingUWP>

        2 つのアイテムがあります。[信頼されたユーザー] で証明書をインストールします。次にアプリをインストールします。
    
*Windows 10 Phone で問題が生じる場合は、* ご使用の携帯電話で、ここ 1 ～ 2 回の更新が行われていない可能性があります。最新の更新プログラムが適用されていることを確認し、そうでない場合は以下をインストールしてください。

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx) 
   

## iOS でのインストール

### Build 2016 参加者

Build 2016 の参加者は、私たちのテスト チームのメンバーとして、HockeyApp でアプリをダウンロードしてください。

1.  ご使用の iOS デバイスで、<https://rink.hockeyapp.net> にサインインします。いずれかの Microsoft サインイン ボタンを押して、カンファレンスに登録したものと同じ MSA 電子メールでサインインします(メールとパスワードのフィールドは使用しないでください)。

    ![](./media/iot-solution-get-started/image1.png)

2.  HockeyApp ダッシュボードで MyDriving を選択し、ダウンロードします。

3.  次の手順で、HockeyApp から入手したベータ版を承認します。

    **[設定]**、**[一般]**、**[プロファイルとデバイス管理]** の順に移動します。

    **Bit Stadium GmbH** の証明書を信頼します。

### Build 2016 に参加していない方

近日中に iOS ストアでアプリをリリースする予定です。

現在のところは、次のようにアプリをビルドして、デプロイすることができます。

-   [GitHub から]コードをダウンロードします。

-   [Xamarin を使用して、ビルドとデプロイを実行します]。

『[MyDriving Reference Guide (MyDriving リファレンス ガイド)](http://aka.ms/mydrivingdocs)』で詳細をご確認ください。

## OBD アダプターの入手 (オプション)

これは真のモノのインターネット システムを実現するパーツです。 これなしでもアプリは使用できますが、実際のモノと組み合わせた方がおもしろく、それでいて費用はかさみません。

オンボード診断 (OBD) は自動車に備わった機能であり、整備工場で調整を行ったり、異音や警告ランプの原因を診断したりするために使用されます。年代物の車でなければ、ダッシュボードの下にソケットが付いています。適切なコネクタを使用して、エンジン性能の指標を収集し、特定の調整を行うことができます。OBD コネクタは取扱い店が多く、安価で購入できます。Bluetooth または WiFi で、携帯電話のアプリに接続します。

ただし、ここで私たちがやろうとしているのは、自動車をクラウドに接続しようという試みです。つまり、OBD と直接接続するのは携帯電話ですが、アプリがリレーとして機能するわけです。自動車のテレメトリは MyDriving IoT Hub に直接送られ、そこで走行経路の記録と運転スタイルの評価が行われます。

### OBD デバイスの接続


1.  車に OBD コネクタが付いていることを確認します (ビンテージ カーでない限り、普通はあります)。車室内のどこかに配置されており、通常はダッシュボード下方のパネルの後ろにあります。

2.  OBD アダプターを入手します。私たちは、次のタイプを使用しました。

    ご使用の携帯電話により異なります。

    -   **Android または Windows** Phone の場合は、**Bluetooth 対応の OBD II** アダプターが必要です。私たちは、[BAFX Products 34t5 Bluetooth OBDII Scan Tool] を使用しました。

    -   **iOS** 搭載の携帯電話の場合は、**WiFi 対応の** OBD アダプターが必要です。私たちは、[ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner] を使用しました。

3.  OBD アダプターに付属の指示に従い、携帯電話に接続します。

    -   Bluetooth アダプターは設定ページで携帯電話とペアリングする必要があります。

    -   WiFi アダプターには、192.168.xxx.xxx の範囲のアドレスを設定する必要があります。

4.  複数台の車両で利用する場合は、それぞれに別個のアダプターを用意してください (3 個まで)。

OBD アダプターがない場合でも、アプリは携帯電話の GPS レシーバーで取得した位置情報と速度データをバックエンドに送信します。さらに、OBD をシミュレートするかどうかの確認が行われます。

アプリによる OBD アダプターのデータの使用方法と、独自の OBD デバイスを作成するオプションについては、『[MyDriving Reference Guide (MyDriving リファレンス ガイド)](http://aka.ms/mydrivingdocs)』のセクション 2.1「IoT Devices (IoT デバイス)」を参照してください。

## アプリの使用

アプリを**起動**します。初回のクイックスタートで、機能をひととおり確認できます。

-   **走行をトラッキングします。** 走行を開始するときは記録ボタン (画面下部の大きな赤い丸) をタップし、終了するときは再びタップします。


    ![](./media/iot-solution-get-started/image2.png)

-   OBD デバイスを使用していない場合は、走行を開始するたびに、シミュレーターを使用するかどうかの確認が行われます。

-   走行終了時に停止ボタンをタップすると、下の図のような概要が示されます。

    ![](./media/iot-solution-get-started/image3.png)

-   **走行内容を確認します。**

    ![](./media/iot-solution-get-started/image4.png)

-   **自分のプロファイルを確認します。**

    ![](./media/iot-solution-get-started/image5.png)

-   **テストのフィードバックを送信します。**アプリのボタンを使用するか、携帯電話をシェイクするだけで送信できます。 このとき自動でスクリーンショットが添付され、こちらで皆さんの伝達内容を把握することができます。運悪くクラッシュした場合は、HockeyApp によってクラッシュ ログが収集され、通知されます。

## フィードバック 

MyDriving は、独自の IoT システムの開発を支援するために作成されました。そのため、このアプリの動作に関するコメントは大歓迎です。問題や課題が生じた場合や、ご自身のシナリオに合わせて拡張できる点がある場合、より効率的に特定のニーズを達成する方法を見つけた場合、MyDriving やこのドキュメントの改善につながるご提案がある場合は、どうぞご連絡ください。

MyDriving アプリ内では、組み込みの HockeyApp フィードバック メカニズムを利用できます。iOS と Android では端末を単にシェイクするか、[フィードバック] メニュー コマンドを使用してください。[HockeyApp ポータル]からフィードバックを送ることもできます。

[GitHub の Issue (イシュー)] としてアップするか、下部にコメントを残すこともできます (ja-JP 版)。

皆様のご意見をお待ちしております。

## 次のステップ

-   『[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)』を読み、MyDriving システム全体の設計と構築方法について把握します。

-   Azure Resource Manager スクリプトを使用して[独自のシステムを作成し、デプロイします](iot-solution-build-system.md)。『[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)』は、ほとんどのカスタマイズで対象となる領域についても説明しています。

  [GitHub から]: https://github.com/Azure-Samples/MyDriving
  [Xamarin を使用して、ビルドとデプロイを実行します]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [HockeyApp ポータル]: https://rink.hockeyapp.org
  [GitHub の Issue (イシュー)]: https://github.com/Azure-Samples/MyDriving/issues

<!---HONumber=AcomDC_0406_2016-->