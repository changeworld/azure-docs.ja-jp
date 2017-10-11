VM へのリモート デスクトップ接続を作成することで、VNet に展開されている VM に接続することができます。 最初に、VM に接続できることを確認する最善の方法では、コンピューター名ではなく、プライベートの IP アドレスを使用して接続します。 かどうかを接続できるかどうかの名前解決が正しく構成されていない参照をテストするようにして、します。

1. プライベート IP アドレスを検索します。 複数の方法で VM のプライベート IP アドレスを見つけることができます。 次に、Azure ポータル、PowerShell の手順を説明します。

  - Azure ポータル、Azure ポータルで、バーチャル マシンを検索します。 仮想マシンのプロパティを表示します。 プライベート IP アドレスが一覧表示します。

  - PowerShell での Vm およびプライベート IP の一覧を表示するには、この例は、リソース グループからアドレスを使用して。 この例を使用する前に変更する必要はありません。

    ```powershell
    $VMs = Get-AzureRmVM
    $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

    foreach($Nic in $Nics)
    {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
    }
    ```

2. VPN 接続を使用して、VNet に接続していることを確認します。
3. 開いている**リモート デスクトップ接続**タスクバーの [検索] ボックスには、"RDP"または「リモート デスクトップ接続」を入力すると、リモート デスクトップ接続を選択し、します。 PowerShell で、'mstsc' コマンドを使用してリモート デスクトップ接続を開くこともできます。 
4. リモート デスクトップ接続では、VM のプライベート IP アドレスを入力します。 その他の設定を調整し、接続「オプションを表示」をクリックできます。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>仮想マシンに RDP 接続のトラブルシューティング

VPN 接続経由でバーチャル マシンに接続できない場合、次を確認します。

- VPN 接続が成功したことを確認します。
- 仮想マシンのプライベート IP アドレスに接続していることを確認します。
- を、プライベート IP アドレスがコンピューター名ではなくを使用して VM に接続できる場合は、DNS が正しく構成されていることを確認します。 Vm の名前解決するしくみの詳細については、次を参照してください。 [Vm の名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)です。
- RDP 接続の詳細については、次を参照してください。 [VM にリモート デスクトップのトラブルシューティングを行う接続](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)です。