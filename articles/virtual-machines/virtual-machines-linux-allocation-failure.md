<properties
	pageTitle="Linux VM の割り当てエラーのトラブルシューティング | Microsoft Azure"
	description="Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング"
	services="virtual-machines-linux, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resourece-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング

VM を作成するとき、停止した (割り当て解除した) VM を再起動するとき、または VM のサイズを変更するとき、Microsoft Azure はコンピューティング リソースをサブスクリプションに割り当てます。これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。この情報は、サービスのデプロイを計画する場合にも役立ちます。[Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーをトラブルシューティング](virtual-machines-windows-allocation-failure.md)することもできます。


[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0525_2016-->