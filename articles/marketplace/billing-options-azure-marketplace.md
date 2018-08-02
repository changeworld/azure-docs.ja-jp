---
title: Azure Marketplace の課金オプション | Azure
description: Azure Marketplace の公開元向け課金オプション。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: d0dbf603c600639679e121723556a7f3ceb17e37
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158983"
---
# <a name="billing-options-in-the-azure-marketplace"></a>Azure Marketplace の課金オプション

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com) で利用できる課金オプションについて説明します。

## <a name="commercial-considerations-in-the-marketplace"></a>Marketplace での販売に関する考慮事項
Marketplace では、次の登録リストの種類については収益が折半されません。 
*   List
*   試用版
*   ライセンス持ち込み (BYOL) 課金モデルを使用したトランザクション

Marketplace のネットショップへは追加料金なしで参加できます。

詳細については、[Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies)に関するページをご覧ください。  

## <a name="pay-as-you-go-and-byol-billing-options"></a>従量課金制と BYOL の課金オプション
従量課金制課金モデルを公開オプションとして使用すると、公開元の使用量ベースのソフトウェア ライセンスの収益は、公開元と Microsoft の間で 8 対 2 の割合で折半されます。 1 つのプランに対して、従量課金制と BYOL の両方の課金モデルを使用して課金することができます。 2 つの課金モデルは、プラン レベルで別個の SKU として共存します。 課金モデルは、Cloud パートナー ポータルのプラン内で構成します。 

次の例を考えてみます。
*   従量課金制オプションを有効にした場合、結果は次のようになります。
    | ライセンス コスト | 1.00 ドル/時間 |
    |:--- |:--- |
    | Azure 使用コスト (D1/1 コア) | 0.14 ドル/時間 |
    | *顧客は Microsoft から請求されます* | *1.14 ドル/時間* |

    このシナリオでは、Microsoft は公開された VM イメージの使用に対して 1 時間あたり 1.14 ドルを請求します。
    | Microsoft からの請求額 | 1.14 ドル/時間 |
    |:--- |:--- |
    | Microsoft は、ライセンス コストの 80% をパブリッシャーに支払います | 0.80 ドル/時間 |
    | Microsoft は、ライセンス コストの 20% を受け取ります | 0.20 ドル/時間 |
    | Microsoft は、Azure の使用コストを受け取ります | 0.14 ドル/時間 |

*   BYOL オプションを有効にした場合、結果は次のようになります。
    | ライセンス コスト | ライセンス料金は公開元によって交渉および請求されます |
    |:--- |:--- |
    | Azure 使用コスト (D1/1 コア) | 0.14 ドル/時間 |
    | *顧客は Microsoft から請求されます* | *0.14 ドル/時間* |

    このシナリオでは、Microsoft は公開された VM イメージの使用に対して 1 時間あたり 0.14 ドルを請求します。 
    | Microsoft からの請求額 | 0.14 ドル/時間 |
    |:--- |:--- |
    | Microsoft は、Azure の使用コストを受け取ります | 0.14 ドル/時間 |
    | Microsoft は、ライセンス コストの 0% を受け取ります | 0.00 ドル/時間 |

## <a name="single-billing-and-payment-methods"></a>一括請求および支払い方法
Marketplace のトランザクション公開オプションを使用する重要な利点は、ライセンス コストと Azure の使用量が一括で公開元の顧客に直接請求される点です。

このシナリオでは、Microsoft が公開元の代わりに請求と徴収を行います。 Microsoft の請求方法を使用すると、公開元は独自に顧客と調達関係を築く必要がなくなります。 一括請求により、時間とリソースを節約することができます。 また、公開元は料金の徴収ではなく売上の獲得に集中できます。 

## <a name="enterprise-agreement"></a>Enterprise Agreement  
公開元が Microsoft との間で Enterprise Agreement を結んでいる場合、Microsoft 製品の料金の支払いに Enterprise Agreement を使用できます。 Azure の使用料を含めて製品代金の請求を受けることができます。 Enterprise Agreement を使用した支払いは、ソフトウェアおよびクラウド サービスのライセンスを 3 年間以上供与したい組織が対象となります。 公開元は、 1 回で前払いする代わりに、支払いを分割できます。 従量課金制の公開オプションをご使用の場合は、ソフトウェア ライセンス コストの請求は、四半期ごとの Enterprise Agreement 超過請求サイクルに従います。  

### <a name="monetary-commitment"></a>年額コミットメント
Enterprise Agreement のお客様の場合は、ご契約に Azure を追加することができます。 Azure をご契約に追加するには、Azure に対して前払いによる年額コミットメントを行う必要があります。 年額コミットメント分は、その年を通して使用されます。 コミットメントでは、Azure サービスを任意に組み合わせて使用することができます。

## <a name="next-steps"></a>次の手順
「[Azure Marketplace と AppSource のパブリッシャー ガイド](./marketplace-publishers-guide.md)」をご覧ください。
