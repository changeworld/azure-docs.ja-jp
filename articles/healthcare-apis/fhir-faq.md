---
title: Azure の FHIR サービスについての FAQ - Azure API for FHIR
description: FHIR API におけるデータの保存場所やバージョン サポートなど、Azure API for FHIR についてよく寄せられる質問とその回答を示します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870982"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Azure API for FHIR についてよく寄せられる質問

## <a name="what-is-fhir"></a>FHIR とは何でしょうか?
高速ヘルスケア相互運用性リソース (Fast Healthcare Interoperability Resources) は、略して FHIR ("ファイア" と発音) と呼ばれ、さまざまな医療システム間で医療データの交換を可能にする相互運用性標準です。 この標準は HL7 組織によって策定され、世界各国の医療機関で採用されています。 FHIR の最新バージョンは R4 (Release 4) です。 Azure API for FHIR では R4 がサポートされるほか、以前のバージョンの STU3 (Standard for Trial Use 3) もサポートされています。 FHIR の詳細については、[HL7.org](http://hl7.org/fhir/summary.html) のページを参照してください。

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API のデータは Azure に格納されるのですか?

はい。データは Azure のマネージド データベースに格納されます。 Azure API for FHIR は、基になるデータ ストアへの直接アクセスを提供していません。

## <a name="what-identity-provider-do-you-support"></a>どの ID プロバイダーがサポートされますか?

現在当社は、ID プロバイダーとして Microsoft Azure Active Directory をサポートしています。

## <a name="what-fhir-version-do-you-support"></a>どのバージョンの FHIR がサポートされますか?

Azure API for FHIR (PaaS) と FHIR Server for Azure (オープンソース) では、どちらもバージョン 4.0.0 と 3.0.1 がサポートされます。

詳細については、「[サポートされている機能](fhir-features-supported.md)」を参照してください。 バージョン間の変更点については、[HL7 FHIR のバージョン履歴](https://hl7.org/fhir/R4/history.html)を参照してください。

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>オープン ソースの Microsoft FHIR Server for Azure と Azure API for FHIR の違いは何ですか?

Azure API for FHIR は、オープンソースの Microsoft FHIR Server for Azure サーバーのホスト マネージド バージョンです。 マネージド サービスでは、メンテナンスや更新プログラムがすべて Microsoft によって提供されます。 

FHIR Server for Azure を実行している場合、基になるサービスに直接アクセスすることができます。 ただし、サーバーのメンテナンスや更新に加え、PHI データを格納する場合は必要なコンプライアンス作業もすべて自分で行う必要があります。

開発の観点から言うと、すべての機能はまず、オープンソースの Microsoft FHIR Server for Azure にデプロイされます。 オープンソースでの検証後、PaaS の Azure API for FHIR ソリューションにリリースされます。 オープンソース リリースから PaaS リリースまでの時間は、機能の複雑さやその他ロードマップ上の優先度によって異なります。 

## <a name="what-is-smart-on-fhir"></a>SMART on FHIR とは何でしょうか?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR は、FHIR サーバーやその他の医療 IT システム (電子カルテ、医療情報交換など) にパートナー アプリケーションを統合するための一連のオープン仕様です。 SMART on FHIR アプリケーションを作成することにより、多数の異なるシステムから確実にそのアプリケーションにアクセスして活用することができます。
認証と Azure API for FHIR。 SMART の詳細については、[SMART Health IT](https://smarthealthit.org/) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR についてよく寄せられる質問のいくつかを確認しました。 FHIR Server for Azure のサポートされている機能について確認してください。
 
>[!div class="nextstepaction"]
>[サポートされる FHIR 機能](fhir-features-supported.md)