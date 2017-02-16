---
title: "Azure の FreeBSD の概要 | Microsoft Docs"
description: "Azure での FreeBSD 仮想マシンの使用について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 71ad04b10bc49500197db6fecdcc0305a1ea0dd2
ms.openlocfilehash: ecb1c385de6c1b12674326afe7d5a0ebf6cd9ad0


---
# <a name="introduction-to-freebsd-on-azure"></a>Azure の FreeBSD の概要
このトピックでは、Azure での FreeBSD 仮想マシンの実行の概要を説明します。

## <a name="overview"></a>Overview
Microsoft Azure の FreeBSD は、最新のサーバー、デスクトップ、組み込みプラットフォームを強化するために使用される先進のコンピューター オペレーティング システムです。

Microsoft Corporation では、Azure で利用可能な、[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) を事前構成した FreeBSD を用意しています。 現時点では、次のバージョンの FreeBSD がイメージとしてマイクロソフトから提供されています。

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

エージェントは、初回使用時の VM のプロビジョニング (ユーザー名、パスワード、SSH キー、ホスト名など) や、選択的な VM 拡張機能の有効化などの操作で、FreeBSD VM と Azure ファブリック間の通信を担います。

FreeBSD の今後のバージョンについては、最新の機能に対応し、FreeBSD リリース エンジニアリング チームが最新版を公開したらすぐに提供することを目指しています。

## <a name="deploying-a-freebsd-virtual-machine"></a>FreeBSD 仮想マシンのデプロイ
FreeBSD 仮想マシンのデプロイは、Azure Marketplace で提供されるイメージを使用した簡単なプロセスです。

- [Azure Marketplace の FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [Azure Marketplace の FreeBSD 11.0](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

## <a name="vm-extensions-for-freebsd"></a>FreeBSD の VM 拡張機能
FreeBSD でサポートされている VM 拡張機能を以下に示します。

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 拡張機能では次のことが可能です。

* 元の sudo ユーザーのパスワードをリセットする。
* パスワードを指定して新しい sudo ユーザーを作成する。
* 指定したキーで公開ホスト キーを設定する。
* ホスト キーが指定されていない場合、VM のプロビジョニング時に指定された公開ホスト キーをリセットする。
* reset_ssh が true に設定されている場合に、SSH ポート (22) を開き、sshd_config を復元する。
* 既存のユーザーを削除する。
* ディスクをチェックする。
* 追加されたディスクを修復する。

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 拡張機能では次のことが可能です。

* 指定した場合、Azure Storage または外部パブリック ストレージ (GitHub など) からカスタマイズされたスクリプトをダウンロードする。
* エントリ ポイント スクリプトを実行する。
* インライン コマンドをサポートする。
* シェル スクリプトと Python スクリプトで Windows 形式の改行を自動的に変換する。
* シェル スクリプトと Python スクリプトで BOM を自動的に削除する。
* CommandToExecute で機密データを保護する。

[!NOTE]FreeBSD VM は、現在のところ CustomScript バージョン 1.x のみをサポートしています。  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>認証: ユーザー名、パスワード、SSH キー
Azure ポータルを使用して FreeBSD 仮想マシンを作成するときに、ユーザー名、パスワード、または SSH 公開キーを入力する必要があります。
Azure に FreeBSD 仮想マシンをデプロイするためのユーザー名は、仮想マシン ("root" など) に既に存在するシステム アカウント (UID は&100; 未満) の名前とは同じにしないでください。
現在サポートされているのは、RSA SSH キーだけです。 複数行の SSH キーは、`---- BEGIN SSH2 PUBLIC KEY ----`で始まり、`---- END SSH2 PUBLIC KEY ----`で終わる必要があります。

## <a name="obtaining-superuser-privileges"></a>スーパーユーザー権限の取得
Azure での仮想マシン インスタンスをデプロイする際に指定したユーザー アカウントは、特権を持つアカウントです。 公開済みの FreeBSD イメージには、sudo のパッケージがインストールされています。
このユーザー アカウントを使用してログインすると、コマンド構文を使用して、root としてコマンドを実行できます。

    $ sudo <COMMAND>

`sudo -s` を使用して root シェルを取得することもできます。

## <a name="known-issues"></a>既知の問題
1. 現在、Hyper-V (および Azure) の FreeBSD 11.0 には、`freebsd-update` を使用してオペレーティング システムに修正プログラムを適用すると、VM の起動が失敗する場合があるという問題があります。 [提案された修正プログラム](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=212721)は、Azure Marketplace の FreeBSD イメージに含まれています。 ただし、FreeBSD チームによってアップストリームにマージされてはいないので、`freebsd-update` を実行すると、カーネルが未修正のものに置き換えられます。 修正プログラムが ERRATA として公開されるまで、Azure のユーザーは FreeBSD 11.0 の修正プログラムをインストールしないことをお勧めします。

2. [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) バージョン 2.2.2 については、Azure 上の FreeBSD VM に対してプロビジョニング エラーが発生するという [既知の問題] (https://github.com/Azure/WALinuxAgent/pull/517) が確認されています。 Azure 上で FreeBSD VM を使用するお客様には 2.2.1 か、またはそれ以前のリリースを使用することをお勧めします。 修正プログラムは、[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) バージョン 2.2.3 によってキャプチャされる予定です。 

## <a name="next-steps"></a>次のステップ
* [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) に移動して、FreeBSD VM を作成します。
* 独自の FreeBSD を Azure で使用する場合は、「[FreeBSD VHD の作成と Azure へのアップロード](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md)」をご覧ください。



<!--HONumber=Jan17_HO2-->


