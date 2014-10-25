<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Injecting Custom Data into Azure Virtual Machines" description="This topic describes how to inject custom data into an Azure Virtual Machine when the instance is created and how to locate the custom data on either Windows or Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="dongill" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="rasquill"></tags>

# Azure の仮想マシンにカスタム データを挿入する

Azure の仮想マシンをプロビジョニングしているときに、スクリプトなどのデータをその中に挿入する場合がよくあります。これは、オペレーティング システムが Microsoft Windows であっても Linux ディストリビューションであっても同じです。このトピックでは、次の操作について説明します。

-   Azure の仮想マシンをプロビジョニングしているときに、スクリプトやデータを挿入する。

-   Windows および Linux 用にデータを取得する。

-   いくつかのシステムで利用できる特殊なツールを使用して、自動的にカスタム データを検出し、操作する。

> [WACOM.NOTE] このトピックは、この機能に対する [Azure ブログへの投稿記事][Azure ブログへの投稿記事]をさらに詳しく説明するものです。新しい機能の登場によりこのトピックも更新されます。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

このトピックの内容:

-   [Azure 仮想マシンにカスタム データを挿入する][Azure 仮想マシンにカスタム データを挿入する]

-   [仮想マシンでカスタム データを使用する][仮想マシンでカスタム データを使用する]

-   [次のステップ][次のステップ]

## <span id="injectingCustomData"></span></a>Azure 仮想マシンにカスタム データを挿入する

この機能は、現在、[Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス][Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス]でのみサポートされています。`azure vm create` コマンドのオプションはすべて使用できますが、以下の方法は、きわめて基本的な方法の 1 つです。

        PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
        VMNAME=mycustomdataubuntu
        USERNAME=username
        VMIMAGE= An image chosen from among those listed by azure vm image list
        azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22

## <span id="usingCustomData"></span></a>仮想マシンでカスタム データを使用する

-   Azure 仮想マシンが Windows 仮想マシンの場合には、カスタム データのファイルは `%SYSTEMDRIVE%\AzureData\CustomData.bin` に保存されます。このファイルは、ローカル コンピューターから新しい仮想マシンに転送するために Base64 でエンコードされますが、自動的にデコードされるため、直ちに開いて使用できます。

> [WACOM.NOTE] このファイルが既に存在している場合には、上書きされます。ディレクトリのセキュリティは、**"システム: フル コントロール"** および **"管理者: フル コントロール"** に設定されます。

-   Azure の仮想マシンが Linux 仮想マシンの場合、カスタム データ ファイルは次の 2 つの場所に配置されます。ただし、データは Base64 でエンコードされるため、最初にデータをデコードする必要があります。

    -   `/var/lib/waagent/ovf-env.xml`
    -   `/var/lib/waagent/CustomData`

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <span id="nextsteps"></span></a>次の手順:cloud-init を使用する

Azure の仮想マシンが Ubuntu イメージの場合には、cloud-init を使用することにより、カスタム データを自動的に使用するスクリプトを起動できます (カスタム データ ファイルがスクリプトの場合には、スクリプトを実行します)。詳細については、[「cloud-init documentation for Ubuntu (Ubuntu 用 cloud-init のドキュメント)」][「cloud-init documentation for Ubuntu (Ubuntu 用 cloud-init のドキュメント)」]を参照してください。

<!--Link references-->

[ロールの追加][ロールの追加]

[Microsoft Azure Cross-Platform Command-line Interface (Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス)][Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス]

  [Azure ブログへの投稿記事]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Azure 仮想マシンにカスタム データを挿入する]: #injectingCustomData
  [仮想マシンでカスタム データを使用する]: #usingCustomData
  [次のステップ]: #nextsteps
  [Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス]: https://github.com/Azure/azure-sdk-tools-xplat
  [「cloud-init documentation for Ubuntu (Ubuntu 用 cloud-init のドキュメント)」]: https://help.ubuntu.com/community/CloudInit
  [ロールの追加]: http://msdn.microsoft.com/library/azure/jj157186.aspx
