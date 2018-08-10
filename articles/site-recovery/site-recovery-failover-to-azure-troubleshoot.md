---
title: Azure へのフェールオーバー障害のトラブルシューティング | Microsoft Docs
description: この記事では、Azure へのフェールオーバー時の一般的なエラーをトラブルシューティングする方法を説明します。
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443386"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>仮想マシンから Azure へのフェールオーバー時のエラーをトラブルシューティングする

仮想マシンから Azure へフェールオーバーを実行中に、次のいずれかのエラーを受け取る可能性があります。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。

## <a name="failover-failed-with-error-id-28031"></a>エラー ID 28031 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想マシンの作成に使用できる十分なクォータがありません。[サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](http://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。

* 同じ可用性セットにある異なるサイズ ファミリの仮想マシンのフェールオーバーを試行しています。 同じ可用性セットにあるすべての仮想マシンに対しては、必ず同じサイズ ファミリを選択してください。 仮想マシンの [コンピューティングとネットワーク] の設定に移動してサイズを変更し、フェールオーバーを再試行してください。

* サブスクリプションに、仮想マシンの作成を禁止しているポリシーがあります。 仮想マシンの作成を許可するようにポリシーを変更して、フェールオーバーを再試行してください。

## <a name="failover-failed-with-error-id-28092"></a>エラー ID 28092 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーされた仮想マシンのネットワーク インターフェイスを作成できませんでした。 サブスクリプションでネットワーク インターフェイスを作成するために使用できる十分なクォータがあることを確認してください。 [サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](http://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。 十分なクォータがある場合、この現象は一時的に発生している可能性があります。もう一度、操作をやり直してください。 再試行しても問題が続く場合、この文書の最後にコメントを残してください。  

## <a name="failover-failed-with-error-id-70038"></a>エラー ID 70038 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした従来の仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想ネットワークなど、仮想マシンの作成に必要ないずれかのリソースが存在しない。 仮想マシンの [コンピューティングとネットワーク] の設定に指定されているように仮想ネットワークを作成するか、既に存在している仮想ネットワークの設定を変更して、フェールオーバーを再試行してください。

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>仮想マシンで [接続] ボタンが灰色表示されているために、/RDP/SSH をフェールオーバーされた仮想マシンに接続できない

[接続] ボタンが灰色表示され、Express Route またはサイト間 VPN 接続を使用して Azure に接続されない場合、次の操作を実行します。

1. **[仮想マシン]** > **[ネットワーク]** に移動し、必要なネットワーク インターフェイスの名前をクリックします。  ![ネットワーク インターフェイス](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. **[IP 構成]** に移動し、必要な IP 構成の名前フィールドをクリックします。 ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. パブリック IP アドレスを有効にするには、**[有効にする]** をクリックします。 ![IP の有効化](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. **[必要な設定の構成]** > **[新規作成]** をクリックします。 ![新規作成](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. パブリック アドレスの名前を入力し、**[SKU]** と **[割り当て]** の既定のオプションを選択し、**[OK]** をクリックします。
1. **[保存]** をクリックして変更を保存します。
1. パネルを閉じ、仮想マシンの **[概要]** セクションに移動して、/RDP に接続します。

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>仮想マシンで [接続] ボタンが使用可能でも (灰色表示されていない)、/RDP/SSH をフェールオーバーされた仮想マシンに接続できない

仮想マシンの **[ブート診断]** を確認し、この記事に記載されているエラーを確認します。

1. 仮想マシンが起動されていない場合は、前の復旧ポイントにフェールオーバーしてみます
1. 仮想マシン内のアプリケーションが開始されていない場合は、アプリケーションと整合性がとれた復旧ポイントにフェールオーバーしてみます
1. 仮想マシンがドメインに参加している場合は、ドメイン コントローラーが適切に機能していることを確認します。 そのためには、以下の手順に従います。
    a. 同じネットワークに新しい仮想マシンを作成します。

    b.  フェールオーバーされた仮想マシンが開始されると予想される同じドメインに参加できることを確認します。

    c. ドメイン コントローラーが適切に機能**していない**場合は、ローカル管理者アカウントを使用して、フェールオーバーされた仮想マシンにログインしてみます
1. カスタム DNS サーバーを使用している場合は、そのサーバーにアクセスできることを確認します。 そのためには、以下の手順に従います。
    a. 同じネットワークに新しい仮想マシンを作成します。b. カスタムの DNS サーバーを使用して仮想マシンが名前を解決できるかどうかを確認します。

>[!Note]
>ブート診断以外の設定を有効にした場合は、フェールオーバーの前に Azure VM エージェントを仮想マシンにインストールする必要があります。

## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、[Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)にクエリを投稿するか、この文書の最後にコメントを残してください。 ユーザー支援を可能にするために必要なアクティブ コミュニティを設置しています。
