---
title: REST クライアントを使用して Azure の医療 Api にアクセスする
description: この記事では、VSCode で REST クライアント拡張機能を使用して、医療 Api にアクセスする方法について説明します。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042354"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>Visual Studio Code の REST クライアント拡張機能を使用した、医療 Api (プレビュー) へのアクセス

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、 [Visual Studio Code で REST クライアント拡張機能](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)を使用して、医療 api にアクセスする方法について説明します。

## <a name="install-rest-client-extension"></a>REST クライアント拡張機能のインストール

Visual Studio Code の左側のパネルにある [拡張機能] アイコンを選択し、"REST クライアント" を検索します。 [REST クライアント拡張機能](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)を検索し、をインストールします。

[![REST クライアント VSCode 拡張機能 ](media/rest-install.png) ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>ファイルを作成 `.http` し、変数を定義する

Visual Studio Code に新しいファイルを作成します。 `GET`ファイルに要求コマンドラインを入力し、として保存し `test.http` ます。 ファイルサフィックスによって `.http` 、REST クライアント環境が自動的にアクティブ化されます。 `Send Request`メタデータを取得するには、[オン] をクリックします。 

[![要求 ](media/rest-send-request.png) の送信 ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>クライアントアプリケーションの値を取得する

> [!Important]
> (メタデータを取得するのではなく) FHIR サーバー REST API を呼び出す前に、 **[アプリケーションの登録](register-application.md)** を完了する必要があります。 Azure **テナント id**、 **クライアント ID**、 **クライアントシークレット** 、 **サービス URL** をメモしておきます。

REST API の呼び出しでクライアント ID などの値を直接使用することもできますが、これらの値に対していくつかの変数を定義し、代わりに変数を使用することをお勧めします。

ファイルに `test.http` 、アプリケーションの登録から取得した次の情報を含めます。 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>Azure AD アクセストークンの取得

ファイルに以下の情報を含めた後 `test.http` 、を押し `Send Request` ます。 アクセストークンを含む HTTP 応答が表示されます。

で始まる行には、 `@name` アクセストークンを含む HTTP 応答をキャプチャする変数が含まれています。 変数は、 `@token` アクセストークンを格納するために使用されます。

>[!Note] 
>のは、 `grant_type` `client_credentials` アクセストークンを取得するために使用されます。

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![アクセストークン ](media/rest-config.png) の取得 ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` 患者データの FHIR

これで、要求で患者または特定の患者の一覧を取得できるようになりました `GET` 。 という行 `Authorization` は、要求のヘッダー情報です `GET` 。 `PUT`また、または要求を送信して、 `POST` FHIR リソースを作成/更新することもできます。

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[患者を取得する ![ ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>PowerShell または CLI を実行する

PowerShell または CLI スクリプトは Visual Studio Code 内で実行できます。 キーを押して `CTRL` 、 `~` PowerShell または Bash を選択します。 [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)の詳細については、こちらを参照してください。

### <a name="powershell-in-visual-studio-code"></a>Visual Studio Code の PowerShell
[![PowerShell ](media/rest-powershell.png) の実行 ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>Visual Studio Code の CLI
[![CLI ](media/rest-cli.png) の実行 ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>トラブルシューティング

HL7 仕様に基づくアクセストークンを必要としないメタデータを取得できない場合は、FHIR サーバーが正常に実行されていることを確認します。

アクセストークンを取得できない場合は、クライアントアプリケーションが正しく登録されていること、およびアプリケーションの登録手順の正しい値を使用していることを確認してください。

FHIR サーバーからデータを取得できない場合は、クライアントアプリケーション (またはサービスプリンシパル) に FHIR サーバーへの "FHIR Data Contributor" などのアクセス許可が付与されていることを確認してください。
