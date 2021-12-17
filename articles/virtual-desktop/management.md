---
title: Microsoft Endpoint Configuration Manager による Azure Virtual Desktop の管理
description: Azure Virtual Desktop 環境の推奨管理方法。
author: heidilohr
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: cc859f82fa65ed1505a440c0b974ef8f4fde5fc7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003384"
---
# <a name="microsoft-endpoint-manager-and-intune-for-azure-virtual-desktop"></a>Microsoft Endpoint Manager と Intune による Azure Virtual Desktop の管理

展開後に、[Microsoft エンドポイント マネージャー](https://www.microsoft.com/endpointmanager)を使用して、Azure Virtual Desktop 環境を管理することをお勧めします。 Microsoft エンドポイント マネージャーは、Microsoft Endpoint Configuration Manager と Microsoft Intune が組み込まれた統合管理プラットフォームです。

> [!NOTE]
> Microsoft エンドポイント マネージャーを使用した Azure Virtual Desktop セッション ホストの管理は、現在 Azure パブリック クラウドでのみサポートされています。

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Microsoft Endpoint Configuration Manager バージョン 1906 以降では、Azure Virtual Desktop デバイスを管理できます。 詳細については、「[Configuration Manager のクライアントとデバイスでサポートされる OS バージョン](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop)」を参照してください。

## <a name="microsoft-intune"></a>Microsoft Intune

Intune では、Azure Virtual Desktop 用の Windows 10 Enterprise Virtual Machines (VM) がサポートされています。 サポートの詳細については、「[Intune での Windows 10 Enterprise の使用](/mem/intune/fundamentals/windows-virtual-desktop)」を参照してください。

Azure Virtual Desktop 上での Windows 10 Enterprise マルチセッション VM に対する Intune のサポートは、現在パブリック プレビューの段階です。 現在サポートされているパブリック プレビューのバージョンを確認するには、「[Intune での Windows 10 Enterprise マルチセッションの使用](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)」を参照してください。

## <a name="licensing"></a>ライセンス

[Microsoft Endpoint Configuration Manager と Microsoft Intune のライセンス](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing)は、大部分の Microsoft 365 サブスクリプションに含まれています。 

ライセンス要件の詳細については、次のリソースを参照してください。

- [Configuration Manager のブランチとライセンスに関してよく寄せられる質問](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Microsoft Intune のライセンス](/mem/intune/fundamentals/licenses)
