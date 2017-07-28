---

title: "Azure RemoteApp から Citrix XenApp Essentials に移行する | Microsoft Docs"
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: ja-jp
ms.lasthandoff: 06/02/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Azure RemoteApp から Citrix XenApp Essentials に移行する

現在使用している Azure RemoteApp を Citrix XenApp Essentials に移行する場合、いくつかの条件を満たす必要があります。 最初に、Citrix の [Citrix XenApp Essentials のステップ バイ ステップのテクニカル デプロイ ガイド](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)と[オンライン テクニカル ライブラリ](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)をお読みください。 

## <a name="prerequisite-steps-for-migration"></a>移行に必要な手順

1. 新しい仮想ネットワークを作成するか、Citrix XenApp Essentials のデプロイ先となる Azure Resource Manager 内の Azure 仮想ネットワークを決定します。 Azure RemoteApp では Azure クラシック ポータルを使用していますが、Citrix XenApp Essentials でサポートされているのは Azure Resource Manager のみです。  
2. Citrix でサポートされるのはハイブリッド デプロイのみであるため、選択した仮想ネットワークがドメイン コントローラーにネットワーク アクセスできるようにします。 Azure RemoteApp のクラウド デプロイを使用している場合は、ご利用の仮想ネットワークから、Active Directory ドメイン コントローラーにネットワークでアクセスできることを確認してください。 Azure Active Directory Domain Services (Azure AD DS) を使用することもできます。 
3. ドメイン参加が初回試行時に成功するように仮想ネットワークの DNS が正しく構成されていることを確認します。 選択した仮想ネットワークに仮想マシン (VM) を作成し、手動によるドメイン参加を実行して、DNS とドメイン参加が想定どおりに動作することを確認します。 これにより、初めて Citrix XenApp Essentials をデプロイするときに操作が成功します。 
4. Azure RemoteApp で使用している Azure クラシック ポータルの仮想ネットワークと、Azure Resource Manager の仮想ネットワークとの間に、必要に応じて仮想ネットワーク ピアリングを作成します。 このピアリング処理が正常に機能するためには、2 つのネットワークが同じリージョンに存在する必要があります。 同じリージョンに存在しない場合は、サイト間 VPN を使用してそれらの仮想ネットワークを接続してください。 
5. 必要に応じて、「[Azure RemoteApp を移行先または移行元とするデータの移行方法](remoteapp-migrate.md)」を参照してください。 
6. Citrix VDA コンポーネントを含むように既存の Azure RemoteApp イメージを更新します (手順については Citrix のドキュメントを参照)。 
7. Azure Marketplace に移動し、Citrix XenApp Essentials のデプロイを開始します。

## <a name="other-considerations"></a>その他の考慮事項

移行作業を行うときは、次の点にも注意してください。
- Citrix XenApp Essentials では、ハイブリッド デプロイのみがサポートされています。 つまり、ドメイン参加を実行するには、ドメイン コントローラーへのネットワーク アクセスが必要です。 Azure RemoteApp のクラウド デプロイを使用している場合は、Azure AD DS を使用するか、仮想ネットワークがドメイン参加のために Active Directory にアクセスできることを確認する必要があります。 
- ユーザー データを Citrix XenApp Essentials に移動する方法については、「[Azure RemoteApp を移行先または移行元とするデータの移行方法](remoteapp-migrate.md)」を参照してください。 
- Citrix XenApp Essentials では、Active Directory アカウントのみがサポートされています。 Microsoft アカウント (outlook.com、msn.com、hotmail.com など) はサポートされません。 

## <a name="citrix-xenapp-essentials-billing"></a>Citrix XenApp Essentials の料金

価格の詳細については、[FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) に関するページと [Citrix の概要記事](https://www.citrix.com/global-partners/microsoft/remote-app.html)を参照してください。 Citrix XenApp Essentials には、次の 3 つの課金コンポーネントがあります。

- Citrix のサービス料金 (1 ユーザーあたり 12 ドル/月)。 Azure Marketplace でのすべての購入と同様に、これは、Azure サブスクリプションに関連付けられた支払い方法に課金されます。 Enterprise Agreement (EA) のお客様の場合、Azure の通貨クレジットはご利用いただけません。 
- Remote Data Services (RDS) クライアント アクセス ライセンス (CAL)。 現在、Citrix XenApp Essentials の支払いにバンドルされているリモート アクセス料金を 6.25 ドルで購入することができます。 EA のお客様の場合、この支払いに Azure の通貨クレジットをご利用いただけます。 既存の RDS CAL の使用をご希望の場合は、お客様の課金内容に適用できるように [arainfo@microsoft.com](mailto:arainfo@microsoft.com) にご連絡ください。 
- Azure のコンピューティングとストレージ。 これは、使用した VM に関する Azure のストレージ コストとコンピューティング使用量です。 VM サイズとユーザー密度を選択する際は価格に注意してください。 EA のお客様の場合、この支払いに Azure の通貨クレジットをご利用いただけます。

その他の質問がある場合
- [arainfo@microsoft.com](mailto:arainfo@microsoft.com) に電子メールを送信する。
- [Azure サポートに問い合わせる](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 まず、必要な手順 1. ～ 5. については、[Azure サポート ケースを開きます](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 手順 6. ～ 7. については、Citrix 管理ポータル内でサポート チケットを開いて Citrix に問い合わせてください。 

