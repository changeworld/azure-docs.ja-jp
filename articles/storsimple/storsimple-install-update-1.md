<properties 
   pageTitle="StorSimple デバイスへの Update 1 のインストール"
   description="デバイスに StorSimple 8000 シリーズの Update 1 をインストールする方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# StorSimple デバイスへの Update 1 のインストール

## 概要

このチュートリアルでは、Update 1 より前のソフトウェア バージョンを実行している StorSimple デバイスに Update 1 をインストールする方法について説明します。お使いのデバイスでは、一般提供 (GA) リリース、Update 0.1、Update 0.2、または Update 0.3 のソフトウェアが実行されている可能性があります。

このインストール中に、デバイスで Update 1 よりも前のバージョンが実行されている場合は、そのデバイスでチェックが実行されます。これらのチェックでは、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を確認します。

手動の事前チェックを実行して、次の点について確認するよう求められます。

- コントローラーの固定 IP アドレスがルーティング可能でインターネットに接続できること。これらの IP は、StorSimple デバイスのサービス更新のために使用されます。このテストを実行するには、各コントローラーで次のコマンドレットを実行します。

    `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**固定 IP でインターネットに接続できるときの Test-Connection のサンプル出力**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    
	    


- デバイスを更新する前に、デバイス データのクラウド スナップショットを取得することをお勧めします。

手動のチェック (上記) を確認した後に、一連の更新前のチェックが自動的に実行されます。チェックの内容は次のとおりです

- **コントローラーの正常性チェック**では、両方のデバイス コントローラーが正常であり、オンラインであることを確認します。

- **ハードウェア コンポーネントの正常性チェック**では、StorSimple デバイスのすべてのハードウェア コンポーネントが正常であることを確認します。

- **DATA 0 チェック**では、デバイスで DATA 0 が有効であることを確認します。このインターフェイスが有効でない場合は、有効にしてから再試行する必要があります。

- **DATA 2 と DATA 3 のチェック**では、DATA 2 と DATA 3 のネットワーク インターフェイスが有効でないことを確認します。これらのインターフェイスが有効になっている場合、無効にしてからデバイスを更新する必要があります。このチェックは、GA ソフトウェアを実行するデバイスから更新する場合にのみ実行されます。バージョン 0.1、0.2、または 0.3 を実行しているデバイスでは、このチェックは必要ありません。

- **ゲートウェイ チェック**は、Update 1 より前のバージョンを実行しているデバイスで行われます。このチェックは、ゲートウェイが DATA 0 以外のネットワーク インターフェイス用に構成されているデバイスでのみ実行されます。
 
Update 1 は、すべての更新前のチェックが正常に完了している場合にのみ適用されます。StorSimple デバイスで Update 1 を適用すると、以降の更新では DATA 2 と DATA 3 のチェックも、ゲートウェイのチェックも実行されません。それ以外の事前チェックは実行されます。

## 管理ポータルを使用して Update 1 をインストールする

Azure 管理ポータルを使用して GA バージョンを実行しているデバイスを更新することをお勧めします。デバイスを更新するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## 更新プログラムのエラーに関するトラブルシューティング

**アップグレード前のチェックが失敗したことを示す通知が表示されるとどうなりますか。**

事前チェックに失敗した場合は、ページの下部に詳細な通知バーが表示されていることを確認します。ここには、どの事前チェックが失敗したかに関するガイダンスが表示されます。次の図に、このような通知が表示される場合の例を示します。この場合は、コントローラーの正常性チェックとハードウェア コンポーネントの正常性チェックに失敗しています。**[ハードウェアの状態]** セクションで、コントローラー 0 とコントローラー 1 の両方のコンポーネントに注意する必要があることがわかります。
 
  ![事前チェックのエラー](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

両方のコントローラーが正常な状態でオンラインであるかどうかを確認する必要があります。[メンテナンス] ページで、StorSimple デバイス内のすべてのハードウェア コンポーネントが正常であると表示されているかどうかも確認する必要があります。確認したら更新プログラムをインストールできます。ハードウェア コンポーネントの問題を解決できない場合は、Microsoft サポートに対処法をお問い合わせいただく必要があります。

**更新プログラムをインストールできないことを示すエラー メッセージが表示され、更新プログラムに関するトラブルシューティング ガイドを参照してエラーの原因を特定するよう推奨されている場合はどうすればよいですか。**

この問題で考えられる原因の 1 つは、Microsoft Update サーバーに接続できないことです。この場合、手動チェックを実行する必要があります。Update サーバーに接続できない場合は、更新ジョブは失敗します。StorSimple デバイスの Windows PowerShell インターフェイスから次のコマンドレットを実行して、接続を確認できます。

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

両方のコントローラーでコマンドレットを実行します。
 
接続の存在を確認したにもかかわらず、この問題が解決しない場合は、Microsoft サポートに対処法をお問い合わせください。

## 次のステップ

[Microsoft Azure StorSimple](storsimple-overview.md) の詳細を確認する

<!---HONumber=July15_HO2-->