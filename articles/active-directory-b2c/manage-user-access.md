---
title: Azure Active Directory B2C のユーザー アクセスの管理 | Microsoft Docs
description: Active Directory B2C を使用して、未成年者の識別、生年月日および国のデータの収集、アプリケーションの利用規約への承諾の取得を行う方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 52cf34d56f87d2543fb272ce99dbe011bc0ea037
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711129"
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Active Directory B2C におけるユーザー アクセスの管理

この記事では、Azure Active Directory (AD) B2C を使用したアプリケーションへのユーザー アクセスの管理方法に関する情報を提供します。 アプリケーションのアクセス管理には次のものが含まれます。

- 未成年者を識別し、アプリケーションを使用するためのアクセスを制御する
- 未成年者がアプリケーションを使用できるように保護者の同意を求める
- ユーザーから生年月日および国データを収集する
- 利用規約への同意を取得し、アクセスを制御する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>この記事では、GDPR での義務を果たすために使用できる情報を提供します。 GDPR に関する全般情報については、[Service Trust ポータルの GDPR に関するセクション](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)をご覧ください。

## <a name="control-minor-access"></a>未成年者のアクセスを制御する

アプリケーションおよび組織は、未成年者を対象としないアプリケーションとサービスを未成年者が使用しないようにすることができます。 または、未成年者を受け入れたうえで、保護者の同意を管理し、ビジネス ルールによる指示と法令による許可に従って、許容されるエクスペリエンスを提供することもできます。 

ユーザーが未成年者であると識別される場合に、Active Directory B2C のユーザー フローを 3 つのオプションのいずれかに設定できます。

- **署名済み JWT id_token をアプリケーションに返送する** - ユーザーがディレクトリに登録され、トークンがアプリケーションに返されます。 アプリケーションはその後、ビジネス ルールに従って進行します。 たとえば、アプリケーションは、保護者の同意プロセスに進むことができます。 そのためには、アプリケーションから **ageGroup** と **consentProvidedForMinor** の要求を受け取ります。
- **無署名の JSON トークンをアプリケーションに送信する** - Active Directory B2C は、ユーザーが未成年者であることをアプリケーションに通知し、ユーザーの保護者の同意の状態を提供します。 アプリケーションはその後、ビジネス ルールに従って進行します。 JSON トークンだけでは、アプリケーションの認証は成功しません。 アプリケーションは、**name**、**email**、**ageGroup**、**consentProvidedForMinor** などが格納されている JSON トークンに含まれる要求に従って、未認証のユーザーを処理する必要があります。
- **ユーザーをブロックする** - ユーザーが未成年者で、保護者の同意を得られない場合。  Active Directory B2C は、ブロックされていることを通知する画面をユーザーに表示できます。  登録プロセスでトークンが発行されず、アクセスがブロックされ、ユーザー アカウントは作成されません。 これを実装するには、適切な HTML/CSS コンテンツ ページを提供して、ユーザーに通知を行い、適切なオプションを提示します。 新しい登録については、アプリケーションによるそれ以上のアクションは必要ありません。

## <a name="get-parental-consent"></a>保護者の同意を得る

アプリケーションの規制によっては、成人であることが確認されているユーザーから保護者としての同意が得られることが必要になる場合があります。  Active Directory B2C には、個人の年齢を確認し、年齢が確認されている成人が未成年者に保護者としての同意を付与できるようなエクスペリエンスは用意されていません。  このエクスペリエンスは、アプリケーションまたは他のサービス プロバイダーによって提供される必要があります。

保護者の同意を得るためのユーザー フローの例を次に示します。

