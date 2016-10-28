## 簡単な手順 

この記事では、ポータルでサブスクリプションにログインし、Resource Manager デプロイメント モデルを使用して利用可能なイメージで仮想マシンを作成していることを前提としています。仮想マシンの起動後、次の手順に従います。

1.  ポータルで仮想マシンの設定を表示し、パブリック IP アドレスをクリックします。

    ![IP リソースの検索](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  パブリック IP の DNS 名が空白であることに注意してください。[パブリック IP] ブレードの **[構成]** をクリックします。

    ![settings ip](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  目的の DNS 名ラベルを入力し、この構成を**保存**します。

    ![DNS 名のラベルの入力](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    これで、パブリック IP リソースのブレードでこの新しい DNS ラベルが表示されるようになりました。

4.  パブリック IP ブレードを閉じ、ポータルの仮想マシン ブレードに戻ります。パブリック IP リソースの IP アドレスの横に DNS 名/FQDN が表示されることを確認します。

    ![FQDN が作成される](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->