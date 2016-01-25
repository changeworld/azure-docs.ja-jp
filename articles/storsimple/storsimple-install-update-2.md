<properties 
   pageTitle="StorSimple デバイスへの Update 2 のインストール | Microsoft Azure"
   description="StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 2 をインストールする方法について説明します。"
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
   ms.date="01/12/2016"
   ms.author="alkohli" />

# StorSimple デバイスへの Update 2 のインストール

## 概要

このチュートリアルでは、Update 2 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure クラシック ポータル経由で Update 2 をインストールする方法について説明します。このチュートリアルでは、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されているときに、Update 1 より前のソフトウェア バージョンから更新する場合に、この更新プログラムで必要な手順についても説明します。

Update 2 には、デバイス ソフトウェアの更新プログラム、LSI ドライバーの更新プログラム、およびディスク ファームウェアの更新プログラムが含まれます。デバイス ソフトウェアと LSI の更新プログラムは中断を伴わない更新プログラムであるため、Azure クラシック ポータルを使用して適用できます。ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスでのみ適用できます。

> [AZURE.IMPORTANT]
 
> -  更新プログラムは段階的に公開されるため、Update 2 が即座に表示されない場合があります。これらの更新プログラムは間もなく利用可能になるため、数日後にもう一度更新プログラムの有無を確認してください。
> - インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。これらの事前チェックは、Azure クラシック ポータルから更新プログラムを適用する場合にのみ実行されます。 
> - ソフトウェアとドライバーの更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。更新プログラムのインストールには、(Windows の更新プログラムを含めて) 4 ～ 7 時間かかる場合があります。メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> - オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 2 にアップグレードしたことを確認します。

## 更新プログラムの準備
更新プログラムをスキャンして適用する前に、次の手順を実行する必要があります。


1. デバイス データのクラウド スナップショットを取得します。

2. コントローラーの固定 IP アドレスがルーティング可能でインターネットに接続できることを確認します。これらの固定 IP は、デバイスに更新プログラムを提供するために使用されます。デバイスの Windows PowerShell インターフェイスから、各コントローラーで次のコマンドレットを実行してこれをテストできます。

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

これらの手動の事前チェックが正常に完了した後で、更新プログラムのスキャンとインストールを開始できます。

## Azure クラシック ポータルを使用して Update 2 をインストールする 

これは、デバイスを更新するための推奨される手順です。次の手順に従います。

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Update 2 を修正プログラムとしてインストールする 

この手順は、Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合にのみ使用してください。このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムを使用してアップグレードできるソフトウェアのバージョンは、Update 0.1、Update 0.2、Update 0.3、Update 1、Update 1.1、および Update 1.2 です。修正プログラムを使用する方法には、次の 3 つの手順が含まれます。

- Microsoft Update カタログから修正プログラムをダウンロードします。
- 通常モードの修正プログラムをインストールして確認します。
- メンテナンス モードの修正プログラムをインストールして確認します。

この方法で適用される修正プログラムを次の表に示します。

| 順序 | KB | 名前 | パッケージの説明 | 更新の種類 |
|--------|-----------|-------------------------|-----------------------------|-------------|
| 1 | KB3121901 | ソフトウェア更新 | HcsMdsSfotwareUpdate.exe <br></br> CisMdsAgentUpdateBundle.exe | 通常 |
| 2 | KB3121900 | LSI ドライバー | HcsLsiUpdate.exe | 通常 |
| 3 | KB3080728 | Storport 修正 | Storport-KB3080728-x64.msu | 通常 |
| 4 | KB3090322 | Spaceport 修正 | Spaceport-KB3090322-x64.msu | 通常 |
| 5 | KB3121899 | ディスク ファームウェア | DiskFirmwarePackage.exe | メンテナンス |


> [AZURE.IMPORTANT]
> 
> - デバイスがリリース (GA) バージョンを実行している場合、この更新プログラムの使用については、[Microsoft サポート](storsimple-contact-microsoft-support.md)にお問い合わせください。
> - この手順は、Update 2 を適用するために 1 回だけ実行する必要があります。以降の更新プログラムは、Azure クラシック ポータルを使用して適用できます。
> - 各修正プログラムのインストールは、完了するまで約 20 分かかる可能性があります。インストール時間の合計は 2 時間近くになります。 
> - この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。

Update 2 を修正プログラムとして適用するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## 更新プログラムのエラーに関するトラブルシューティング

**アップグレード前のチェックが失敗したことを示す通知が表示されるとどうなりますか。**

事前チェックに失敗した場合は、ページの下部に詳細な通知バーが表示されていることを確認します。ここには、どの事前チェックが失敗したかに関するガイダンスが表示されます。次の図に、このような通知が表示される場合の例を示します。この場合は、コントローラーの正常性チェックとハードウェア コンポーネントの正常性チェックに失敗しています。**[ハードウェアの状態]** セクションで、**コントローラー 0** と**コントローラー 1** の両方のコンポーネントに注意する必要があることがわかります。
 
  ![事前チェックのエラー](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

両方のコントローラーが正常な状態でオンラインであるかどうかを確認する必要があります。[メンテナンス] ページで、StorSimple デバイス内のすべてのハードウェア コンポーネントが正常であると表示されているかどうかも確認する必要があります。確認したら更新プログラムをインストールできます。ハードウェア コンポーネントの問題を解決できない場合は、Microsoft サポートに対処法をお問い合わせいただく必要があります。

**更新プログラムをインストールできないことを示すエラー メッセージが表示され、更新プログラムに関するトラブルシューティング ガイドを参照してエラーの原因を特定するよう推奨されている場合はどうすればよいですか。**

この問題で考えられる原因の 1 つは、Microsoft Update サーバーに接続できないことです。この場合、手動チェックを実行する必要があります。Update サーバーに接続できない場合は、更新ジョブは失敗します。StorSimple デバイスの Windows PowerShell インターフェイスから次のコマンドレットを実行して、接続を確認できます。

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

両方のコントローラーでコマンドレットを実行します。
 
接続の存在を確認したにもかかわらず、この問題が解決しない場合は、Microsoft サポートに対処法をお問い合わせください。


## 次のステップ

詳細については、「[Update 2 リリース ノート](storsimple-update2-release-notes.md)」を参照してください。

<!---HONumber=AcomDC_0114_2016-->