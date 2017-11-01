---
title: "Azure Active Directory Domain Services: CentOS VM の管理対象ドメインへの参加 | Microsoft Docs"
description: "CentOS Linux 仮想マシンを Azure AD Domain Services に参加させる"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 0316d40abc6cf71d5f9218346fa6543c9167eaa4
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>CentOS Linux 仮想マシンを管理対象ドメインに参加させる
この記事では、Azure 内の CentOS Linux 仮想マシンを Azure AD Domain Services の管理対象ドメインに参加させる方法について説明します。

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。
1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。
4. 管理対象ドメインの IP アドレスを、必ず仮想ネットワークの DNS サーバーとして構成します。 詳しくは、[Azure 仮想ネットワークの DNS 設定を更新する方法](active-directory-ds-getting-started-dns.md)に関するページをご覧ください。
5. [Azure AD Domain Services の管理対象ドメインとのパスワードの同期](active-directory-ds-getting-started-password-sync.md)に必要な手順をすべて実行します。


## <a name="provision-a-centos-linux-virtual-machine"></a>CentOS Linux 仮想マシンをプロビジョニングする
次のいずれかの方法を使用して、Azure で CentOS 仮想マシンをプロビジョニングします。
* [Azure ポータル](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * **Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク**に、仮想マシンをデプロイします。
> * Azure AD Domain Services を有効にしたサブネットとは**異なるサブネット**を選択します。
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>新しくプロビジョニングされた Linux 仮想マシンへのリモート接続
CentOS 仮想マシンが Azure でプロビジョニングされました。 次は、VM のプロビジョニング中に作成したローカル管理者アカウントを使用して、仮想マシンにリモートで接続します。

[Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従ってください。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 仮想マシン上の hosts ファイルを構成する
SSH ターミナルで /etc/hosts ファイルを編集し、ご自分のマシンの IP アドレスとホスト名を更新します。

```
sudo vi /etc/hosts
```

hosts ファイルに、次の値を入力します。

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
ここで、"contoso100.com" は、管理対象ドメインの DNS ドメイン名です。 "contoso-centos" は、管理対象ドメインに参加させる CentOS 仮想マシンのホスト名です。


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 仮想マシンに必要なパッケージのインストール
次は、仮想マシンでのドメイン参加に必要なパッケージをインストールします。 SSH ターミナルで、次のコマンドを入力して、必要なパッケージをインストールします。

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 仮想マシンの管理対象ドメインへの参加
Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンを管理対象ドメインに参加させます。

1. AAD ドメイン サービスの管理対象ドメインを探します。 SSH ターミナルで、次のコマンドを入力します。

    ```
    sudo realm discover CONTOSO100.COM
    ```

      > [!NOTE]
      > **トラブルシューティング:** *realm discover* で管理対象ドメインが見つからない場合:
        * Ensure that the domain is reachable from the virtual machine (try ping).
        * Check that the virtual machine has indeed been deployed to the same virtual network in which the managed domain is available.
        * Check to see if you have updated the DNS server settings for the virtual network to point to the domain controllers of the managed domain.
      >

2. Kerberos を初期化します。 SSH ターミナルで、次のコマンドを入力します。

    > [!TIP]
    > * "AAD DC 管理者" グループに所属するユーザーを指定します。
    > * kinit のエラーを防ぐため、ドメイン名は必ず大文字で指定します。
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. コンピューターをドメインに参加させます。 SSH ターミナルで、次のコマンドを入力します。

    > [!TIP]
    > 前の手順で指定したユーザー アカウントを使用します ("kinit")。
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

コンピューターの管理対象ドメインへの参加が完了すると、「Successfully enrolled machine in realm (コンピューターは領域に正常に登録されました)」という旨のメッセージが表示されます。


## <a name="verify-domain-join"></a>ドメイン参加の確認
マシンが管理対象ドメインに正常に参加したかどうかを確認してみましょう。 別の SSH 接続を使用して、ドメインに参加した CentOS VM に接続します。 ドメイン ユーザー アカウントを使用して、そのユーザー アカウントが正しく解決されているかどうかを確認します。

1. SSH ターミナルで次のコマンドを入力し、SSH を使用して、ドメインに参加した CentOS 仮想マシンに接続します。 管理対象ドメインに属するドメイン アカウントを使用します (例: ここでは 'bob@CONTOSO100.COM')。
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. SSH ターミナルで次のコマンドを入力し、ホーム ディレクトリが正しく初期化されているかどうかを確認します。
    ```
    pwd
    ```

3. SSH ターミナルで次のコマンドを入力し、グループ メンバーシップが正しく解決されているかどうかを確認します。
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
「 [Troubleshooting domain join (ドメイン参加のトラブルシューティング)](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join) 」を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Installing Kerberos (Kerberos のインストール)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Windows 統合ガイド)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
