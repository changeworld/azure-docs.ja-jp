
Windows ベースの Azure 仮想マシンへのリモート デスクトップ (RDP) 接続に関するトラブルシューティングで問題が発生している場合、または Linux ベースの Azure 仮想マシンへの SSH 接続に関するトラブルシューティングを行う場合は、この記事を参考にすることで、繰り返しサポートを受けたり仮想マシンのサイズを変更したりしなくても、お客様自身で問題を解決することができます。Microsoft Azure では、Azure PowerShell から再デプロイ操作が呼び出されると仮想マシンを再デプロイします。

この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されることに注意してください。


## Azure PowerShell の使用

マシンに最新の Azure PowerShell 1.x がインストールされていることを確認します。詳細については、「[Azure PowerShell のインストールおよび構成方法](../articles/powershell-install-configure.md)」を参照してください。

次の Azure PowerShell コマンドを使用して仮想マシンを再デプロイします。

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


このコマンドの実行中に、[Azure ポータル](https://portal.azure.com)で仮想マシンをチェックします。VM の **[状態]** が次のように変わることを確認してください。

1. 初期の **[状態]** は *[実行中]* です。

	![再デプロイの初期状態](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. **[状態]** が *[更新しています]* に変わります。

	![再デプロイの状態 [更新しています]](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. **[状態]** が *[開始中]* に変わります。

	![再デプロイの状態 [開始中]](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. **[状態]**が *[実行中]* に戻ります。

	![再デプロイの最終状態](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

**[状態]**が *[実行中]* に戻ると、VM は正常に再デプロイされています。

<!---HONumber=AcomDC_0309_2016-->