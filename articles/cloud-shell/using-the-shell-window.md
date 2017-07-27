---
title: "Azure Cloud Shell (プレビュー) の使用 | Microsoft Docs"
description: "Azure Cloud Shell ウィンドウのチュートリアル。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 024f2ea5ba574444bc096d655a8aa9f313f7f2fe
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Azure Cloud Shell ウィンドウの使用

ここでは、Azure Cloud Shell ウィンドウの使用方法について説明します。

## <a name="concurrent-sessions"></a>同時セッション数
Cloud Shell では、各セッションが別の Bash プロセスとして存在できるようにすることで、ブラウザー タブで複数の同時セッションを行うことができます。
セッションを終了する場合は、必ず各セッション ウィンドウを終了してください。各プロセスは同一マシン上で実行していますが、それぞれが独立しているためです。

## <a name="restart-cloud-shell"></a>Cloud Shell の再起動
![](media/recycle.png)
> [!WARNING]
> Cloud Shell を再起動すると、マシンの状態がリセットされ、ファイル共有に保持されていないファイルはすべて失われます。

* ツールバーの再起動アイコンをクリックして、Cloud Shell マシンをリセットします。

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell ウィンドウの最小化と最大化
![](media/minmax.png)
* ウィンドウの右上の最小化アイコンをクリックすると、ウィンドウが非表示になります。 Cloud Shell アイコンをクリックすると再び表示されます。
* 最大化アイコンをクリックすると、ウィンドウの高さが最大に設定されます。 ウィンドウを元のサイズに戻すには、[復元] をクリックします。

## <a name="copy-and-paste"></a>コピーと貼り付け
* Windows: `Ctrl-insert` と `Shift-insert` または右クリックのドロップダウンを使用して、コピーと貼り付けを行います。
  * FireFox や IE ではクリップボードのアクセス許可が正しくサポートされない場合があります。
* Mac OS: `Cmd-c` と `Cmd-v` または右クリックのドロップダウンを使用して、コピーと貼り付けを行います。

## <a name="resize-cloud-shell-window"></a>Cloud Shell ウィンドウのサイズ変更
* ツールバーの上端をクリックしてから、上下にドラッグして Cloud Shell ウィンドウのサイズを変更します。

## <a name="scrolling-text-display"></a>テキスト表示のスクロール
* マウスまたはタッチパッドを使用してスクロールします。

## <a name="exit-command"></a>exit コマンド
`exit` を実行すると、アクティブなセッションが終了します。 この動作は既定では 10 分後に発生します (操作が行われない場合)。

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md)  
