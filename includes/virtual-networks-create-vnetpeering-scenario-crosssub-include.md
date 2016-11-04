## サブスクリプション間のピアリング
このシナリオでは、異なるサブスクリプションに属している 2 つの VNet 間でピアリングを作成します。

![cross sub scenario](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet ピアリングにおける承認には、ロールベースのアクセス制御 (RBAC) が使用されています。異なるサブスクリプション間のシナリオでは、まずピアリング リンクを作成するユーザーに、必要な権限を与える必要があります。

> [!NOTE]
> 同じユーザーが 2 つのサブスクリプションに対する特権を持っている場合、以下の手順 1. ～ 4. は省略してください。
> 
> 

<!---HONumber=AcomDC_0921_2016-->