1. [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) の操作によって、ユーザーは未成年者として識別され、ユーザー データが無署名の JSON トークンの形式でアプリケーションに返されます。
2. アプリケーションによって JSON トークンが処理され、この未成年者に対して、保護者の同意が必要であることを通知する画面が表示されます。また、この画面では、オンラインでの保護者の同意が求められます。 
3. Active Directory B2C は、ユーザーの正常なサインインを許可するサインイン プロセスを表示し、**legalAgeGroupClassification = “minorWithParentalConsent”** を格納するように設定されているトークンをアプリケーションに発行します。アプリケーションは保護者の電子メール アドレスを収集し、国内官庁での身元確認、ライセンス検証、またはクレジット カードでの証明などの信頼できるソースを使用して保護者が成人であることを確認します。 成功した場合、アプリケーションは、Active Directory B2C ユーザー フローを使用して、未成年者にサインインするよう求めるメッセージを表示します。 同意が拒否された場合 (例: **legalAgeGroupClassification = “minorWithoutParentalConsent”**)、Active Directory B2C は、同意プロセスを再開するためにアプリケーションに (ログインではなく) JSON トークンを返します。 必要に応じて、ユーザー フローをカスタマイズすることもできます。つまり、未成年者または成人は、記録されている自分の電子メール アドレスに登録コードを送信することで未成年者のアカウントへのアクセスを回復できます。
4. 同意を取り消すオプションがアプリケーションによって未成年者に提供されます。
5. 未成年者または大人が同意を取り消すと、Azure AD Graph API を使用して、**consetProvidedForMinor** を **denied** に変更できます。 または、アプリケーションで、同意が取り消された未成年者を削除することも選択できます。 必要に応じて、認証された未成年者が (または保護者が未成年者のアカウントを使用して) 同意を取り消すことができるように、ユーザー フローをカスタマイズすることもできます。 Active Directory B2C では、**consentProvidedForMinor** を **denied** として記録します。

**legalAgeGroupClassification**、**consentProvidedForMinor**、および **ageGroup** の詳細については、「[User Resource Type (ユーザー リソースの種類)](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user)」を参照してください。 カスタム属性の詳細については、[カスタム属性を使用したコンシューマー情報の収集](active-directory-b2c-reference-custom-attr.md)に関するページをご覧ください。 Azure AD Graph API を使用して拡張属性を処理する場合、"extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z" などの長いバージョンの属性を使用する必要があります。

## <a name="gather-date-of-birth-and-country-data"></a>生年月日と国のデータを収集する

アプリケーションでは、Active Directory B2C を利用して、登録時にすべてのユーザーの生年月日 (DOB) と国を収集できます。 DOB または国の情報がまだ存在しない場合、次の認証 (サインイン) プロセスでユーザーに問い合わせることができます。 ユーザーは、DOB と国の情報を入力しなければ続行できません。 Active Directory B2C は、入力された国および DOB に基づき、その国の規制基準に従って、個人を未成年者と見なすかどうかを判別します。 

ユーザー フローをカスタマイズすると、DOB と国の情報を収集し、Azure AD B2C の要求変換を使用して **ageGroup** を決定し、結果をディレクトリに保存 (または国と DOB の情報を直接保存) することができます。

次の手順は、生年月日から **ageGroup** を計算するために使用されるロジックを示しています。

1. 一覧の国番号で、国の検索を試みます。 国が見つからない場合は、**既定値**にフォールバックします。
2. **MinorConsent** ノードが国の要素に存在する場合: <br>a. ユーザーが成人と見なされるために生まれている必要がある日付の最小値を計算します。 例: 生年月日が 2015 年 3 月 14 日で **MinorConsent** が 18 の場合、最小生年月日は 2000 年 3 月 14 日です。
    <br>b. 実際の生年月日と最小生年月日を比較します。 最小生年月日がユーザーの生年月日より前の場合、この計算は、年齢グループの計算結果として **Minor** を返します。
3. **MinorNoConsentRequired** ノードが国の要素に存在する場合、**MinorNoConsentRequired** の値を使用して手順 2a. と 2b. を繰り返します。 最小生年月日がユーザーの生年月日より前の場合、2b. の出力によって **MinorNoConsentRequired** が返されます。 
4. どちらの計算でも true が返されなかった場合、計算結果として **Adult** が返されます。

アプリケーションが他の方法で確実に DOB または国のデータを収集している場合、このアプリケーションでは GRAPH API を使用してこの情報によってユーザー レコードを更新できます。 例: 

- ユーザーが成人であることがわかっている場合は、ディレクトリ属性 **ageGroup** を **Adult** の値で更新します。
- ユーザーが未成年者であることがわかっている場合は、ディレクトリ属性 **ageGroup** を **Minor** の値で更新し、必要に応じて **consentProvidedForMinor** を設定します。

