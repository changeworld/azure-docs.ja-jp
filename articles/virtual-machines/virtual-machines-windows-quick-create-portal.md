---
title: "Azure クイック スタート - ポータルで Windows VM を作成する | Microsoft Docs"
description: "Azure クイック スタート - ポータルで Windows VM を作成する"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 56e9deea4e070a99e47b7de7c5686526a4528fb6
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Azure Portal で Windows 仮想マシンを作成する

Azure 仮想マシンは、Azure Portal で作成できます。 この方法では、ブラウザーベースのユーザー インターフェイスで VM と関連するすべての Azure リソースを作成および構成できます。

## <a name="log-in-to-azure"></a>Azure へのログイン 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-virtual-machine"></a>仮想マシンの作成

2. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

3. **[新規]** ブレードの **[Compute]** を選択し、**[Compute]** ブレードの **[Windows Server 2016 Datacenter]** を選択して、**[作成]** ボタンをクリックします。

4. 仮想マシンの **[基本]** フォームに入力します。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 完了したら、**[OK]** をクリックします。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. VM のサイズを選択し、**[選択]** をクリックします。 

6. 設定ブレードの **[管理ディスクを使用]** で **[はい]** を選択し、他の設定は既定のままにして、**[OK]** をクリックします。

7. 概要ページで **[OK]** をクリックして、仮想マシンのデプロイを開始します。

8. デプロイの状態を監視するには、仮想マシンをクリックします。 VM は、Azure Portal ダッシュボードに表示されます。または、左側のメニューで **[Virtual Machines]** を選択すると表示されます。 VM が作成されると、状態は **[デプロイ中]** から **[実行中]** に変わります。

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

1. 仮想マシンのブレードで、**[接続]** ボタンをクリックします。 リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成され、ダウンロードされます。このファイルは、自分のマシンに接続するためのショートカットのように使用できます。 このファイルを開いて VM に接続します。

    ![ポータル 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. リモート デスクトップ接続のウィンドウで **[接続]** をクリックします。

    ![ポータル 10](./media/virtual-machine-quick-start/portal-quick-start-10.png) 

4. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

5. 証明書に関する警告が表示される場合があります。接続を続行するには、**[はい]** をクリックします。

## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、仮想マシン ブレードでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

[ロールのインストールとファイアウォールの構成のチュートリアル](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[VM デプロイ CLI サンプルを探索する](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
