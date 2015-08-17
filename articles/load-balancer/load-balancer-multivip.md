<properties 
   pageTitle="クラウド サービスごとの複数の VIP"
   description="MultiVIP の概要とクラウド サービスで複数の Vip を設定する方法"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/23/2015"
   ms.author="joaoma" />

# クラウド サービスごとの複数の VIP
Azure Cloud Services は、Azure によって提供される IP アドレスを使用して、パブリック インターネット経由でアクセスできます。このパブリック IP アドレスは、Azure ロード バランサーにリンクされているため VIP (仮想 IP) と呼ばれますが、実際はクラウド サービス内の VM インスタンスではありません。クラウド サービス内の VM インスタンスは、1 つの VIP を使用してアクセスできます。

ただし、同じクラウド サービスへのエントリ ポイントとして 1 つ以上の VIP が必要な場合のあるシナリオがあります。たとえば、クラウド サービスは、既定の SLSL ポート 443 を使用して SSL 接続を必要とする複数の Web サイト、異なる顧客をホストする各サイト、テナントをホストできます。このようなシナリオでは、Web サイトごとに異なるパブリック IP アドレスを持つ必要があります。次の図は、同じパブリック ポートで複数の SSL 証明書が必要な一般的なマルチテナント Web ホスティングを示しています。

![Multi VIP SSL scenario](./media/load-balancer-multivip/Figure1.png)

前のシナリオでは、すべての VIP は同じパブリック ポート (443) を使用し、トラフィックはすべての Web サイトをホストするクラウド サービスの内部 IP アドレス用の一意のプライベート ポート上の 1 つ以上の負荷分散された VM にリダイレクトされます。

>[AZURE.NOTE]複数の VIP を使用するもう 1 つのシナリオは、同じ仮想マシン セット上に複数の SQL AlwaysOn 可用性グループ リスナーをホストしています。

VIP は、既定では動的です。つまり、時間の経過と共にクラウド サービスに割り当てられている実際の IP アドレスが変化する可能性があります。この問題を防ぐために、サービスの VIP を予約することができます。予約済み VIP の詳細については、「[予約済みパブリック IP](../virtual-networks-reserved-public-ip)」に関するページをご覧ください。

>[AZURE.NOTE]VIP と予約済み IP の料金の詳細については、「[IP アドレスの料金](http://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。

PowerShell を使用すると、クラウド サービスで使用される VIP を確認できるだけでなく、VIP の追加 と削除、エンドポイントへの VIP の関連付け、特定の VIP での負荷分散の構成を行うことができます。

## 制限事項

現時点では、複数 VIP の機能は以下のシナリオに制限されています。

- **IaaS のみ**。複数の VIP は、VM を含むクラウド サービスでのみ有効にできます。PaaS のシナリオで、ロール インスタンスと共に複数の VIP を使用することはできません。
- **PowerShell のみ**。複数の VIP は、PowerShell を使用してのみ管理できます。

>[AZURE.IMPORTANT]これらの制限は一時的なものであり、いつでも変更される可能性があります。今後の変更を確認するには、このページに再度アクセスしてください。


## VIP をクラウド サービスに追加する方法
VIP をサービスを追加するには、次の PowerShell コマンドを実行します。

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

上記のコマンドでは、次の例のような結果が表示されます。

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## VIP をクラウド サービスから削除する方法
上記の例でサービスに追加された VIP を削除するには、次の PowerShell コマンドを実行します。

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT]エンドポイントが関連付けられていない場合にのみ、VIP を削除できます。

## クラウド サービスから VIP 情報を取得する方法
クラウド サービスに関連付けられている VIP を取得するには、次の PowerShell スクリプトを実行します。

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

上記のスクリプトでは、次の例のような結果が表示されます。

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

この例では、クラウド サービスには次の 3 つの VIP があります。

- **Vip1** は既定の VIP で、IsDnsProgrammedName の値が true に設定されていることがわかります。
- **Vip2** と **Vip3** は、IP アドレスがない場合は使用されません。VIP にエンドポイントを関連付ける場合のみ使用されます。

>[AZURE.NOTE]これらをエンドポイントに関連付けると、サブスクリプションは余分な VIP にのみ課金されます。料金の詳細については、「[IP アドレスの料金](http://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。

## VIP をエンドポイントに関連付ける方法
クラウド サービスの VIP をエンドポイントに関連付けるには、次の PowerShell コマンドを実行します。

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
    | Update-AzureVM

上記のコマンドは、ポート *80* 上の *Vip2* という VIP にリンクされているエンドポイントを作成し、これを *TCP* ポート *8080* を使用して*myService* というクラウド サービスの *myVM1* という VM にリンクします。

構成を確認するには、次の PowerShell コマンドを実行します。

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

次のような出力が表示されます。

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## 特定の VIP で負荷分散を有効にする方法
負荷分散のために、複数の仮想マシンに 1 つの VIP を関連付けることができます。たとえば、*myService* というクラウド サービスと、*myVM1* と *myVM2* という 2 つの仮想マシンがあると仮定します。クラウド サービスには複数の VIP があり、その中の 1 つが *Vip2* です。*Vip2* のポート *81* のすべてのトラフィックが、ポート *8181* 上の *myVM1* と *myVM2* 間で分散されていることを確認する場合は、次の PowerShell スクリプトを実行します。

    Get-AzureVM -ServiceName myService -Name myVM1 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
    | Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
    | Update-AzureVM

ロード バランサーを更新して異なる VIP を使用することもできます。たとえば、次の PowerShell コマンドを実行すると、負荷分散セットは Vip1 という VIP を使用するように変更されます。

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## 関連項目

[インターネットに接続するロード バランサーの概要](load-balancer-internet-overview.md)

[インターネットに接続するロード バランサーの開始](load-balancer-internet-getstarted.md)

[仮想ネットワークの概要](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=August15_HO6-->