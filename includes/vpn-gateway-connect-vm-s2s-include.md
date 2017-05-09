リモート デスクトップ接続を作成すると、VNet にデプロイされている VM に接続できます。 VM に接続できるかどうかを初めて確認する際に最も良い方法は、その VM のコンピューター名ではなく、プライベート IP アドレスを使って接続してみることです。 この方法であれば、名前の解決が適切に構成されているかではなく、VM に接続できるかどうかをテストすることができます。

1. **プライベート IP アドレスを特定します。** VM のプライベート IP アドレスは、Azure Portal で VM のプロパティを表示するか、PowerShell を使うと確認できます。

  - Azure Portal を使用する場合: Azure Portal で仮想マシンを探します。 VM のプロパティを表示すると、 プライベート IP アドレスが表示されます。

  - PowerShell を使用する場合: 以下の例に示したコマンドを使用すると、リソース グループに含まれる VM とプライベート IP アドレスの一覧が表示されます。 このコマンドは、使用前に変更を加える必要はありません。

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **VM に接続します。** VPN 接続を使って VNet に接続していることを確認します。 タスク バーの検索ボックスに「RDP」または「リモート デスクトップ接続」と入力してリモート デスクトップ接続を開き、リモート デスクトップ接続を選択します。 このほか、PowerShell で `mstsc` コマンドを使ってリモート デスクトップ接続を開くこともできます。 リモート デスクトップ接続で、VM のプライベート IP アドレスを入力します。 必要に応じて [オプションの表示] をクリックして追加の設定を済ませたら、接続します。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM に対する RDP 接続をトラブルシューティングするには

VPN 接続を使って仮想マシンに接続する際に問題が発生した場合には、いくつかの点を確認する必要があります。

- VPN 接続が成功したことを確認します。
- VM のプライベート IP アドレスに接続できていることを確認します。
- プライベート IP アドレスを使って VM に接続できるものの、コンピューター名では接続できない場合には、DNS が正しく構成されているかどうかを確認します。
- 詳細については、[VM に対するリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。