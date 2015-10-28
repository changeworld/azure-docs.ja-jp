## ARM テンプレートのダウンロードおよび理解

Github から既存の ARM テンプレートをダウンロードして VNet と 2 つのサブネットを作成し、そのテンプレートに変更を加えて再利用することができます。再利用するには、次の手順に従ってください。

1. https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnets に移動します。
2. [**azuredeploy.json**]、[**RAW**] の順にクリックします。
3. お使いのコンピューター上のローカル フォルダーにファイルを保存します。
4. ARM テンプレートを使用したことがある場合は、手順 7. に進みます。
5. 保存したファイルを開き、 5 行目にある **parameters** の内容を参照します。ARM テンプレートのパラメーターでは、デプロイ中に入力できる、値のプレース ホルダーが用意されています。

	| パラメーター | 説明 |
	|---|---|
	| **location** | VNet が作成される Azure リージョン |
	| **vnetName** | 新しい VNet の名前 |
	| **addressPrefix** | VNet のアドレス空間 (CIDR 形式) |
	| **subnet1Name** | 最初の VNet の名前 |
	| **subnet1Prefix** | 最初のサブネットの CIDR ブロック |
	| **subnet2Name** | 2 番目の VNet の名前 |
	| **subnet2Prefix** | 2 番目のサブネットの CIDR ブロック |

	>[AZURE.IMPORTANT]Github で管理される ARM テンプレートは、今後変更される可能性があります。使用する前に、必ずテンプレートを確認してください。
	
6. **リソース** の内容を確認し、次を参照します。

	- **type**。テンプレートによって作成されるリソースのタイプ。この場合は **Microsoft.Network/virtualNetworks** にあたり、VNet を指しています。
	- **name**。リソースの名前です。**[parameters('vnetName')]** が使用されているため、名前はデプロイ中にユーザーまたはパラメーター ファイルによって入力されます。
	- **properties**。リソースのプロパティの一覧です。VNet の作成中、このテンプレートではアドレス空間とサブネットのプロパティが使用されます。

7. https://github.com/Azure/azure-quickstart-templates/tree/master/101-two-subnetsに戻ります。
8. [**azuredeploy-paremeters.json**]、[**RAW**] の順にクリックします。
9. お使いのコンピューター上のローカル フォルダーにファイルを保存します。
10. 保存したファイルを開き、パラメーターの値を編集します。次の値を使用して、このシナリオで説明した VNet をデプロイします。

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. ファイルを保存します。
  

<!---HONumber=Oct15_HO3-->