---
title: 個人情報の名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799968"
---
## <a name="personal-information-entity-types"></a>個人情報のエンティティ型:

### <a name="phone-number"></a>電話番号

電話番号。 

言語:

* パブリック プレビュー: `English`

| サブタイプ名           | 説明                                           |
|------------------------|-------------------------------------------------------|
| 該当なし                    | 電話番号 (例: `+1 123-123-123`)。          |
| EU の電話番号        | 欧州連合に固有の電話番号。         |
| EU の携帯電話番号 | 欧州連合に固有の携帯電話番号。 |

### <a name="eu-gps-coordinates"></a>EU の GPS 座標

 欧州連合内の場所の GPS 座標。 

言語:

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                               |
|--------------|-------------------------------------------|
| 該当なし          | 欧州連合内の GPS 座標 |

### <a name="azure-information"></a>Azure の情報

認証情報、接続文字列などの特定可能な Azure 情報。 

言語:

* パブリック プレビュー: `English`

| サブタイプ名                          | 説明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 認証キー             | Azure DocumentDB サーバーの認証キー。                           |
| Azure IAAS データベース接続文字列 | Azure サービスとしてのインフラストラクチャ (IaaS) データベースの接続文字列。 |
| Azure SQL 接続文字列           | Azure SQL データベースの接続文字列。                                |
| Azure IoT 接続文字列           | Azure モノのインターネット (IoT) の接続文字列。                        |
| Azure 発行設定パスワード        | Azure 発行設定のパスワード。                                        |
| Azure Redis Cache 接続文字列   | Azure Cache for Redis の接続文字列。                             |
| Azure SAS                             | Azure サービスとしてのソフトウェア (SaaS) の接続文字列。                     |
| Azure Service Bus 接続文字列   | Azure Service Bus の接続文字列。                                |
| Azure Storage アカウント キー             | Azure Storage アカウントのアカウント キー。                                   |
| Azure Storage アカウント キー (汎用)   | Azure Storage アカウントの汎用アカウント キー。                           |
| SQL Server 接続文字列          | SQL Server の接続文字列。                                         |

### <a name="identification"></a>識別

言語:

* パブリック プレビュー: `English`

#### <a name="financial-account-identification"></a>金融アカウント ID

| サブタイプ名               | 説明                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA ルーティング ナンバー        | 米国銀行協会 (ABA) トランジット ルーティング ナンバー。                  |
| SWIFT コード                 | 支払指示情報の SWIFT コード。                           |
| クレジット カード                | クレジット カード番号。                                                       |
| IBAN コード                  | 支払指示情報の IBAN コード。                            |

#### <a name="government-and-country-specific-identification"></a>政府および国に固有の ID

以下のエンティティがグループ化され、国別に表示されます。

アルゼンチン
* 国民識別 (DNI) 番号

オーストラリア
* 納税者番号 
* 運転免許証 ID
* パスポート ID
* 医療アカウント番号
* 銀行口座番号 (当座預金口座、普通預金口座、引落口座など)

ベルギー
* 国内番号

ブラジル
* 法人番号 (CNPJ)
* CPF 番号
* 国が発行する身分証明書 (RG)

カナダ
* パスポート ID
* 運転免許証 ID
* 健康保険番号
* 個人医療 ID 番号 (PHIN)
* 社会保障番号
* 銀行口座番号 (当座預金口座、普通預金口座、引落口座など)

チリ
* 身分証明書番号 

中国
* 身分証明書番号
* 住民登録証明書 (PRC) 番号

クロアチア
* 身分証明書番号
* 個人 ID (OIB) 番号

チェコ共和国
* 国が発行する身分証明書番号

デンマーク
* 個人 ID 番号

欧州連合 (EU)
* 国民識別番号
* パスポート ID
* 運転免許証 ID
* 社会保障番号 (SSN) または同等の ID
* EU 納税者番号 (TIN)
* EU デビット カード番号

フィンランド
* 国民識別番号
* パスポート ID

フランス
* 国が発行する身分証明書 (CNI)
* 社会保障番号 (INSEE)
* パスポート ID
* 運転免許証 ID

ドイツ
* 身分証明書番号
* パスポート ID
* 運転免許証 ID

ギリシャ 
* 国が発行する身分証明書番号

香港特別行政区
* 身分証明書 (HKID) 番号

インド
* 永続的なアカウント番号 (PAN)
* 固有 ID (Aadhaar) 番号

インドネシア
* 身分証明書番号 (KTP)

アイルランド
* パーソナル パブリック サービス (PPS) 番号

イスラエル
* 国内 ID
* 銀行口座番号 (当座預金口座、普通預金口座、引落口座など)

イタリア
* 運転免許証 ID

日本
* 住民登録番号
* 在留カード番号
* 運転免許証 ID
* 社会保険番号 (SIN)
* パスポート ID
* 銀行口座番号 (当座預金口座、普通預金口座、引落口座など)

マレーシア
* 身分証明書番号

オランダ
* 市民サービス (BSN) 番号

ニュージーランド
* 保健省番号

ノルウェー
* 身分証明書番号

フィリピン
* UMID (Unified Multi-Purpose ID) 番号

ポーランド
* 身分証明書番号
* 国内 ID (PESEL)
* パスポート ID

ポルトガル 
* 市民カード番号

サウジアラビア
* 国内 ID

シンガポール
* 国民登録身分証明書 (NRIC) 番号

南アフリカ
* ID 番号
* 住民登録番号

韓国
* 住民登録番号

スペイン 
* 社会保障番号 (SSN)

スウェーデン
* 国内 ID
* パスポート ID

台湾 
* 国内 ID
* 居住証明書 (ARC/TARC) 番号
* パスポート ID

タイ
* Population ID コード

イギリス
* パスポート ID
* 運転免許証 ID
* 国民保険番号 (NINO)
* 国民健康保険 (NHS) 番号

米国
* 社会保障番号 (SSN)
* 運転免許証 ID
* パスポート ID
* 選挙人名簿番号
* 個人納税者識別番号 (ITIN)
* 麻薬取締局 (DEA) 番号
* 銀行口座番号 (当座預金口座、普通預金口座、引落口座など)
