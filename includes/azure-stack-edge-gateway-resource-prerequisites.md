---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: cb0bf7926f1bbc310e422867752e1ee1762c979a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731675"
---
開始する前に次の点を確認します。

* ご利用の Microsoft Azure サブスクリプションで Azure Stack Edge リソースが有効になっていること。 [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。
* Azure Stack Edge、IoT Hub、および Azure Storage のリソースに対してリソース グループ レベルで所有者または共同作成者のアクセス許可を持っていること。

* Azure Edge Hardware Center で注文を作成するには、Microsoft.EdgeOrder プロバイダーが登録されている必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。   
* Azure Stack Edge のリソースを作成するには、リソース グループ レベルにスコープ指定された共同作成者 (以上) のアクセス許可を持っている必要があります。 また、`Microsoft.DataBoxEdge` プロバイダーが登録されていることも確認する必要があります。 登録方法の詳細については、「[リソース プロバイダーの登録](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
  * IoT Hub リソースを作成するには、Microsoft.Devices プロバイダーが登録されていることを確認します。 登録方法の詳細については、「[リソース プロバイダーの登録](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)」をお読みください。
  * Storage アカウントのリソースを作成するには、ここでも、リソース グループ レベルにスコープ指定された共同作成者以上のアクセス許可が必要になります。 Azure Storage は、既定で、登録されたリソース プロバイターになっています。
* Azure Active Directory Graph API に対して管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。