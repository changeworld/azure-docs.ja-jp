## Azure ポータルの使用

1. 再デプロイする VM を選択し、[設定] ブレードで [再デプロイ] をクリックします。

	![Azure VM ブレード](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. [再デプロイ] をクリックして、操作を確認します。

	![VM の再デプロイ ブレード](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. VM が再デプロイの準備に入ると、VM の **ステータス** は "更新中" に変わります。

	![VM 更新中](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. VM が新しい Azure ホスト上で起動すると、VM の**ステータス**は "開始中" に変わります。

	![VM 開始中](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. VM の起動プロセスが完了したら、**ステータス** が "実行中" に戻ります。これは、VM が適切に再デプロイされたことを示します。

	![VM 実行中](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0706_2016-->