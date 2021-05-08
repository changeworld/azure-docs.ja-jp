---
title: Azure で Windows クライアント イメージを使用する
description: Visual Studio サブスクリプションの特典を使用して、Azure で Windows 7、Windows 8、または Windows 10 を開発/テストのシナリオにデプロイする方法
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 7879e355573d2b9952728828bd3780094a919fe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869057"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Azure で Windows クライアントを開発/テスト シナリオに使用する
適切な Visual Studio (以前は MSDN) サブスクリプションがある場合は、Azure で Windows 7、Windows 8、または Windows 10 Enterprise (x64) を開発/テストのシナリオに使用できます。 

運用環境で Windows 10 を実行する場合は、「[マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法](windows-desktop-multitenant-hosting-deployment.md)」を参照してください。


## <a name="subscription-eligibility"></a>サブスクリプションの資格
アクティブな Visual Studio サブスクライバー (Visual Studio サブスクリプション ライセンスを取得したユーザー) は、Windows クライアント イメージを開発とテストに使用できます。 Windows クライアント イメージは、お使いのハードウェアまたは Azure Virtual Machines で使用できます。

特定の Windows クライアント イメージは、Azure Marketplace から入手できます。 また、任意の種類のプラン内の Visual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[準備および作成](prepare-for-upload-vhd-image.md)してから、[Azure にアップロード](upload-generalized-managed.md)することもできます。

## <a name="eligible-offers-and-client-images"></a>対象となるプランとクライアント イメージ
次の表に、Azure Marketplace から Windows クライアント イメージをデプロイするための対象となるプラン ID の詳細を示します。 Windows クライアント イメージは、次のプランにのみ表示されます。 

> [!NOTE]
> イメージ プランは、Azure Marketplace の **[Windows クライアント]** の下にあります。 Visual Studio サブスクライバーで使用できるクライアント イメージを検索する場合は、 **[Windows クライアント]** を使用します。 

| プラン名 | プラン番号 | 使用可能なクライアント イメージ | 
|:--- |:---:|:---:|
| [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Test Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Premium with MSDN (特典)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Visual Studio Enterprise (BizSpark) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Azure サブスクリプションの確認
プラン ID を把握していない場合は、Azure portal から取得できます。  
- *[サブスクリプション]* ウィンドウで:![Azure portal からのプラン ID の詳細](./media/client-images/offer-id-azure-portal.png) 
- または、**[課金情報]** をクリックした後、サブスクリプション ID をクリックします。 *[課金情報]* ウィンドウにプラン ID が表示されます。 
- また、Azure アカウント ポータルの ['サブスクリプション' タブ](https://account.windowsazure.com/Subscriptions) からオファー ID を表示することもできます。![Azure アカウント ポータルからのプラン ID の詳細](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>次のステップ
これで、[PowerShell](quick-create-powershell.md)、[Resource Manager テンプレート](ps-template.md)、または [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) を使用して VM をデプロイすることができます。
