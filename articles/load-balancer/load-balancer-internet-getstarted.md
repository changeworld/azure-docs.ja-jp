
<properties
   pageTitle="インターネットに接続するロード バランサーの開始 | Microsoft Azure"
   description="まず、仮想マシンまたはクラウド サービスに、インターネットに接続するロード バランサー セットを取得します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# インターネットに接続するロード バランサーの構成の開始

Microsoft Azure の負荷分散サービスは、すべてのテナントの種類 (IaaS または PaaS) とすべての OS バージョン (Windows または任意の Linux ベース OS をサポート) で動作します。


## インターネットに接続するロード バランサーを仮想マシンに設定する

クラウド サービスの仮想マシン全体でインターネットからのネットワーク トラフィックを負荷分散するには、負荷分散セットを作成する必要があります。この手順では、既に仮想マシンが作成されていて、これらがすべて同じクラウド サービス内にあることを前提としています。

次の手順に従って、仮想マシンにロード バランサー セットを構成します。

1. Microsoft Azure 管理ポータルで、[仮想マシン] をクリックし、負荷分散セットの仮想マシンの名前をクリックします。
2.	[エンドポイント] をクリックし、[追加] をクリックします。

4.	[仮想マシンにエンドポイントを追加します] ページで、右矢印をクリックします。

4.	[エンドポイントの詳細を指定します] ページで、次のように指定します。
	- [名前] にエンドポイントの名前を入力するか、一般的なプロトコル用にあらかじめ定義されているエンドポイントの一覧から選択します。
	-  [プロトコル] で、エンドポイントの種類に応じて必要となるプロトコル (TCP または UDP) を選択します (必要な場合)。
 	-  [パブリック ポート] と [プライベート ポート] に、仮想マシンが使用するポート番号を入力します (必要な場合)。仮想マシンのプライベート ポートとファイアウォール ルールを使って、アプリケーションに適した方法でトラフィックをリダイレクトすることができます。プライベート ポートはパブリック ポートと同じにできます。たとえば、Web (HTTP) トラフィック用のエンドポイントの場合、パブリック ポートとプライベート ポートの両方にポート 80 を割り当てることができます。

5.	[負荷分散セットの作成] を選択し、右矢印をクリックします。

6.	[負荷分散セットの構成] ページで、負荷分散セットの名前を入力し、Azure ロード バランサーのプローブ動作の値を割り当てます。ロード バランサーはプローブを使用して、負荷分散セット内の仮想マシンが着信トラフィックを受信できるかどうかを判断します。

7.	チェック マークをクリックして、負荷分散されたエンドポイントを作成します。仮想マシンの [エンドポイント] ページの [負荷分散セット名] 列に [はい] が表示されます。

8.	管理ポータルで、[仮想マシン]、負荷分散セットの追加の仮想マシンの名前、[エンドポイント]、[追加] の順にクリックします。

9.	[仮想マシンにエンドポイントを追加します] ページで、[既存の負荷分散セットにエンドポイントを追加する] をクリックし、負荷分散セットの名前を選択して、右矢印をクリックします。

10.	[エンドポイントの詳細を指定します] ページで、エンドポイントの名前を入力し、チェック マークをクリックします。負荷分散セットに仮想マシンを追加する場合は、手順 8. ～ 10. を繰り返します。

次の Windows PowerShell コマンドレットでエンドポイントを構成することもできます。

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## インターネットに接続するバランサーをクラウド サービスに設定する


クラウド サービスは、ロード バランサーで自動的に構成され、サービス モデルを使用してカスタマイズできます。

Azure SDK for .NET 2.5 を使用してクラウド サービスを更新できます。クラウド サービスのエンドポイントの設定は、[サービス定義](https://msdn.microsoft.com/library/azure/gg557553.aspx) .csdef で行われます。

次の例は、クラウド デプロイメントの servicedefinition.csdef ファイルを構成する方法を示しています。

クラウド デプロイメントによって生成された .csdef ファイルのスニペット内を確認すると、外部エンドポイントがポート 10000、10001、10002 の HTTP ポートを使用するように構成されていることがわかります。


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### クラウド サービスのロード バランサーの正常性状態を確認する


次に、正常性プローブの例を示します。

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

ロード バランサーは、エンドポイントの情報とプローブの情報を結合して、サービスの正常性のクエリに使用する URL を http://{DIP of VM}:80/Probe.aspx という形式で作成します。

サービスは、同じ IP が定期的にアクセスしていることを通知します。これは、VM が実行しているノードのホストから送信される正常性プローブ要求です。サービスは、サービスが正常であると仮定するロード バランサーの状態コード HTTP 200 で応答する必要があります。他の HTTP 状態コード (例: 503) は、直接 VM をローテーションから除外します。

プローブの定義はプローブの頻度も制御します。上記の場合、ロード バランサーは、エンドポイントを 5 秒ごとに調査しています。肯定応答を10 秒間 (2 つのプローブ間隔) 受信しなかった場合、そのプローブは下位と見なされ、仮想マシンはローテーションから除外されます。同様に、サービスがローテーションから除外され、肯定応答を受信した場合、サービスはすぐにローテーションに戻されます。サービスが正常な状態と異常な状態の間で変動する場合、ロード バランサーは多くのプローブが正常な状態になるまで、サービスをローテーションに再び戻すことを遅らせることができます。

詳細については、[正常性プローブ](https://msdn.microsoft.com/library/azure/jj151530.aspx)のサービス定義スキーマをご確認ください。

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->