DOB データの収集の詳細については、「[Using age gating in Azure AD B2C (Active Directory B2C での年齢制限の使用)](basic-age-gating.md)」を参照してください。

## <a name="capture-terms-of-use-agreement"></a>利用規約の同意を得る

通常、アプリケーションを開発したら、ユーザー ディレクトリから参加できないか、参加が制限された状態で、アプリケーション内でユーザーから利用規約への承諾を取得します。  ただし、Active Directory B2C ユーザー フローを使用して、利用規約の承諾を収集する、承諾がない場合にアクセスを制限する、最後に同意した日付および最新バージョンの利用規約の日付に基づいて今後の利用規約の変更への承諾の強制をする、といったことを行うことができます。

**利用規約**に、"データを第三者と共有することに同意します" という一文を含めることもできます。  これらの規約に対するユーザーの承諾の収集では、ユーザーは、まとめて承諾することも、地元の法令とビジネス ルールに応じて、ある規約は承諾するが別の規約は承諾しないことも技術的に可能です。

次の手順では、利用規約の管理機能について説明します。

1. Graph API と拡張属性を使用して、利用規約への承諾と、承諾した日付を記録します。 組み込みユーザー フローとカスタム ユーザー フローの両方を使用することで、これを行うことができます。 **extension_termsOfUseConsentDateTime** 属性および **extension_termsOfUseConsentVersion** 属性を作成して使用することをお勧めします。
2. "利用規約に同意する" という必須のチェック ボックスを作成し、サインアップの際に結果を記録します。 組み込みユーザー フローとカスタム ユーザー フローの両方を使用することで、これを行うことができます。
3. Active Directory B2C は、利用規約と同意を格納します。 Graph API を使用して、応答の記録に使用した拡張属性 (たとえば、**termsOfUseTestUpdateDateTime**) を読み取ることで、任意のユーザーの状態を照会できます。 組み込みユーザー フローとカスタム ユーザー フローの両方を使用することで、これを行うことができます。
4. 承諾した日付と最新バージョンの利用規約の日付を比較することで、更新版利用規約への承諾を求めます。 これを行うことができるのは、カスタム ユーザー フローを使用した場合のみです。 拡張属性 **extension_termsOfUseConsentDateTime** を使用して **termsOfUseTextUpdateDateTime** の要求の値と比較します。承諾の方が古い場合、新規の承諾のセルフアサート画面を強制します。承諾がなければ、ポリシー ロジックを使用してアクセスをブロックします。
5. 承諾対象のバージョン番号と最後に承諾したバージョン番号を比較することで、更新版利用規約への同意を求めます。 これを行うことができるのは、カスタム ユーザー フローを使用した場合のみです。 拡張属性 **extension_termsOfUseConsentDateTime** を使用して **extension_termsOfUseConsentVersion** の要求の値と比較します。承諾の方が古い場合、新規の承諾のセルフアサート画面を強制します。承諾がなければ、ポリシー ロジックを使用してアクセスをブロックします。

利用規約の同意の取得は、次のシナリオでユーザーに提示できます。

- 新しいユーザーがサインアップします。 利用規約が表示され、同意の結果が格納されます。
- サインインしようとしているユーザーは、最新またはアクティブな利用規約に既に同意しています。 利用規約は表示されません。
- サインインしようとしているユーザーは、最新またはアクティブな利用規約にまだ同意していません。 利用規約が表示され、同意の結果が格納されます。
- サインインしようとしているユーザーは、古い利用規約に既に同意しています。この利用規約は現在、新しいバージョンに更新されています。 利用規約が表示され、同意の結果が格納されます。

次の図は、推奨されるユーザー フローを示しています。

![承諾のユーザー フロー](./media/manage-user-access/user-flow.png) 

要求における DateTime ベースの利用規約の同意の例を、次に示します。

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

要求における Version ベースの利用規約の同意の例を、次に示します。

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>次の手順

- ユーザー データを削除およびエクスポートする方法については、[ユーザー データの管理](manage-user-data.md)に関するページをご覧ください
