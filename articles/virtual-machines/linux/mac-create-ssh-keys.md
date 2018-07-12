---
title: Azure での Linux VM 用の SSH キー ペアの作成と使用 | Microsoft Docs
description: Azure 内に Linux VM 用の SSH 公開キーと秘密キーのペアを作成し、認証プロセスのセキュリティを向上させる方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928456"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>簡単な手順: Azure 内に Linux VM 用の SSH 公開/秘密キーのペアを作成して使用する
Secure Shell (SSH) キー ペアを使用すると、認証に SSH キーを使う仮想マシン (VM) を Azure に作成でき、ログインするためのパスワードが不要になります。 この記事では、Linux VM 用の SSH 公開キー ファイルと秘密キー ファイルのペアを短時間で生成して使用する方法について説明します。 これらの手順は、Azure Cloud Shell、macOS または Linux ホスト、Windows Subsystem for Linux、および OpenSSH をサポートするその他のツールを使用して完了できます。 

詳しい背景と例については、[SSH キー ペアを作成するための詳細な手順](create-ssh-keys-detailed.md)に関するページを参照してください。

Windows コンピューター上で、SSH キーを生成して使用するその他の方法については、「[Azure 上の Windows で SSH キーを使用する方法](ssh-from-windows.md)」を参照してください。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成
`ssh-keygen` コマンドを使用して、SSH 公開キー ファイルと秘密キー ファイルを生成します。これらのファイルは、既定では `~/.ssh` ディレクトリに作成されます。 入力を求められたときに、別の場所を指定したり、追加のパスフレーズ (秘密キー ファイルにアクセスするためのパスワード) を指定したりできます。 SSH キー ペアが現在の場所にある場合、それらのファイルは上書きされます。

```bash
ssh-keygen -t rsa -b 2048
```

[Azure CLI 2.0](/cli/azure) を使用して VM を作成する場合は、必要に応じて [az vm create](/cli/azure/vm#az_vm_create) コマンドを `--generate-ssh-keys` オプション付きで実行することで、SSH 公開キー ファイルと秘密キー ファイルを作成できます。 キーは、~/.ssh ディレクトリに格納されます。 このコマンド オプションを指定すると、その場所にキーが既に存在している場合でも、キーが上書きされることはありません。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM のデプロイ時に SSH 公開キーを提供する
認証するために SSH キーを使用する Linux VM を作成するには、Azure Portal、CLI、Resource Manager テンプレート、またはその他の方法を使用して VM を作成するときに SSH 公開キーを指定します。

* [Azure Portal で Linux 仮想マシンを作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI で Linux 仮想マシンを作成する](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure テンプレートを使用して Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

SSH 公開キーの書式がわからない場合は、次のように `cat` を実行して公開キーを表示できます。`~/.ssh/id_rsa.pub` の部分は実際の公開キー ファイルの場所に置き換えてください。

```bash
cat ~/.ssh/id_rsa.pub
```

公開キー ファイルの内容をコピーし、Azure Portal または Resource Manager テンプレートに貼り付けて使用する場合は、余分な空白スペースをコピーしないように注意してください。 たとえば、macOS を使用している場合は、公開キー ファイル (既定では `~/.ssh/id_rsa.pub`) を **pbcopy** にパイプして、内容をコピーできます (**xclip** など、同じ目的を達成できる Linux プログラムが他にもあります)。

キーの作成時に場所を変更していない限り、Azure 内の Linux VM 上に配置した公開キーは、既定で `~/.ssh/id_rsa.pub` に格納されます。 [Azure CLI 2.0](/cli/azure) で既存の公開キーを使用する VM を作成する場合は、[az vm create](/cli/azure/vm#az_vm_create) コマンドを `--ssh-key-value` オプション付きで実行することで、使用する公開キーの値または場所を指定します。 

## <a name="ssh-to-your-vm"></a>VM に SSH 接続する
公開キーを Azure VM に、秘密キーをローカル システム上に配置した状態で、VM の IP アドレスまたは DNS 名を使用して、VM に SSH 接続します。 次のコマンドの *azureuser* と *myvm.westus.cloudapp.azure.com* を、管理者のユーザー名と完全修飾ドメイン名 (または IP アドレス) に置き換えてください。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

キー ペアを作成する際にパスフレーズを指定した場合は、ログイン プロセス中に入力を求められたら、そのパスフレーズを入力します  (サーバーは `~/.ssh/known_hosts` フォルダーに追加されます。Azure VM にある公開キーが変更されるかサーバー名が `~/.ssh/known_hosts` から削除されるまで、再度接続を求められることはありません)。

SSH キーを使用して作成された VM は、既定ではパスワードが無効にされます。この措置により、推測によるブルート フォース攻撃はコストが非常に高くつき、実行するのが難しくなります。 

## <a name="next-steps"></a>次の手順

この記事では、すぐに使えるように単純な SSH キー ペアを作成する方法について説明しました。 

* SSH キー ペアの操作に関する関する詳しい説明については、[SSH キー ペアと証明書を作成するための詳細な手順](create-ssh-keys-detailed.md)に関するページを参照してください。

* Azure VM への SSH 接続に問題がある場合は、[Azure Linux VM への SSH 接続に関するトラブルシューティング](troubleshoot-ssh-connection.md)に関するページを参照してください。


