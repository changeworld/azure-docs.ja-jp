<properties 
	pageTitle="Telestream Wirecast エンコーダーを構成して単一ビットレートのライブ ストリームを送信する | Microsoft Azure" 
	description="このトピックでは、Wirecast ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako;cenkdin;anilmur"/>

#Wirecast エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

このトピックでは、[Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。詳細については、「[Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。このツールは、Windows PC でのみ実行されます。Mac または Linux の場合は、Azure クラシック ポータルを使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) と [プログラム](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)を作成します。


##前提条件

- [Azure Media Services アカウントの作成](media-services-create-account.md)
- 1 つ以上のストリーミング ユニットが割り当てられたストリーミング エンドポイントが実行中であることを確認します。詳細については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)」を参照してください。
- 最新バージョンの [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ツールをインストールします。
- ツールを起動し、AMS アカウントに接続します。

##ヒント

- 可能な限り、有線のインターネット接続を使用します。
- 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。これは必須の要件ではありませんが、ネットワークの混雑の影響を軽減することができます。
- ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。


## チャネルの作成

1.  AMSE ツールで、**[Live]** タブに移動して、チャネル領域内を右クリックします。メニューから **[チャネルの作成]** を選択します。

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。[チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTMP]** に設定して、**[Standard]** を選択します。それ以外の設定はすべてそのままにしておくことができます。


**[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. **[チャネルの作成]** をクリックします。![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] チャネルの開始までに 20 分程度かかることがあります。

チャネルを開始している間に、[エンコーダーを構成する](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)ことができます。

>[AZURE.IMPORTANT] チャネルが準備完了の状態になるとすぐに課金が開始されることに注意してください。詳細については、「[チャネルの状態](media-services-manage-live-encoder-enabled-channels.md#states)」を参照してください。

##<a id=configure_wirecast_rtmp></a>Telestream Wirecast エンコーダーを構成する

このチュートリアルでは、次の出力設定が使用されます。このセクションの残りの部分では、構成の手順の詳細について説明します。

**ビデオ**:
 
- コーデック: H.264
- プロファイル: 高 (レベル 4.0)
- ビットレート: 5000 kbps
- キーフレーム: 2 秒 (60 秒)
- フレーム レート: 30
 
**オーディオ**:

- コーデック: AAC (LC)
- ビットレート: 192 kbps
- サンプル レート: 44.1 kHz


###構成の手順

1. 使用中のコンピューターで、Telestream Wirecast アプリケーションを開き、RTMP ストリーミング用の設定を行います。
2. **[出力]** タブに移動して **[出力設定]** を選択し、出力を構成します。
	
	**[出力先]** に **[RTMP Server]** が設定されていることを確認します。
3. **[OK]** をクリックします。
4. 設定ページで、**[ターゲット]** フィールドを **[Azure Media Services]** に設定します。
 
	エンコードのプロファイルは、**Azure H.264 720 p 16:9 (1280 x 720)** に事前に選択されています。これらの設定をカスタマイズするには、ドロップダウンの右側にある歯車アイコンを選択し、**[新しいプリセット]** を選択します。

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. エンコーダー プリセットを構成します。

	プリセットに名前を付けて、次の推奨設定を確認します。

	**ビデオ**
	
	- エンコーダー: MainConcept H.264
	- 1 秒あたりのフレーム数: 30
	- 平均ビットレート: 5000 kbps (ネットワークの制限に基づいて調整可能)
	- プロファイル: メイン
	- キーフレームの距離: 60 フレーム

	**オーディオ**

	- ターゲットのビットレート: 192 kbps
	- サンプル レート: 44.100 kHz
	 
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. **[保存]** をクリックします。

	エンコードのフィールドで、新しく作成したプロファイルを選択できるようになりました。

	新しいプロファイルが選択されていることを確認します。

7. Wirecast の **RTMP エンドポイント**に割り当てるために、チャネルの入力 URL を取得します。
	
	AMSE ツールに戻り、チャネルの完了状態を確認します。状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。
	  
	チャネルが実行されている場合、チャネル名を右クリックして、下へ移動して **[入力 URL をクリップボードにコピー]** にマウスを合わせ、**[プライマリ入力 URL]** を選択します。
	
	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Wirecast の **[出力設定]** ウィンドウで、この情報を出力セクションの **[アドレス]** フィールドに貼り付け、ストリーム名を割り当てます。


	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. **[OK]** を選択します。

10. メインの **[Wirecast]** 画面で、ビデオとオーディオの入力ソースの準備が整っていることを確認し、左上隅の **[ストリーム]** をクリックします。

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT] **[ストリーム]** をクリックする前に、チャネルの準備が整っていることを確認する**必要**があります。また、15 分を超える入力投稿フィードがある場合を除き、チャネルを準備可能のままにしないでください。

##再生をテストする
  
1. AMSE ツールに移動し、テストするチャネルを右クリックします。メニューが表示されたら、**[プレビューの再生]** にマウスを合わせ、**[Azure Media Player を使用]** を選択します。

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関するトピックをご覧ください。

##プログラムを作成する

1. チャネルの再生が確認されたら、プログラムを作成します。AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、**[新しいプログラムの作成]** を選択します。

	![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. 必要に応じてプログラムに名前を付け、**[アーカイブ ウィンドウの長さ]** (既定では 4 時間) を調整します。ストレージの場所を指定することも、既定値のままにすることもできます。
3. **[プログラムを今すぐ開始]** ボックスを選択します。
4. **[プログラムの作成]** をクリックします。
  
	注: プログラムの作成は、チャネルの作成ほど時間はかかりません。
 
5. プログラムが実行されたら、再生を確認するために、プログラムを右クリックして **[プログラムの再生]** に移動し、**[Azure Media Player を使用]** を選択します。
6. 確認したら、プログラムをもう一度クリックし、**[出力 URL をクリップボードにコピー]** を選択します (または、メニューの **[プログラムの情報と設定]** オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。


## トラブルシューティング
 
ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関するトピックをご覧ください。

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->