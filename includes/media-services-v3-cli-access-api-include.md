---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830104"
---
## <a name="access-the-media-services-api"></a>Media Services API にアクセスする

Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用します。 以下のコマンドでは、Azure AD アプリケーションを作成し、そのアカウントにサービス プリンシパルをアタッチしています。 以下の手順で示したように、返された値を使用して .NET アプリを構成することになります。

スクリプトを実行する前に、`amsaccount` と `amsResourceGroup` は、実際にリソースを作成するときに選んだ名前に置き換えてください。 `amsaccount` は、サービス プリンシパルのアタッチ先となる Azure Media Services アカウントの名前です。 <br/>このコマンドには、さらに `xml` オプションが指定されています。このオプションによって返される xml を app.config に貼り付けることができます。`xml` オプションを省略した場合、応答は `json` 形式になります。

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

このコマンドからは、次のような応答が生成されます。

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>サンプル アプリの構成

アプリを実行して Media Services の API にアクセスするには、正しいアクセス値を App.config で指定する必要があります。 

1. Visual Studio を開きます。
2. 複製したソリューションにブラウザーでアクセスします。
3. ソリューション エクスプローラーで、*EncodeAndStreamFiles* プロジェクトを展開します。
4. このプロジェクトをスタート アップ プロジェクトとして設定します。
5. App.config を開きます。
6. appSettings の値を、前の手順で取得した値に置き換えます。

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Ctrl + Shift + B キーを押して、ソリューションをビルドします。
