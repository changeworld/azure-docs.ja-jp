<properties
   pageTitle="Azure の FreeBSD の概要 | Microsoft Azure"
   description="Azure での FreeBSD 仮想マシンの使用について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# Azure の FreeBSD の概要
このトピックでは、Azure での FreeBSD 仮想マシンの実行の概要を説明します。

## Overview
Microsoft Azure の FreeBSD は、最新のサーバー、デスクトップ、組み込みプラットフォームを強化するために使用される先進のコンピューター オペレーティング システムです。Microsoft Corporation が提供する FreeBSD 10.3 イメージは、Azure で使用でき、FreeBSD 10.3 リリースに基づいています。インストールされているのは、Azure VM Guest Agent [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) で、エージェントは、初回使用時の VM のプロビジョニング (ユーザー名、パスワード、ホスト名など) や、選択的な VM 拡張機能の有効化などの操作で、FreeBSD VM と Azure ファブリック間の通信を担います。FreeBSD の今後のバージョンについては、最新の機能に対応し、FreeBSD リリース エンジニアリング チームが最新版をリリースしたらすぐに提供することを目指しています。次期リリースは [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html) です。

## FreeBSD 仮想マシンのデプロイ
FreeBSD 仮想マシンのデプロイは、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) で提供されるイメージを使用した簡単なプロセスです。

## FreeBSD の VM 拡張機能
FreeBSD でサポートされている VM 拡張機能を以下に示します。

### VMAccess

[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 拡張機能では次のことが可能です。

- 元の sudo ユーザーのパスワードをリセットする。
- パスワードを指定して新しい sudo ユーザーを作成する。
- 指定したキーで公開ホスト キーを設定する。
- ホスト キーが指定されていない場合、VM のプロビジョニング時に指定された公開ホスト キーをリセットする。
- reset\_ssh が true に設定されている場合に、SSH ポート (22) を開き、sshd\_config を復元する。
- 既存のユーザーを削除する。
- ディスクをチェックする。
- 追加されたディスクを修復する。

### CustomScript

[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 拡張機能では次のことが可能です。

- 指定した場合、Azure Storage または外部パブリック ストレージ (GitHub など) からカスタマイズされたスクリプトをダウンロードする。
- エントリ ポイント スクリプトを実行する。
- インライン コマンドをサポートする。
- シェル スクリプトと Python スクリプトで Windows 形式の改行を自動的に変換する。
- シェル スクリプトと Python スクリプトで BOM を自動的に削除する。
- CommandToExecute で機密データを保護する。

## 認証: ユーザー名、パスワード、SSH キー
Azure ポータルを使用して FreeBSD 仮想マシンを作成するときに、ユーザー名、パスワード、または SSH 公開キーを入力する必要があります。Azure に FreeBSD 仮想マシンをデプロイするためのユーザー名は、仮想マシン ("root" など) に既に存在するシステム アカウント (UID は 100 未満) の名前とは同じにしないでください。現在サポートされているのは、RSA SSH キーだけです。複数行の SSH キーは、"---- BEGIN SSH2 PUBLIC KEY ----" で始まり、"---- END SSH2 PUBLIC KEY ----" で終わる必要があります。

## スーパーユーザー権限の取得
Azure での仮想マシン インスタンスをデプロイする際に指定したユーザー アカウントは、特権を持つアカウントです。公開済みの FreeBSD イメージには、sudo のパッケージがインストールされています。このユーザー アカウントを使用してログインすると、コマンド構文を使用して、root としてコマンドを実行できます。

    # sudo <COMMAND>

sudo -s を使用して root シェルを取得することもできます。

## 次のステップ
- [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) に移動して、FreeBSD VM を作成します。
- 独自の FreeBSD を Azure で使用する場合は、「[FreeBSD VHD の作成と Azure へのアップロード](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md)」をご覧ください。

<!---HONumber=AcomDC_0914_2016-->