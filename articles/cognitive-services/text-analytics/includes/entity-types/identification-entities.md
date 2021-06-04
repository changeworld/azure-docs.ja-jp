---
title: Identification エンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: a74c0cad971389168d643c9504f5bb809438a1ea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097553"
---
### <a name="financial-account-identification"></a>金融アカウント ID

このエンティティ カテゴリには、銀行関係の情報や公的な ID 形式が含まれます。

#### <a name="category-aba-routing-number"></a>カテゴリ: ABA ルーティング ナンバー

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        ABA ルーティング ナンバー

    :::column-end:::
    :::column span="2":::
        **詳細**

        米国銀行協会 (ABA) トランジット ルーティング ナンバー。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`ABARoutingNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ABARoutingNumber` も API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>カテゴリ: SWIFT コード

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        SWIFT コード

    :::column-end:::
    :::column span="2":::
        **詳細**

        支払指示情報の SWIFT コード。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`SWIFTCode` を `pii-categories` パラメーターに追加します。 検出されると、`SWIFTCode` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>カテゴリ: クレジット カード

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        クレジット カード

    :::column-end:::
    :::column span="2":::
        **詳細**

        クレジット カード番号。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`CreditCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CreditCardNumber` が API 応答で返されます。

    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>カテゴリ: IBAN (International Banking Account Number) 

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        クレジット カード

    :::column-end:::
    :::column span="2":::
        **詳細**

        支払指示情報の IBAN コード。 `domain=phi` でも返されます。

        このエンティティ カテゴリを取得するには、`InternationlBankingAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`InternationlBankingAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>政府機関と国または地域固有の識別情報

> [!NOTE]
> 次の財務エンティティと国または地域固有のエンティティは、`domain=phi` パラメーターでは返されません。
> * パスポート番号
> * 税 ID

以下のエンティティがグループ化され、国別に表示されます。

#### <a name="argentina"></a>アルゼンチン

:::row:::
    :::column span="":::
        **エンティティ**

        アルゼンチンの国民 ID (DNI) 番号 

    :::column-end:::
    :::column span="2":::
        **詳細** `domain=phi` でも返されます。
        
        このエンティティ カテゴリを取得するには、`ARNationalIdentityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ARNationalIdentityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>オーストリア

:::row:::
    :::column span="":::
        **エンティティ**

        オーストリアの身分証明書

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`ATIdentityCard` を `pii-categories` パラメーターに追加します。 検出されると、`ATIdentityCard` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストリアの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ATTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ATTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストリアの付加価値税 (VAT) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ATValueAddedTaxNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ATValueAddedTaxNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>オーストラリア

:::row:::
    :::column span="":::
        **エンティティ**

        オーストラリアの銀行口座番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`AUDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`AUDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアのビジネス番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`AUBusinessNumber` を `pii-categories` パラメーターに追加します。 検出されると、`AUBusinessNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの企業番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`AUCompanyNumber` を `pii-categories` パラメーターに追加します。 検出されると、`AUCompanyNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの運転免許証番号  

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`AUDriversLicense` を `pii-categories` パラメーターに追加します。 検出されると、`AUDriversLicense` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの医療口座番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`AUMedicalAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`AUMedicalAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアのパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ATPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ATPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ATTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ATTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>ベルギー

:::row:::
    :::column span="":::
        **エンティティ**

        ベルギーの国民番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`BENationalNumber` を `pii-categories` パラメーターに追加します。 検出されると、`BENationalNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ベルギーの付加価値税 (VAT) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`BEValueAddedTaxNumber` を `pii-categories` パラメーターに追加します。 検出されると、`BEValueAddedTaxNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>ブラジル 

:::row:::
    :::column span="":::
        **エンティティ**

        ブラジルの法人番号 (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`BRLegalEntityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`BRLegalEntityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ブラジルの CPF 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`BRCPFNumber` を `pii-categories` パラメーターに追加します。 検出されると、`BRCPFNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ブラジルの国民 ID カード (RG)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`BRNationalIDRG` を `pii-categories` パラメーターに追加します。 検出されると、`BRNationalIDRG` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>カナダ

:::row:::
    :::column span="":::
        **エンティティ**

        カナダの銀行口座番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`CABankAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CABankAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの運転免許証番号

    :::column-end:::

    :::column span="2":::

        このエンティティ カテゴリを取得するには、`CADriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CADriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの医療サービス番号

        
    :::column-end:::

    :::column span="2":::

        このエンティティ カテゴリを取得するには、`CAHealthServiceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CAHealthServiceNumber` が API 応答で返されます。
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダのパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`CAPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CAPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの個人保健識別番号 (PHIN)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`CAPersonalHealthIdentification` を `pii-categories` パラメーターに追加します。 検出されると、`CAPersonalHealthIdentification` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの社会保険番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`CASocialInsuranceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CASocialInsuranceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>チリ 

:::row:::
    :::column span="":::
        **エンティティ**

        チリの身分証明書番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`CLIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CLIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>中国

:::row:::
    :::column span="":::
        **エンティティ**

        中国の在留身分証明書 (PRC) 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`CNResidentIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CNResidentIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>欧州連合 (EU)

