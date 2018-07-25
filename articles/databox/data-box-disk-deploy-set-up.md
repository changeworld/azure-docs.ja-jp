---
title: Microsoft Azure Data Box Disk の設定| Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk を設定する方法について説明します。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010363"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>チュートリアル: Azure Data Box Disk の開梱、接続、ロック解除

このチュートリアルでは、Azure Data Box Disk の開梱、接続、ロック解除の方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Data Box Disk を開梱する
> * Data Box Disk を接続してロックを解除する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box Disk の注文に関するチュートリアル](data-box-disk-deploy-ordered.md)を完了していること。
2. ディスクの受け取りが済んでいて、ポータルでジョブの状態が **[配信済み]** に更新されていること。
3. Data Box Disk のロック解除ツールをインストールするホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-disk-system-requirements.md)が実行されていること。
    - [Windows PowerShell 4 がインストール済み](https://www.microsoft.com/download/details.aspx?id=40855)であること。
    - [.NET Framework 4.5 がインストール済み](https://www.microsoft.com/download/details.aspx?id=30653)であること。

## <a name="unpack-your-disks"></a>ディスクを開梱する

 次の手順に従ってディスクを開梱します。

1. Data Box Disk は、小さな梱包箱に入って郵送されます。 箱を開けて内容物を取り出します。 1 台から 5 台の SSD (Solid State Disk) と各ディスクにつき 1 本の USB 接続ケーブルが同梱されていることを確認してください。 箱を調べて、改ざんの痕跡や、その他明らかな損傷がないか確認します。 

    ![Data Box Disk の発送パッケージ](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 梱包箱が改ざんされていたり、ひどく破損している場合は、箱を開けないでください。 Microsoft サポートに問い合わせて、ディスクが正常に使用できる状態にあるかどうか、また交換ディスクを発送してもらう必要があるかどうかを評価してください。
3. 返送用の配送先住所ラベルが入った透明な袋が (現在のラベルの下に) 同梱されていることを確認します。 このラベルが紛失するか破損した場合は、いつでも Azure portal から新しいラベルをダウンロードして印刷することができます。 

    ![Data Box Disk の配送先住所ラベル](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 箱と梱包材は、ディスクを返送するときのために保管しておいてください。

## <a name="connect-and-unlock-your-disks"></a>ディスクを接続してロックを解除する

ディスクを接続し、ロックを解除するには、次の手順を実行します。

1. 前提条件に記載したサポート対象 OS が実行されている Windows コンピューターに、同梱されているケーブルを使用してディスクを接続します。 

    ![Data Box Disk の接続](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Azure portal で **[全般] > [デバイスの詳細]** に移動します。 
3. **[Download Data Box Disk unlock tool]\(Data Box Disk ロック解除ツールのダウンロード\)** をクリックします。 

    ![Data Box Disk ロック解除ツールのダウンロード](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. データのコピーに使用するコンピューターにこのツールを抽出します。
5. 同じコンピューターで、管理者として Windows PowerShell を実行するかコマンド プロンプト ウィンドウを開きます。
6. (省略可) ディスクのロック解除に使用しているコンピューターがオペレーティング システムの要件を満たしていることを確認するために、システム チェック コマンドを実行します。 サンプル出力を次に示します。 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Azure portal で **[全般] > [デバイスの詳細]** に移動します。 コピー アイコンを使用してパスキーをコピーします。
8. `DataBoxDiskUnlock.exe` を実行してパスキーを指定します。 ディスクに割り当てられたドライブ文字が表示されます。 サンプル出力を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. 残りのディスクについても、それぞれ接続し直しながら、手順 6. から手順 8. を繰り返します。 Data Box Disk ロック解除ツールでわからないことがある場合は、help コマンドを使用してください。   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. ディスクのロックが解除されたら、ディスクの内容を表示することができます。    

    ![Data Box Disk の内容](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Disk を開梱する
> * Data Box Disk を接続してロックを解除する


次のチュートリアルに進み、Data Box Disk にデータをコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [データを Data Box Disk にコピーする](./data-box-disk-deploy-copy-data.md)

