---
title: Azure VM を使用して Azure Arc 対応サーバーを評価する方法
description: Azure 仮想マシンを使用して Azure Arc 対応サーバーを評価する方法について説明します。
ms.date: 10/01/2021
ms.topic: conceptual
ms.openlocfilehash: 54656e0701857fd3badbcec619b2185917935857
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389819"
---
# <a name="evaluate-azure-arc-enabled-servers-on-an-azure-virtual-machine"></a>Azure 仮想マシンで Azure Arc 対応サーバーを評価する

Azure Arc 対応サーバーは、オンプレミスまたは他のクラウドで実行されているサーバーの Azure への接続に役立つように設計されています。 通常、Azure 仮想マシンでは Azure Arc 対応サーバーを使用しません。それは、Azure Resource Manager での VM の表現、VM 拡張機能、マネージド ID、Azure Policy を含め、同じ機能のすべてをこれらの VM でネイティブに使用できるためです。 Azure Arc 対応サーバーを Azure VM にインストールしようとすると、それがサポートされていないことを示すエラー メッセージが表示され、エージェントのインストールはキャンセルされます。

運用環境シナリオでは Azure Arc 対応サーバーを Azure VM にインストールできませんが、"*評価とテストだけを目的として*" Azure VM で実行されるように Azure Arc 対応サーバーを構成することは可能です。 この記事は、Azure VM を、後でそこで Azure Arc 対応サーバーを有効にできるように設定しておくために役立ちます。

> [!NOTE]
> この記事の手順は、Azure クラウドでホストされている仮想マシンを対象としています。 Azure Arc 対応サーバーは、Azure Stack Hub または Azure Stack Edge で実行されている仮想マシンではサポートされていません。

## <a name="prerequisites"></a>前提条件

