---
title: サービス プリンシパルの資格情報を更新
description: サービス プリンシパルの資格情報を更新する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669544"
---
# <a name="update-service-principal-credentials"></a>サービス プリンシパルの資格情報を更新

サービス プリンシパルの資格情報が変更された場合は、データ コントローラーのシークレットを更新する必要があります。

たとえば、サービス プリンシパルのテナント ID、クライアント ID、およびクライアント シークレットの特定の値セットを使用してデータ コントローラーをデプロイした後、これらの値の 1 つまたは複数を変更する場合は、データ コントローラーのシークレットを更新する必要があります。  テナント ID、クライアント ID、またはクライアント シークレットを更新する手順を次に示します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>バックグラウンド

サービス プリンシパルは、[サービス プリンシパルの作成](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)で作成されました。 

## <a name="steps"></a>手順

1. 既定のエディターでサービス プリンシパル シークレットにアクセスします。

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   たとえば、`arc` 名前空間内のデータ コントローラーに対してサービス プリンシパル シークレットを編集するには、次のコマンドを実行します。

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit` コマンドを実行すると、既定のエディターで credentials.yml ファイルが開きます。 


1. サービス プリンシパル シークレットを編集します。 

   既定のエディターで、データ セクションの値を更新された資格情報に置き換えます。

   次に例を示します。

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   必要に応じて、`clientID`、`clientSecret`、`tenantID` の値を編集します。 

> [!NOTE]
>値は base64 でエンコードする必要があります。 その他のプロパティは編集しないでください。

`clientId`、`clientSecret`、または`tenantID` に不正な値が指定されている場合、`control-xxxx` ポッドまたはコントローラーのコンテナー ログに次のようなエラー メッセージが表示されます。

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>次のステップ

[Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する](retrieve-the-username-password-for-data-controller.md)

[サービス プリンシパルを作成する](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
