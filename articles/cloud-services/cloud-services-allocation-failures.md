<properties
	pageTitle="クラウド サービスの割り当てエラーのトラブルシューティング | Microsoft Azure"
	description="Azure でクラウド サービスをデプロイするときの割り当てエラーのトラブルシューティング"
	services="azure-service-management, cloud-services"
	documentationCenter=""
	authors="kenazk"
	manager="drewm"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="cloud-services"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="kenazk"/>



# Azure でクラウド サービスをデプロイするときの割り当てエラーのトラブルシューティング

## 概要
インスタンスをクラウド サービスにデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。この情報は、サービスのデプロイを計画する場合にも役立ちます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。

### 背景情報 – 割り当てのしくみ
Azure データセンターのサーバーは、クラスターにパーティション分割されています。複数のクラスターで、新しいクラウド サービスの割り当て要求が試みられます。最初のインスタンスを (ステージング環境または運用環境の) クラウド サービスにデプロイすると、このクラウド サービスは 1 つのクラスターに固定されます。このクラウド サービスでさらにデプロイすると、すべて同じクラスターで処理されます。この記事では、このことを "クラスターに固定されている" と言います。次の図 1 は、複数のクラスターで試行される通常の割り当てを示しています。図 2 は、クラスター 2 に固定されている割り当てを示しています。これは、既存のクラウド サービス CS\_1 がクラスター 2 でホストされているためです。

![割り当ての図表](./media/cloud-services-allocation-failure/Allocation1.png)

### 割り当てエラーが発生する理由
割り当て要求がクラスターに固定されている場合、利用可能なリソース プールが 1 つのクラスターに制限されるため、空きリソースを見つけられない可能性が高くなります。さらに、割り当て要求がクラスターに固定されていて、そのクラスターでは要求されたリソースの種類がサポートされていない場合、クラスターに空きリソースがあっても、要求は失敗します。次の図 3 は、唯一の候補であるクラスターに空きリソースがないため、固定された割り当てがエラーになる場合を示しています。図 4 は、唯一の候補であるクラスターに空きリソースがあるものの、要求された VM サイズをサポートしていないため、固定された割り当てがエラーになる場合を示しています。

![固定された割り当てのエラー](./media/cloud-services-allocation-failure/Allocation2.png)

## クラウド サービスの割り当てエラーのトラブルシューティング
### エラー メッセージ
次のようなエラー メッセージが表示されることがあります。

	"Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### 一般的な問題
以下に、割り当て要求が単一のクラスターに固定される原因となる一般的な割り当てシナリオを示します。

- ステージング スロットへのデプロイ - クラウド サービスをいずれかのスロットにデプロイすると、クラウド サービス全体が特定のクラスターに固定されます。つまり、運用スロットにデプロイが既に存在する場合、新しいステージング環境のデプロイは、運用スロットと同じクラスターにしか割り当てることができません。クラスターが限界に近づくと、要求が失敗する場合があります。 
 
- スケーリング - 既存のクラウド サービスに新しいインスタンスを追加する場合は、同じクラスターに割り当てる必要があります。通常、小規模のスケーリング要求は割り当てることができますが、常に割り当てることができるわけではありません。クラスターが限界に近づくと、要求が失敗する場合があります。
	
- アフィニティ グループ - 空のクラウド サービスに新しくデプロイする場合は、クラウド サービスがアフィニティ グループに固定されている場合を除き、そのリージョン内の任意のクラスターのファブリックによって割り当てることができます。同じアフィニティ グループへのデプロイは、同じクラスターで試行されます。クラスターが限界に近づくと、要求が失敗する場合があります。
	
- アフィニティ グループ vNet - 以前の仮想ネットワークはリージョンではなくアフィニティ グループに関連付けられていたため、これらの仮想ネットワーク内のクラウド サービスはアフィニティ グループのクラスターに固定されました。このような仮想ネットワークへのデプロイは、固定されたクラスターで試行されます。クラスターが限界に近づくと、要求が失敗する場合があります。

## 解決方法

1. 新しいクラウド サービスに再デプロイする - この解決方法ではそのリージョン内のすべてのクラスターからプラットフォームを選択できるため、成功する可能性が最も高くなります。
	
	- 新しいクラウド サービスにワークロードをデプロイします  
	
	- 新しいクラウド サービスへのトラフィックを指すように CNAME または A レコードを更新します
		
	- 以前のサイトに送信されるトラフィックが 0 個になると、以前のクラウド サービスを削除できます。この解決方法ではダウンタイムは発生しません。

2. 運用スロットとステージング スロットの両方を削除する - この解決方法では、既存の DNS 名が維持されますが、アプリケーションでダウンタイムが発生します。
	
	- 既存のクラウド サービスの運用スロットとステージング スロットを削除し、クラウド サービスを空にします。 
	
	- 次に、既存のクラウド サービスに新しいデプロイを作成します。これにより、リージョン内のすべてのクラスターで割り当てが再度試行されます。クラウド サービスがアフィニティ グループに関連付けられていないことを確認します。

3. 予約済み IP - この解決方法では、既存の IP アドレスが維持されますが、アプリケーションでダウンタイムが発生します。
	
	- Powershell を使用して既存のデプロイ用の ReservedIP を作成します 

	```
	New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
	```
		
	- 上の 2. の手順に従って、サービスの CSCFG で新しい ReservedIP を確実に指定します。

4. 新しいデプロイのアフィニティ グループを削除する - アフィニティ グループは推奨されなくなりました。上の 1. の手順に従って、新しいクラウド サービスをデプロイしてください。クラウド サービスがアフィニティ グループ内にないことを確認します。

5. リージョン Virtual Network に変換する - 「[アフィニティ グループから、リージョン Virtual Network (VNet) に移行する方法](https://azure.microsoft.com/documentation/articles/virtual-networks-migrate-to-regional-vnet/)」を参照してください。

## その他のリソース
### Azure カスタマー サポートへの問い合わせ

この記事が Azure の問題の解決に役立たなかった場合は、[MSDN と Stack Overflow](http://azure.microsoft.com/support/forums/) の Azure フォーラムを参照してください。問題についての Azure サポート インシデントを送信することもできます。その場合は、[Azure サポート](http://azure.microsoft.com/support/options/)のサイトに移動して、[サポートの要求] をクリックします。Azure サポートの使用方法の詳細については、「[Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。

<!---HONumber=Nov15_HO4-->