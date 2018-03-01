---
title: "Azure AD Connect Health と一般データ保護規則 | Microsoft Docs"
description: "このドキュメントでは、Azure AD Connect で GDPR コンプライアンスを取得する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>GDPR コンプライアンスと Azure AD Connect Health 

[一般データ保護規則 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm)は、欧州連合 (EU) のデータ保護およびプライバシー保護法です。 GDPR は、EU 内で人々に製品やサービスを提供したり、EU 居住者に関連するデータを収集および分析したりする企業、政府機関、非営利組織やその他の組織を対象とする新しいルールです。 

Microsoft では、GDPR の要件を満たすのに役立つ製品やサービスを提供しています。 Microsoft のプライバシー ポリシーについては、[セキュリティ センター](https://www.microsoft.com/trustcenter)をご覧ください。

Azure AD Connect Health は、オンプレミスの ID インフラストラクチャと同期サービスを監視します。 また、インサイトやサーフェスのアラートも提供します。 Microsoft は、2018 年に施行される GDPR にすべてのクラウド サービスを準拠させ、契約上のコミットメントにおいて、GDPR 関連の保証を提供します。 

>[!NOTE] 
> この記事では、Azure AD Connect Health での GDPR コンプライアンスについて説明します。 Azure AD Connect での GDPR コンプライアンスについては、「[GDPR compliance and Azure AD Connect (GDPR コンプライアンスと Azure AD Connect)](../../active-directory/connect/active-directory-aadconnect-gdpr.md)」をご覧ください。

## <a name="gdpr-classification"></a>GDPR の分類
Azure AD Connect Health は、GDPR の**データ プロセッサ** カテゴリに分類されます。 このサービスは、データ プロセッサ パイプラインとして、重要なパートナーやエンド コンシューマーにデータ処理サービスを提供するものです。 Azure AD Connect Health は、ユーザー データを生成するものではなく、どのような個人データが収集されるかや、それらのデータの用途について、独立した制御を持つものでもありません。 Azure AD Connect Health でのデータの取得、集計、分析、およびレポートは、既存のオンプレミス データに基づいて行われます。 

## <a name="data-retention-policy"></a>データ リテンション期間ポリシー
Azure AD Connect Health では、レポートの生成、分析の実行、またはインサイトの提供を、30 日間を超えて行うことはありません。 そのため Azure AD Connect Health では、いかなるデータも 30 日間を超えて格納、処理、保持されることはありません。 この設計は、GDPR の規制、Microsoft のプライバシー コンプライアンス規則、および Azure AD のデータ リテンション ポリシーに準拠したものです。 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health でのデータの収集と 監視を無効にする
Azure AD Connect Health では、各監視対象サーバーや、監視対象サービスのインスタンスについて、データ収集を停止させることができます。 たとえば、Azure AD Connect Health を使用して監視されている個々の AD FS (Active Directory フェデレーション サービス) サーバーについて、データ収集を停止することもできます。 また、Azure AD Connect Health を使用して監視されている ADFS インスタンス全体について、データ収集を停止することもできます。 これを行った場合は、データ収集が停止された後に、対応するサーバーが Azure AD Connect Health ポータルから削除されます。 

>[!IMPORTANT]
> Azure AD Connect Health から監視対象サーバーを削除するには、Azure AD グローバル管理者特権か、RBAC の共同作成者ロールが必要です。
>
> Azure AD Connect Health からサーバーまたはサービス インスタンスを削除した場合、その操作を元に戻すことはできません。 

### <a name="what-to-expect"></a>予期される事柄
個々の監視対象サーバー、または監視対象サービスのインスタンスについてデータの収集と監視を停止する場合には、次のことに注意してください。

- 監視対象サービスのインスタンスを削除すると、そのインスタンスは、ポータルにある Azure AD Connect Health 監視サービスの一覧から削除されます。 
- 監視対象サーバー、または監視対象サービスのインスタンスを削除しても、正常性エージェントがサーバーからアンインストールされたり、削除されることはありません。 正常性エージェントは、Azure AD Connect Health にデータを送信しないように構成されています。 ユーザーは、以前の監視対象サーバーから正常性エージェントを手動でアンインストールする必要があります。
- Health エージェントをアンインストールせずにこの手順を実行した場合、Health エージェントに関連したエラー イベントがそのサーバー上に表示されます。
- 監視対象サービスのインスタンスに属するすべてのデータは、Microsoft Azure のデータ リテンション期間ポリシーに従って削除されます。

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>監視対象サーバーのデータ収集と監視を無効にする
「[Azure AD Connect Health サービスからサーバーを削除するには](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service)」をご覧ください。

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>監視対象サービスのインスタンスのデータ収集と監視を無効にする
「[Azure AD Connect Health サービスからのサービス インスタンスの削除](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)」をご覧ください。


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Azure AD Connect Health でのデータの収集と 監視を再度有効にする
以前に削除された監視対象サービスについて Azure AD Connect Health での監視を再度有効にするには、正常性エージェントをアンインストールし、すべてのサーバーに[正常性エージェントを再インストールする](active-directory-aadconnect-health-agent-install.md)必要があります。


## <a name="next-steps"></a>次の手順
* [セキュリティ センターで Microsoft のプライバシー ポリシーを確認する](https://www.microsoft.com/trustcenter)
* [Azure AD Connect と GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