:::row:::
    :::column span="":::
        **エンティティ**

        EU デビット カード番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`EUDebitCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUDebitCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の運転免許証番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の GPU 座標

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUGPSCoordinates` を `pii-categories` パラメーターに追加します。 検出されると、`EUGPSCoordinates` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の国民識別番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUNationalIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUNationalIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU のパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の社会保障番号 (SSN) または同等の ID

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUSocialSecurityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUSocialSecurityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 納税者番号 (TIN)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`EUTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`EUTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>フランス

:::row:::
    :::column span="":::
        **エンティティ**

        フランスの運転免許証番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`FRDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの健康保険番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRHealthInsuranceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRHealthInsuranceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの国民 ID カード (CNI)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRNationalID` を `pii-categories` パラメーターに追加します。 検出されると、`FRNationalID` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスのパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの社会保障番号 (INSEE)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRSocialSecurityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRSocialSecurityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの納税者番号 (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの付加価値税 (VAT) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`FRValueAddedTaxNumber` を `pii-categories` パラメーターに追加します。 検出されると、`FRValueAddedTaxNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>ドイツ

:::row:::
    :::column span="":::
        **エンティティ**

        ドイツの運転免許証番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`DEDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DEDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの身分証明書番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`DEIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DEIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツのパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`DEPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DEPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`DETaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DETaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの付加価値税番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`DEValueAddedNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DEValueAddedNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>香港特別行政区

:::row:::
    :::column span="":::
        **エンティティ**

        香港特別行政区の身分証明書 (HKID) 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`HKIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`HKIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>ハンガリー

:::row:::
    :::column span="":::
        **エンティティ**

        ハンガリーの個人識別番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`HUPersonalIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`HUPersonalIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ハンガリーの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`HUTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`HUTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ハンガリーの付加価値税番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`HUValueAddedNumber` を `pii-categories` パラメーターに追加します。 検出されると、`HUValueAddedNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>インド

:::row:::
    :::column span="":::
        **エンティティ**

        インドの基本税務番号 (PAN)

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`INPermanentAccount` を `pii-categories` パラメーターに追加します。 検出されると、`INPermanentAccount` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        インドの一意識別 (Aadhaar) 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`INUniqueIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`INUniqueIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>インドネシア

:::row:::
    :::column span="":::
        **エンティティ**

        インドネシアの身分証明書 (KTP) 番号

    :::column-end:::
    :::column span="2":::

        **詳細**

        このエンティティ カテゴリを取得するには、`IDIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`IDIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>アイルランド

:::row:::
    :::column span="":::
        **エンティティ**

        アイルランドの個人公共サービス (PPS) 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`IEPersonalPublicServiceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`IEPersonalPublicServiceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        アイルランドの個人公共サービス (PPS) 番号 v2

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`IEPersonalPublicServiceNumberV2` を `pii-categories` パラメーターに追加します。 検出されると、`IEPersonalPublicServiceNumberV2` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>イスラエル

:::row:::
    :::column span="":::
        **エンティティ**

        イスラエルの国民 ID

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`ILNationalID` を `pii-categories` パラメーターに追加します。 検出されると、`ILNationalID` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イスラエルの銀行口座番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ILBankAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ILBankAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>イタリア

:::row:::
    :::column span="":::
        **エンティティ**

        イタリアの運転免許証 ID

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`ITDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ITDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イタリアの個人納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ITFiscalCode` を `pii-categories` パラメーターに追加します。 検出されると、`ITFiscalCode` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イタリアの付加価値税番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ITValueAddedTaxNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ITValueAddedTaxNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>日本

:::row:::
    :::column span="":::
        **エンティティ**

        日本の銀行口座番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`JPBankAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`JPBankAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の運転免許証番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`JPDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の "マイナンバー" (個人)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPMyNumberPersonal` を `pii-categories` パラメーターに追加します。 検出されると、`JPMyNumberPersonal` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の "マイナンバー" (企業)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPMyNumberCorporate` を `pii-categories` パラメーターに追加します。 検出されると、`JPMyNumberCorporate` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の住民票コード

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ITValueAddedTaxNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ITValueAddedTaxNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の在留カード番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPResidenceCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`JPResidenceCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の社会保険番号 (SIN)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPSocialInsuranceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`JPSocialInsuranceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本のパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`JPPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`JPPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>ルクセンブルク

:::row:::
    :::column span="":::
        **エンティティ**

        ルクセンブルグの国民識別番号 (自然人)

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`LUNationalIdentificationNumberNatural` を `pii-categories` パラメーターに追加します。 検出されると、`LUNationalIdentificationNumberNatural` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ルクセンブルグの国民識別番号 (自然人以外)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`LUNationalIdentificationNumberNonNatural` を `pii-categories` パラメーターに追加します。 検出されると、`LUNationalIdentificationNumberNonNatural` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>マルタ

:::row:::
    :::column span="":::
        **エンティティ**

        マルタの身分証明書番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`MTIdentityCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`MTIdentityCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        マルタの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`MTTaxIDNumber` を `pii-categories` パラメーターに追加します。 検出されると、`MTTaxIDNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>ニュージーランド

:::row:::
    :::column span="":::
        **エンティティ**

        ニュージーランドの銀行口座番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`NZBankAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`NZBankAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランドの運転免許証番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`NZDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`NZDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランドの内国税収入番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`NZInlandRevenueNumber` を `pii-categories` パラメーターに追加します。 検出されると、`NZInlandRevenueNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランド保健省 (Ministry of Health) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`NZMinistryOfHealthNumber` を `pii-categories` パラメーターに追加します。 検出されると、`NZMinistryOfHealthNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ニュージーランドの社会福祉番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`NZSocialWelfareNumber` を `pii-categories` パラメーターに追加します。 検出されると、`NZSocialWelfareNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>フィリピン

:::row:::
    :::column span="":::
        **エンティティ**

        フィリピンの多目的統一 ID 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`PHUnifiedMultiPurposeIDNumber` を `pii-categories` パラメーターに追加します。 検出されると、`PHUnifiedMultiPurposeIDNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>ポルトガル 

:::row:::
    :::column span="":::
        **エンティティ**

        ポルトガルの市民カード番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`PTCitizenCardNumber` を `pii-categories` パラメーターに追加します。 検出されると、`PTCitizenCardNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ポルトガルの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`PTTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`PTTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>シンガポール

:::row:::
    :::column span="":::
        **エンティティ**

        シンガポールの国民登録 ID カード (NRIC) 番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`PTTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`PTTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>南アフリカ

:::row:::
    :::column span="":::
        **エンティティ**

        南アフリカの識別番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`ZAIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ZAIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>韓国

:::row:::
    :::column span="":::
        **エンティティ**

        韓国の住民登録番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`KRResidentRegistrationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`KRResidentRegistrationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>スペイン

:::row:::
    :::column span="":::
        **エンティティ**

        スペインの DNI

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`ESDNI` を `pii-categories` パラメーターに追加します。 検出されると、`ESDNI` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スペインの社会保障番号 (SSN)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ESSocialSecurityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ESSocialSecurityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スペインの納税者番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`ESTaxIdentificationNumber` を `pii-categories` パラメーターに追加します。 検出されると、`ESTaxIdentificationNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>スイス

:::row:::
    :::column span="":::
        **エンティティ**

        スイスの社会保障番号 AHV

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`CHSocialSecurityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`CHSocialSecurityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>台湾 

:::row:::
    :::column span="":::
        **エンティティ**

        台湾の国民 ID

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`TWNationalID` を `pii-categories` パラメーターに追加します。 検出されると、`TWNationalID` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       台湾の在留証明書 (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`TWResidentCertificate` を `pii-categories` パラメーターに追加します。 検出されると、`TWResidentCertificate` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        台湾のパスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`TWPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`TWPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>イギリス

:::row:::
    :::column span="":::
        **エンティティ**

        英国 運転免許証番号

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`UKDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`UKDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       英国 選挙人名簿番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`UKNationalInsuranceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`UKNationalInsuranceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国民健康保険 (NHS) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`UKNationalHealthNumber` を `pii-categories` パラメーターに追加します。 検出されると、`UKNationalHealthNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国民保険番号 (NINO)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`UKNationalInsuranceNumber` を `pii-categories` パラメーターに追加します。 検出されると、`UKNationalInsuranceNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 または米国パスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`USUKPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`USUKPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 納税者参照番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`UKUniqueTaxpayerNumber` を `pii-categories` パラメーターに追加します。 検出されると、`UKUniqueTaxpayerNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>United States

:::row:::
    :::column span="":::
        **エンティティ**

        米国社会保障番号 (SSN)

    :::column-end:::
    :::column span="2":::
        **詳細**

        このエンティティ カテゴリを取得するには、`USSocialSecurityNumber` を `pii-categories` パラメーターに追加します。 検出されると、`USSocialSecurityNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::
      **サポートされているドキュメントの言語**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国運転免許証番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`USDriversLicenseNumber` を `pii-categories` パラメーターに追加します。 検出されると、`USDriversLicenseNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国または英国 パスポート番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`USUKPassportNumber` を `pii-categories` パラメーターに追加します。 検出されると、`USUKPassportNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国米国の個人納税者番号 (ITIN)

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`USIndividualTaxpayerIdentification` を `pii-categories` パラメーターに追加します。 検出されると、`USIndividualTaxpayerIdentification` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国麻薬取締局 (DEA) 番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`DrugEnforcementAgencyNumber` を `pii-categories` パラメーターに追加します。 検出されると、`DrugEnforcementAgencyNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国銀行口座番号

    :::column-end:::
    :::column span="2":::

        このエンティティ カテゴリを取得するには、`USBankAccountNumber` を `pii-categories` パラメーターに追加します。 検出されると、`USBankAccountNumber` が API 応答で返されます。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
