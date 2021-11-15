---
title: 利用可能なリージョンとデータの保存場所
titleSuffix: Azure AD B2C
description: 利用可能なリージョン、データ所在地、高可用性、SLA、Azure Active Directory B2C プレビュー テナントに関する情報。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 52fc2cc1e9658ef63b331d7d3983fdc47d13489e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424422"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C:利用可能なリージョンとデータの保存場所

Azure AD B2C ID データは、テナントの作成時に指定された国/リージョンに基づく地理的場所に格納されます。

利用可能なリージョンとデータの保存場所は、Azure AD B2C に適用される 2 つの異なる概念です。 この記事では、これら 2 つの概念の違いについて説明するとともに、Azure と Azure AD B2C に対してそれらがどのように適用されるかを比較します。

Azure AD B2C は **世界中で一般提供** されており、**データの保存場所** としては **米国、ヨーロッパ、アジア太平洋、またはオーストラリア** を選択できます。

[利用可能なリージョン](#region-availability)とは、サービスを使用できる場所を指します。

[データの保存場所](#data-residency)とは、ユーザー データがどこに保存されるかを示します。

## <a name="region-availability"></a>利用可能なリージョン

Azure AD B2C は、Azure パブリック クラウドを介して全世界で提供されています。 このサービスが利用できるかどうかは、Azure の「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」ページと [Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)に関するページで確認できます。 また、Azure AD B2C は高可用性のサービスです。 詳細については、[Azure Active Directory B2C のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/active-directory-b2c/v1_1) を参照してください。
## <a name="data-residency"></a>データの保存場所

Azure AD B2C では、ユーザー データが米国、ヨーロッパ、アジア太平洋のリージョン、またはオーストラリアに保存されます。

データの保存場所は、[Azure AD B2C のテナントを作成する](tutorial-create-tenant.md)ときに選択した国/地域によって決まります。

![[テナントの作成] フォームのスクリーンショット ([国またはリージョン] の選択)。](./media/data-residency/data-residency-b2c-tenant.png)

次の国/地域の場合、データは **米国** に保存されます。

> 米国 (US)、カナダ (CA)、コスタリカ (CR)、ドミニカ共和国 (DO)、エルサルバドル (SV)、グアテマラ (GT)、メキシコ (MX)、パナマ (PA)、プエルトリコ (PR)、トリニダード・トバゴ (TT)

次の国/地域の場合、データは **ヨーロッパ** に保存されます。

> アルジェリア (DZ)、オーストリア (AT)、アゼルバイジャン (AZ)、バーレーン (BH)、ベラルーシ (BY)、ベルギー (BE)、ブルガリア (BG)、クロアチア (HR)、キプロス (CY)、チェコ共和国 (CZ)、デンマーク (DK)、エジプト (EG)、エストニア (EE)、フィンランド (FT)、フランス (FR)、ドイツ (DE)、ギリシャ (GR)、ハンガリー (HU)、アイスランド (IS)、アイルランド (IE)、イスラエル (IL)、イタリア (IT)、ヨルダン (JO)、カザフスタン (KZ)、ケニア (KE)、クウェート (KW)、ラトビア (LV)、レバノン (LB)、リヒテンシュタイン (LI)、リトアニア (LT)、ルクセンブルク (LU)、北マケドニア (ML)、マルタ (MT)、モンテネグロ (ME)、モロッコ (MA)、オランダ (NL)、ナイジェリア (NG)、ノルウェー (NO)、オマーン (OM)、パキスタン (PK)、ポーランド (PL)、ポルトガル (PT)、カタール (QA)、ルーマニア (RO)、ロシア (RU)、サウジアラビア (SA)、セルビア (RS)、スロバキア (SK)、スロベニア (ST)、南アフリカ (ZA)、スペイン (ES)、スウェーデン (SE)、スイス (CH)、チュニジア (TN)、トルコ (TR)、ウクライナ (UA)、アラブ首長国連邦 (AE)、英国 (GB)

次の国/地域の場合、データは **アジア太平洋** に保存されます。

> アフガニスタン (AF)、香港特別行政区 (HK)、インド (IN)、インドネシア (ID)、日本 (日本)、韓国 (韓国)、マレーシア (MY)、フィリピン (PH)、シンガポール (SG)、スリランカ (LK)、台湾 (TW)、タイ (TH)

次の国/地域の場合、データは **オーストラリア** に保存されます。

> オーストラリアとニュージーランド

以下の国/地域については今後追加されていく予定です。 差し当たって、上記のいずれかの国/地域を選ぶことによって Azure AD B2C を使用することができます。

> アルゼンチン、ブラジル、チリ、コロンビア、エクアドル、イラク、パラグアイ、ペルー、ウルグアイ、ベネズエラ

## <a name="remote-profile-solution"></a>リモート プロファイルのソリューション

Azure AD B2C の[カスタム ポリシー](custom-policy-overview.md)を使用すると、[RESTful API サービス](api-connectors-overview.md)と統合できます。これにより、リモート データベース (マーケティング データベース、CRM システム、または任意の基幹業務アプリケーションなど) からユーザー プロファイルを格納して読み取ることができます。  
- サインアップとプロファイル編集のフロー中に、Azure AD B2C からカスタム REST API が呼び出され、ユーザー プロファイルをリモート データ ソースに保持します。 ユーザーの資格情報は、Azure AD B2C ディレクトリに格納されます。 
- サインイン時に、ローカルまたはソーシャル アカウントを使用して資格情報を検証した後、Azure AD B2C によって REST API が呼び出されます。これにより、ユーザーの一意の識別子がユーザーのプライマリ キー (電子メール アドレスまたはユーザーの objectId) として送信されます。 REST API によって、リモート データベースからデータが読み取られ、ユーザー プロファイルが返されます。  

サインアップ、プロファイルの編集、またはサインインを完了すると、Azure AD B2C のアクセス トークンにはユーザー プロファイルが含まれ、これがアプリケーションに返されます。 詳細については、GitHub の [Azure AD B2C リモート プロファイルのサンプル ソリューション](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure AD B2C テナントを作成](tutorial-create-tenant.md)します。
