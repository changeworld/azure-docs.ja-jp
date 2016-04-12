<properties
	pageTitle="Windows VM の割り当てエラーのトラブルシューティング |Microsoft Azure"
	description="Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング"
	services="virtual-machines-windows, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング

VM を作成するとき、停止した (割り当て解除した) VM を再起動するとき、または VM のサイズを変更するとき、Microsoft Azure はコンピューティング リソースをサブスクリプションに割り当てます。これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。この情報は、サービスのデプロイを計画する場合にも役立ちます。

「一般的なトラブルシューティングの手順」のセクションでは、一般的な問題を解決する手順を示します。「詳細なトラブルシューティングの手順」のセクションでは、特定のエラー メッセージごとに問題の解決の手順を説明します。最初に、割り当てのしくみと割り当てエラーの発生理由を理解するための背景情報を以下に示します。[Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーをトラブルシューティング](virtual-machines-linux-allocation-failure.md)することもできます。

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!---HONumber=AcomDC_0330_2016------>