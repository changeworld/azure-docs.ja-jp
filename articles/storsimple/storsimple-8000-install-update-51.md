---
title: StorSimple 8000 シリーズ デバイスへの Update 5.1 のインストール | Microsoft Docs
description: StorSimple 8000 シリーズ デバイスに StorSimple 8000 シリーズの Update 5.1 をインストールする方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/21/2021
ms.author: alkohli
ms.openlocfilehash: 289ffbbd3dc23be0060ee5dbe488bf80323214e2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897219"
---
# <a name="install-update-51-on-your-storsimple-device"></a>StorSimple デバイスへの Update 5.1 のインストール

## <a name="overview"></a>概要

このチュートリアルでは、Update 5.1 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure Portal 経由で Update 5.1 をインストールする方法、または修正プログラムを使用してインストールする方法について説明します。

更新プログラム5.1 には、Azure portal または修正プログラムのメソッドによって適用できる、中断のないセキュリティ更新プログラムが含まれています。

Azure portal から Update 5.1 を適用する場合は、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure portal から更新プログラムを適用する場合にのみ行われます。

修正プログラムのメソッドを使用する場合は、まず、「[StorSimple デバイスに Update 5 をインストールする](storsimple-8000-install-update-5.md)」の手順に従って update 5 をインストールすることを強くお勧めします。 次に、Update 5.1 をインストールするには、次の「[修正プログラムとして Update 5.1 をインストールする](#install-update-51-as-a-hotfix)」の手順に従います。

Update 5.1 のセキュリティ更新プログラムは、インストールに約 30 分かかります。

> [!IMPORTANT]
> * Update 5.1 は必須の更新プログラムであり、すぐにインストールする必要があります。 詳細については、[Update 5.1 リリース ノート](storsimple-update51-release-notes.md)に関する記事を参照してください。
> * サポートされる最小バージョンは Update 5 です。

> [!NOTE]
> * ソフトウェアとその他の通常の更新プログラムのインストールには Azure Portal を使用することをお勧めします。 <!--You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device. - N/A FOR 5.1? No maintenance mode updates, and the security updates go quickly.-->
> * 修正プログラムのメソッドを使用してインストールする場合は、インストールを開始する前に [Microsoft サポート](mailto:support@microsoft.com) に問い合わせる必要があります。

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Azure portal を使用して Update 5.1 をインストールする

デバイスを [Update 5.1](storsimple-update51-release-notes.md) に更新するには、次の手順を実行します。

> [!NOTE]
> Microsoft はデバイスから追加の診断情報を取得します。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。

#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure ポータルから 更新プログラムをインストールするには

1. StorSimple サービス ページでデバイスを選択します。

    ![デバイスの削除](./media/storsimple-8000-install-update-51/update1.png)

2. **[デバイスの設定]**  >  **[デバイスの更新プログラム]** の順に移動します。

    ![[設定] ブレードのスクリーンショット。[デバイスの更新] オプションのコールアウトが示されています。](./media/storsimple-8000-install-update-51/update2.png)

3. 新しい更新プログラムが利用できる場合は、通知が表示されます。 または、 **[デバイスの更新プログラム]** ブレードで **[更新プログラムのスキャン]** をクリックします。 利用可能な更新プログラムをスキャンするジョブが作成されます。 ジョブが正常に完了すると、その旨が通知されます。

    ![[設定] ブレードのスクリーンショット。[デバイスの更新] オプションのコールアウトが示され、[デバイスの更新] ブレードでは [新しい通常の更新プログラムを利用できます] というメッセージがコールアウトされています。](./media/storsimple-8000-install-update-51/update3.png)

4. 更新プログラムをデバイスに適用する前に、リリース ノートを確認することをお勧めします。 **[更新プログラムのインストール]** をクリックすると、更新プログラムが適用されます。 **[定期更新プログラムの確認]** ブレードで、更新プログラムを適用する前に完了する必要のある前提条件を確認します。 デバイスを更新する準備ができたことを示すチェック ボックスをオンにし、 **[インストール]** をクリックします。

    ![[デバイスの更新] ブレードのスクリーンショット。[更新プログラムのインストール] オプションのコールアウトと、[定期的な更新プログラムの確認] の同意オプションと [インストール] オプションのコールアウトが示されています。](./media/storsimple-8000-install-update-51/update4.png)

5. 一連の前提条件のチェックが開始されます。 これらのチェックは次のとおりです。
   
   * **コントローラーの正常性チェック** では、両方のデバイス コントローラーが正常であり、オンラインであることを確認します。
   * **ハードウェア コンポーネントの正常性チェック** では、StorSimple デバイスのすべてのハードウェア コンポーネントが正常であることを確認します。
   * **DATA 0 チェック** では、デバイスで DATA 0 が有効であることを確認します。 このインターフェイスが有効でない場合は、有効にしてから再試行する必要があります。

     すべてのチェックが正常に完了した場合にのみ、更新プログラムがダウンロードされてインストールされます。 チェックが実行中のときは通知されます。 事前チェックに失敗した場合、失敗の理由が表示されます。 それらの問題を解決してから操作をやり直してください。 これらの問題に自分で対処できない場合、Microsoft サポートに連絡することが必要になる場合があります。

6. 事前チェックが正常に完了したら、更新ジョブが作成されます。 更新ジョブが正常に作成されると、通知されます。
   
    ![[ソフトウェアの更新ジョブを開始しています] という通知のスクリーンショット。](./media/storsimple-8000-install-update-51/update6.png)
   
    その後、更新プログラムがデバイスに適用されます。

7. 更新の完了には数時間かかります。 更新ジョブを選択し、 **[詳細]** をクリックすると、ジョブの詳細をいつでも表示できます。

    ![[デバイスの更新] ブレードのスクリーンショット。[ソフトウェア更新プログラムのダウンロードとインストールが進行中です] オプションのコールアウトと、[更新プログラムのインストール] ブレードが表示されています。](./media/storsimple-8000-install-update-51/update8.png)

     **[デバイスの設定]、[ジョブ]** の順に移動して、更新ジョブの進行状況を監視することもできます。 **[ジョブ]** ブレードで、更新の進行状況を確認できます。

     ![[設定] ブレードのスクリーンショット。[ジョブ] のコールアウトが表示され、[ジョブ] ブレードに更新の進行状況が示されています。](./media/storsimple-8000-install-update-51/update7.png)

8. ジョブが完了したら、 **[デバイスの設定]、[デバイスの更新プログラム]** の順に移動します。 ソフトウェアのバージョンが更新されています。

   デバイスで **StorSimple 8000 Series Update 5.1 (6.3.9600.17885)** が実行されており、 **最終更新日** が今日の日付であることを確認します。

## <a name="install-update-51-as-a-hotfix"></a>Update 5.1 を修正プログラムとしてインストールする

Update 5.1 を修正プログラムとしてインストールする場合は、インストールを開始する前に、次の手順を実行します。

* Update 5.1 をインストールする前に、Update 5 をインストールします。 手順については、「[StorSimple デバイスへ Update 5 をインストールする](storsimple-8000-install-update-5.md)」を参照してください。
* 修正プログラムのインストールを開始する前に、 [Microsoft サポート](mailto:support@microsoft.com)にお問い合わせください。

修正プログラムを使用する方法には、次の手順が含まれます。

1. Microsoft Update カタログから修正プログラムをダウンロードします。
2. 通常モードの修正プログラムをインストールして確認します。
3. メンテナンス モードの修正プログラムをインストールして確認します。

#### <a name="download-updates-for-your-device"></a>デバイスの更新プログラムをダウンロードする

指定の順序で推奨されるフォルダーで、次の修正プログラムをダウンロードしてインストールする必要があります。

| 注文 | KB       | 説明 | 更新の種類 | インストール時間 |インストールするフォルダー|
|-------|----------|------------ |-------------|--------------|----- |
|1.     |KB4542887|ソフトウェア更新<br>_HcsSoftwareUpdate.exe_ と _CisMSDAgent.exe_ の両方をダウンロード |通常 <br></br>中断なし |～ 25 分 |FirstOrderUpdate|
|3.<sup>1, 2</sup>     |KB4037263|ディスク ファームウェア|メンテナンス <br></br>中断あり|～ 30 分|ThirdOrderUpdate|

<sup>1</sup> Update 5.1 では、2 番目の注文の更新プログラムはありません。

<sup>2</sup> Update 5 の修正プログラムの更新プログラムの上にディスク ファームウェアの更新プログラムをインストールしていない場合は、3 番目の更新プログラムをインストールします。

この修正プログラムをダウンロードしてインポートするには、次のステップを実行します。

#### <a name="download-hotfixes"></a>修正プログラムをダウンロードする

修正プログラムをダウンロードするには、「[修正プログラムをダウンロードする](storsimple-8000-install-update-5.md#to-download-hotfixes)」を参照してください。

#### <a name="install-and-verify-device-updates"></a>デバイスの更新プログラムをインストールして検証する

「**Update 5 を StorSimple デバイスにインストールする**」の「[通常モードの修正プログラムをインストールして検証する](storsimple-8000-install-update-5.md#to-install-and-verify-regular-mode-hotfixes)」の手順に従って、KB4542887 でデバイスの更新プログラムをインストールます。

> [!IMPORTANT]
> まだ [Microsoft サポート](mailto:support@microsoft.com)に連絡していない場合は、修正プログラムをインストールする前に、これを行う必要があります。

手順に従って、最初の更新プログラムをインストールします。 Update 5.1 では、2 番目の更新プログラムはありません。

Update 5.1 の場合は、インストール後にこれらのソフトウェアのバージョンを確認します。

 * FriendlySoftwareVersion: StorSimple 8000 Series Update 5.1
 * HcsSoftwareVersion: 6.3.9600.17885
 * CisAgentVersion: 1.0.9777.0
 * MdsAgentVersion: 35.2.2.0
 * Lsisas2Version: 2.0.78.00

#### <a name="install-and-verify-disk-firmware-updates"></a>ディスクファームウェアの更新プログラムをインストールして確認する

Update 5 をインストールしたときにディスク ファームウェアの更新プログラムをインストールしていない場合は、「**Update 5 を StorSimple デバイスにインストールする**」の「[通常モードの修正プログラムをインストールして検証する](storsimple-8000-install-update-5.md#to-install-and-verify-maintenance-mode-hotfixes)」の手順に従って、KB4037263 でディスク ファームウェアの更新プログラムをインストールます。

`XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N003`、`0107`の各ファームウェア バージョンを実行している場合は、ディスク ファームウェアの更新プログラムをインストールする必要はありません。

ディスク ファームウェアの更新プログラムが必要かどうかを確認するには、`Get-HcsFirmwareVersion` コマンドレットを実行します。

## <a name="next-steps"></a>次のステップ

詳しくは、[Update 5.1 リリース](storsimple-update51-release-notes.md)に関するページをご覧ください。
