#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>クラウド アプライアンス上のパブリック エンドポイントを作成するには

1. Azure ポータルにサインインします。
2. 移動して**仮想マシン**を選択して、クラウド アプライアンスとして使用されている仮想マシンをクリックします。
    
3. 仮想マシンとの間のトラフィックのフローを制御するには、ネットワーク セキュリティ グループ (NSG) ルールを作成する必要があります。 NSG ルールを作成する次の手順を実行します。
    1. 選択**ネットワーク セキュリティ グループ**です。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 表示される既定のネットワーク セキュリティ グループをクリックします。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 選択**受信のセキュリティ規則**です。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. をクリックして**+ 追加**受信セキュリティ規則を作成します。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        追加の受信セキュリティ規則ブレード。

        1. **名前**エンドポイントの次の名前を入力: WinRMHttps です。
        
        2. **優先度**を選択する (既定の規則の優先順位) 数が 1000 より小さい。 高い値を優先順位を下げます。

        3. 設定、**ソース**に**任意**です。

        4. **サービス** **WinRM**です。 **プロトコル**に自動的に設定されている**TCP**と**ポート範囲**に設定されている**5986**です。

        5. をクリックして**OK**規則を作成します。

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最後の手順は、サブネットまたは特定のネットワーク インターフェイスに、ネットワーク セキュリティ グループを関連付けるにです。 サブネットを持つネットワーク セキュリティ グループを関連付けるには、次の手順を実行します。
    1. 移動して**サブネット**です。
    2. をクリックして**関連付ける +**です。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 仮想ネットワークを選択し、適切なサブネットを選択します。
    4. をクリックして**OK**規則を作成します。

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

ルールを作成した後は、パブリック仮想 IP (VIP) アドレスを決定するには、その詳細を表示できます。 このアドレスを記録します。


