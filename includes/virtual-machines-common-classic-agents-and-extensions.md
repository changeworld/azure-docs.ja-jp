



VM 拡張機能により以下を実行できます。

* セキュリティおよび ID 機能の変更 (アカウント値のリセットやマルウェア対策の使用など)
* 監視および診断の開始、停止、構成
* 接続機能 (RDP や SSH など) のリセットまたはインストール
* VM の診断、監視、管理

他にも多くの機能があります。VM 拡張機能は定期的にリリースされます。 この記事では、Windows および Linux 向けの Azure VM エージェントと、それらのエージェントの VM 拡張機能のサポート方法について説明します。 機能カテゴリごとの VM 拡張機能の一覧については、[Azure VM 拡張機能とその機能](../articles/virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows および Linux 用 Azure VM エージェント
Azure Virtual Machines エージェント (VM エージェント) はセキュリティで保護された軽量のプロセスであり、イメージ ギャラリーから Azure Virtual Machines のインスタンスに対して、また VM エージェントをインストール済みのカスタム VM インスタンスに対して、VM 拡張機能のインストール、構成、削除を行います。 VM エージェントは、安全な Azure VM のローカル制御サービスとして機能します。 エージェントから読み込まれるされる拡張機能によって、インスタンスを使用して生産性を向上させる機能が提供されます。

Azure VM エージェントには、Windows VM 向けと Linux VM 向けの 2 種類があります。 既定では、VM エージェントはイメージ ギャラリーから VM を作成したときに自動的にインストールされますが、インスタンスの作成後にインストールしたり、カスタムの VM イメージで VM エージェントをインストールしてから自分でアップロードしたりすることも可能です。

> [!IMPORTANT]
> これらの VM エージェントは、仮想マシン インスタンスを安全に管理できる非常に軽量なサービスです。 VM エージェントを必要としない場合もあります。 この場合には、VM エージェントがインストールされていない VM を作成する必要があります。 VM エージェントは物理的に削除できますが、インスタンス上でのすべての VM 拡張機能の動作は定義されていません。 このため、インストールされた VM エージェントの削除は、この時点ではサポートされていません。
> 
> 

VM エージェントは、次のような状況で有効化されます。

* Azure クラシック ポータルの**簡易作成**の方法を使用して VM のインスタンスを作成した場合。または、Azure クラシック ポータルの**カスタム作成**の方法を使用し、**[VM エージェントのインストール]** チェック ボックスをオンにして (下記画像を参照) 仮想マシンのインスタンスを作成した場合。 詳細については、[カスタム仮想マシンの作成方法](../articles/virtual-machines/virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。
  
  ![VM エージェントのチェックボックス](./media/virtual-machines-common-classic-agents-and-extensions/IC719409.png)
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) コマンドレットまたは [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) コマンドレットを使用して VM のインスタンスを作成した場合。 **Add-AzureProvisioningConfig** コマンドレットに [–DisableGuestAgent](https://msdn.microsoft.com/library/azure/dn495299.aspx) パラメーターを追加すると、VM エージェントをインストールせずに VM を作成することができます。
* VM エージェント (Window バージョンまたは Linux バージョンのいずれか) を手動でダウンロードして既存の VM インスタンスにインストールし、PowerShell または REST 呼び出しで **ProvisionGuestAgent** の値を **true** に設定した場合 (VM エージェントの手動インストール後にこの値を設定しない場合、VM エージェントを追加したことが正常に検出されません)。次のコード例に、`$svc` 引数と `$name` 引数が決定済みの場合に PowerShell を使用してこの処理を行う方法を示します。
  
      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc
* VM エージェントを Azure へアップロードする前にインストールした VM イメージを作成した場合。 Window VM の場合、 [Windows VM Agent.msi ファイル](http://go.microsoft.com/fwlink/?LinkID=394789) をダウンロードして、VM エージェントをインストールします。 Linux VM の場合、VM エージェントは GitHub リポジトリ (<https://github.com/Azure/WALinuxAgent>) からインストールします。 Linux への VM エージェントのインストール方法の詳細については、[Azure Linux VM エージェント ユーザー ガイド](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。

> [!NOTE]
> PaaS では VM エージェントは **WindowsAzureGuestAgent** と呼ばれ、Web ロール VM および worker ロール VM でいつでも使用できます (詳細については、[Azure ロールのアーキテクチャ](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)に関するページを参照してください)。ロール VM 用の VM エージェントで、永続 Virtual Machines の場合と同じ方法でクラウド サービス VM に拡張機能を追加できるようになりました。 ロール VM の VM 拡張機能と永続 VM の VM 拡張機能の最も大きな違いは、ロール VM では、拡張機能がクラウド サービス、そのクラウド サービス内のデプロイの順に追加されることです。
> 
> すべての利用可能なロール VM 拡張機能の一覧を表示するには、 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) コマンドレットを使用します。
> 
> 

## <a name="find-add-update-and-remove-vm-extensions"></a>VM 拡張機能の検索、追加、更新、および削除
これらのタスクの詳細については、[VM 拡張機能の追加、検索、更新、および削除](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。



<!--HONumber=Nov16_HO3-->


