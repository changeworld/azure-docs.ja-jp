---
title: "Azure でのシングル サインアウトの SAML プロトコル | Microsoft Docs"
description: "この記事では、Azure Active Directory でのシングル サインアウト SAML プロトコルについて説明します。"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d05ab5c4bd813b5f371be27605a9096b0b9c9656


---
# <a name="single-sign-out-saml-protocol"></a>シングル サインアウトの SAML プロトコル
Azure Active Directory (Azure AD) は、SAML 2.0 の Web ブラウザー シングル サインアウト プロファイルをサポートします。 シングル サインアウトが正常に動作するには、Azure AD はアプリケーションの登録時にメタデータの URL を登録する必要があります。 Azure AD は、クラウド サービスのログアウト URL と署名キーをメタデータから取得します。 Azure AD は、署名キーを使用して着信した LogoutRequest の署名を検証し、サインアウト後に LogoutURL を使用してユーザーをリダイレクトします。

クラウド サービスがメタデータ エンドポイントをサポートしていない場合、開発者は、アプリケーションが登録された後で、Microsoft サポートに連絡してログアウト URL と署名キーを提供する必要があります。

次の図では、Azure AD のシングル サインアウト プロセスのワークフローを示します。

![シングル サインアウトのワークフロー](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
クラウド サービスは `LogoutRequest` メッセージを Azure AD に送信して、セッションが終了されたことを示します。 `LogoutRequest` 要素の例を次に示します。

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Azure AD に送信される `LogoutRequest` 要素には、次の属性が必要です。

* `ID` : サインアウト要求を示します。 `ID` の値は、数字以外で始まっている必要があります。 一般的な方法としては、GUID の文字列表現の前に **id** を付加します。
* `Version` : この要素の値は **2.0**に設定します。 この値は必須です。
* `IssueInstant` : 世界協定時刻 (UTC) の値と[ラウンドトリップ書式 ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx) を含む `DateTime` 文字列です。 Azure AD ではこの型の値が期待されますが、必須ではありません。
* `LogoutRequest` 要素に含まれる `Consent`、`Destination`、`NotOnOrAfter`、`Reason` 属性は無視されます。

### <a name="issuer"></a>発行者
`LogoutRequest` の `Issuer` 要素は、Azure AD でのクラウド サービスの **ServicePrincipalNames** のいずれかと厳密に一致する必要があります。 通常、これはアプリケーション登録時に指定される **App ID URI** に設定されます。

### <a name="nameid"></a>NameID
`NameID` 要素の値は、サインアウトしているユーザーの `NameID` と厳密に一致する必要があります。

## <a name="logoutresponse"></a>LogoutResponse
Azure AD は `LogoutRequest` 要素への応答で `LogoutResponse` を送信します。 `LogoutResponse`の例を次に示します。

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD は、`LogoutResponse` 要素の `ID`、`Version`、`IssueInstant` の値を設定します。 また、`InResponseTo` 要素には、応答の原因になった `LogoutRequest` の `ID` 属性の値を設定します。

### <a name="issuer"></a>発行者
Azure AD は、この値を `https://login.microsoftonline.com/<TenantIdGUID>/` に設定します。<TenantIdGUID> は、Azure AD テナントのテナント ID です。

`Issuer` 要素の値を評価するには、アプリケーション登録時に指定された **App ID URI** の値を使用します。

### <a name="status"></a>状態
Azure AD は、`Status` 要素の `StatusCode` 要素を使用して、サインアウトの成功または失敗を示します。 サインアウトの試行が失敗した場合、 `StatusCode` 要素にはカスタム エラー メッセージを含めることもできます。




<!--HONumber=Nov16_HO3-->