* お使いのアカウントが、[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールに割り当てられている。
* Azure 仮想マシンが、[Azure Arc 対応サーバーでサポートされているオペレーティング システム](agent-overview.md#supported-operating-systems)を実行している。 Azure VM がない場合は、[単純な Windows VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) または[単純な Ubuntu Linux 18.04 LTS VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) をデプロイできます。
* Windows の場合は [Microsoft ダウンロード センター](https://aka.ms/AzureConnectedMachineAgent)から、Linux の場合は Microsoft [パッケージ リポジトリ](https://packages.microsoft.com/)から Azure Connected Machine エージェント パッケージをダウンロードするために、Azure VM から送信方向に通信できる。 IT セキュリティ ポリシーに従ってインターネットへの送信接続が制限されている場合は、エージェント パッケージを手動でダウンロードし、Azure VM 上のフォルダーにコピーする必要があります。
* VM に対する昇格された (つまり、管理者または root) 権限と、VM への RDP または SSH によるアクセス。
* Azure VM を Azure Arc 対応サーバーに登録して管理するには、リソース グループ内の [Azure Connected Machine リソース管理者](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator)または[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールのメンバーであること。

## <a name="plan"></a>プラン

Azure Arc 対応サーバーとしての Azure VM の管理を開始するには、後で Azure Arc 対応サーバーをインストールして構成できるように、Azure VM に次の変更を加えておく必要があります。

1. Azure VM にデプロイされている VM 拡張機能 (Log Analytics エージェントなど) を削除します。 Azure Arc 対応サーバーでは Azure VM と同じ拡張機能の多くがサポートされますが、Azure Arc 対応サーバー エージェントは、既に VM にデプロイされている VM 拡張機能を管理できません。

2. Azure Windows または Linux ゲスト エージェントを無効にします。 Azure VM ゲスト エージェントが Azure Arc 対応サーバー Connected Machine エージェントに対して同様の目的を果たします。 2 者の間の競合を回避するために、Azure VM エージェントを無効にする必要があります。 いったんこれを無効にしたら、VM 拡張機能や一部の Azure サービスは使用できません。

3. Azure Instance Metadata Service (IMDS) へのアクセスを拒否するセキュリティ規則を作成します。 IMDS は、リソース ID や場所など、Azure での VM の表現に関する情報を取得するためにアプリケーションから呼び出すことができる REST API です。 IMDS により、マシンに割り当てられているマネージド ID へのアクセスも提供されます。 Azure Arc 対応サーバーは独自の IMDS 実装を提供し、VM の Azure Arc 表現に関する情報を返します。 両方の IMDS エンドポイントが使用できるためにアプリがこの 2 つのいずれかを選択しなければならない状況を回避するために、Azure VM IMDS へのアクセスをブロックして、Azure Arc 対応サーバーの IMDS 実装だけが使用可能になるようにします。

これらの変更を加えると、Azure VM は Azure の外部にあるすべてのマシンまたはサーバーと同様に動作し、Azure Arc 対応サーバーをインストールして評価するために必要な開始点に位置しています。

Azure Arc 対応サーバーが VM 上に構成されると、Azure にその 2 つの表現が表示されます。 1 つはリソースの種類が `Microsoft.Compute/virtualMachines` である Azure VM リソース、もう 1 つはリソースの種類が `Microsoft.HybridCompute/machines` である Azure Arc リソースです。 共有の物理ホスト サーバーからゲスト オペレーティング システムが管理されないようにしているため、2 つのリソースに対する考え方として最も適切なことは、Azure VM リソースは VM の仮想ハードウェアであり、これによって、電源の状態を制御し、その SKU、ネットワーク、ストレージの構成に関する情報を表示できるということです。 Azure Arc リソースは、その VM 内のゲスト オペレーティング システムを管理するほか、拡張機能をインストールしたり、Azure Policy のコンプライアンス データを表示したり、Azure Arc 対応サーバーでサポートされているその他のすべてのタスクを完了したりするために使用できます。

## <a name="reconfigure-azure-vm"></a>Azure VM を再構成する

1. Azure VM 上のすべての VM 拡張機能を削除します。

   Azure portal で、Azure VM リソースに移動し、左側のウィンドウで **[拡張機能]** を選択します。 VM に拡張機能がインストールされている場合は、各拡張機能を個別に選択し、 **[アンインストール]** を選択します。 すべての拡張機能のアンインストールが完了するのを待ってから、手順 2 に進みます。

2. Azure VM ゲスト エージェントを無効にします。

   Azure VM ゲスト エージェントを無効にするには、リモート デスクトップ接続 (Windows) または SSH (Linux) を使用して VM に接続する必要があります。 接続したら、次のコマンドを実行してゲスト エージェントを無効にします。

   Windows では、次の PowerShell コマンドを実行します。

   ```powershell
   Set-Service WindowsAzureGuestAgent -StartupType Disabled -Verbose
   Stop-Service WindowsAzureGuestAgent -Force -Verbose
   ```

   Linux の場合は、次のコマンドを実行します。

   ```bash
   current_hostname=$(hostname)
   sudo service walinuxagent stop
   sudo waagent -deprovision -force
   sudo rm -rf /var/lib/waagent
   sudo hostnamectl set-hostname $current_hostname
   ```

3. Azure IMDS エンドポイントへのアクセスをブロックします。

   サーバーにまだ接続している状態で、次のコマンドを実行して、Azure IMDS エンドポイントへのアクセスをブロックします。 Windows では、次の PowerShell コマンドを実行します。

   ```powershell
   New-NetFirewallRule -Name BlockAzureIMDS -DisplayName "Block access to Azure IMDS" -Enabled True -Profile Any -Direction Outbound -Action Block -RemoteAddress 169.254.169.254
   ```

   Linux の場合は、ディストリビューションのドキュメントを参照して、TCP ポート 80 を介する `169.254.169.254/32` への送信アクセスをブロックする最適な方法を確認します。 通常は、組み込みのファイアウォールを使用して送信アクセスをブロックしますが、**iptables** または **nftables** を使用して一時的にブロックすることもできます。

   Azure VM で Ubuntu が実行されている場合は、次の手順を実行して、Uncomplicated Firewall (UFW) を構成します。

   ```bash
   sudo ufw --force enable
   sudo ufw deny out from any to 169.254.169.254
   sudo ufw default allow incoming
   ```

   Azure VM が CentOS、Red Hat、または SUSE Linux Enterprise Server (SLES) を実行している場合は、次の手順を実行して firewalld を構成します。

   ```bash
   firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 1 -p tcp -d 169.254.169.254 -j DROP
   firewall-cmd --reload
   ```

   その他のディストリビューションについては、ファイアウォールのドキュメントを参照するか、または次のコマンドを使用して汎用 iptables ルールを構成します。

   ```bash
   iptables -A OUTPUT -d 169.254.169.254 -j DROP
   ```

   > [!NOTE]
   > この iptables 構成は、永続的な iptables ソリューションが使用されていない限り、再起動するたびに設定する必要があります。


4. Azure Arc 対応サーバー エージェントをインストールして構成します。

   これで、Azure Arc 対応サーバーの評価を開始するための VM の準備が完了しました。 Azure Arc 対応サーバー エージェントをインストールして構成するには、[Azure portal を使用したハイブリッド マシンの接続](onboard-portal.md)に関するページを参照して、その手順に従ってインストール スクリプトを生成し、スクリプト化された方法を使用してインストールします。

   > [!NOTE]
   > Azure VM からのインターネットへの送信接続が制限されている場合は、エージェント パッケージを手動でダウンロードする必要があります。 エージェント パッケージを Azure VM にコピーし、ソース フォルダーを参照するように Azure Arc 対応サーバーのインストール スクリプトを変更します。

手順のいずれかを実行しなかった場合、インストール スクリプトではそれが Azure VM 上で実行されていることが検出され、エラーで終了します。 手順 1 から 3 を完了していることを確認してから、スクリプトを再実行します。

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、Azure Arc 対応サーバーに登録するように構成したら、Azure portal に移動して、サーバーが正常に接続したことを確認します。 対象のマシンが [Azure portal](https://portal.azure.com) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

* Azure で不可欠なセキュリティ管理および監視機能の構成を簡略化するために、[多数のマシンを計画して Azure Arc 対応サーバーに対して有効にする方法](plan-at-scale-deployment.md)について学習します。

* Windows または Linux マシン用の Automation、KeyVault などの他の Azure サービスを使用してデプロイを簡略化するために使用できる、[サポート対象の Azure VM 拡張機能](manage-vm-extensions.md)について学びます。

* テストを完了したら、[Azure Arc 対応サーバー エージェントの削除](manage-agent.md#remove-the-agent)に関するページを参照してください。
