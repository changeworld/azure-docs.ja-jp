---

title: "Azure RemoteApp から Citrix XenApp Essentials に移行する方法 | Microsoft Docs"
description: "Azure RemoteApp から Citrix XenApp Essentials に移行する方法"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Azure RemoteApp から Citrix XenApp Essentials に移行する方法

Citrix XenApp Essentials への移行を希望する Azure RemoteApp のお客様には、いくつかの前提条件があります。  最初に、Citrix の [Citrix XenApp Essentials のステップ バイ ステップのテクニカル デプロイ ガイド](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)と[オンライン テクニカル ライブラリ](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)を読むことをお勧めします。 

さいわいなことに、Azure RemoteApp で既に行った投資の大半は再利用できます。ただし、XenApp Essentials をデプロイするには高度な前提条件がいくつかあります。

## <a name="prerequisites"></a>前提条件

1. 新しい VNET を作成するか、Citrix XenApp Essentials のデプロイ先となる Azure Resource Manager 内の Azure VNET を決定します。 Azure RemoteApp では Azure クラシックを使用していますが、Citrix XenApp Essentials でサポートされているのは Azure Resource Manager のみです。  
2. Citrix はハイブリッド デプロイのみをサポートしているため、選択した VNET がドメイン コントローラーにネットワーク アクセスできるようにします。 Azure RemoteApp のクラウド デプロイを使用している場合は、VNET が Active Directory ドメイン コントローラーにネットワーク アクセスできるようにする必要があります。または、Azure Active Directory Domain Services (AAD-DS) を使用することをお勧めします。 
3. ドメイン参加が初回試行時に成功するように VNET の DNS が正しく構成されていることを確認します。 選択した VNET に仮想マシンを作成し、手動によるドメイン参加を実行して、DNS とドメイン参加が想定どおりに動作することを確認します。 これにより、初めて Citrix XenApp Essentials をデプロイするときに操作が成功します。 
4. 必要に応じて、Azure RemoteApp で使用している Azure クラシック VNET と Azure Resource Manager VNET が同じリージョンに存在する場合は、その VNET 間に VNET ピアリングを作成します。また、同じリージョンに存在しない場合は、ネットワーク用に VNET を接続するために S2S VPN を使用します。 
5. 必要に応じて、「[Azure RemoteApp を移行先または移行元とするデータの移行方法](remoteapp-migrate.md)」を参照してください。 
6. Citrix VDA コンポーネントを含むように既存の Azure RemoteApp イメージを更新し、手順について Citrix ドキュメントを参照します。 
7. Azure Marketplace に移動し、Citrix XenApp Essentials のデプロイを開始します。

Azure RemoteApp をご利用いただきありがとうございました。成功をお祈りしています。 

## <a name="other-considerations"></a>その他の考慮事項

- Citrix XenApp Essentials では、ハイブリッド デプロイのみがサポートされています。 つまり、ドメイン参加を実行するには、ドメイン コントローラーへのネットワーク アクセスが必要です。 Azure RemoteApp のクラウド デプロイを使用している場合は、AAD-DS を使用するか、VNET がドメイン参加のために Active Directory にアクセスできることを確認する必要があります。 
- ユーザー データを CXE に移動する方法については、「[Azure RemoteApp を移行先または移行元とするデータの移行方法](remoteapp-migrate.md)」を参照してください。 
- Citrix XenApp Essentials では、Active Directory アカウントのみがサポートされています。 Microsoft アカウント (@outlook.com、@msn.com、@hotmail.com など) はサポートされていません。 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>XenApp Essentials の課金について 

価格の詳細については、[FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) に関するページおよび [Citrix の概要記事](https://www.citrix.com/global-partners/microsoft/remote-app.html)を参照してください。 Citrix XenApp Essentials には、次の 3 つの課金コンポーネントがあります。

1. Citrix のサービス料金は、1 ユーザーあたり 12 ドル/月です。 Azure Marketplace でのすべての購入と同様に、これは、Azure サブスクリプションに関連付けられた支払い方法に課金されます。 EA のお客様の場合、Azure の通貨クレジットはご利用いただけません。 
2. RDS CAL。 RDS CAL (近日対応予定) を持ち込むか、Citrix XenApp Essentials の支払いにバンドルされているリモート アクセス料金 (RAF) を 6.25 米国ドルで購入する必要があります。 EA のお客様の場合、この支払いに Azure の通貨クレジットをご利用いただけます。 既存の RDS CAL の使用をご希望の場合は、お客様の課金内容に適用できるように [arainfo@microsoft.com](mailto:arainfo@microsoft.com) にご連絡ください。 
3. Azure のコンピューティングとストレージ。 これは、使用した VM に関する Azure のストレージ コストとコンピューティング使用量です。 VM サイズとユーザー密度を選択する際は価格に注意してください。 EA のお客様の場合、この支払いに Azure の通貨クレジットをご利用いただけます。

まだ不明な点がある場合は、お問い合わせください。
1. [arainfo@microsoft.com](mailto:arainfo@microsoft.com) に電子メールを送信する。
2. [Azure サポートに問い合わせる](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 まず、上記の手順 1. ～ 5. については、[Azure サポート ケースを開きます](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 手順 6. ～ 7. については、Citrix 管理ポータル内でサポート チケットを開いて Citrix に問い合わせてください。 

