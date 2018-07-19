---
title: Azure Storage クライアントのセキュリティで保護された TLS の有効化 | Microsoft Docs
description: Azure Storage のクライアントで TLS 1.2 を有効にする方法について説明します。
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 6c313b6015a8a6dcc4ca5befb5fef70b047d0410
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866527"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Azure Storage クライアントのセキュリティで保護された TLS の有効化

最新のコンプライアンスとセキュリティの要件に基づいて Azure Storage の使用時にサービスを監査する必要がある場合は、SSL 1.0、2.0、3.0、および TLS 1.0 がコンプライアンスに準拠していない通信プロトコルとして認識されます。

SSL 1.0、2.0、および 3.0 は脆弱性があることが確認されています。 これらは RFC で禁止されています。 TLS 1.0 は、安全でないブロック暗号 (DES CBC と RC2 CBC) およびストリーム暗号 (RC4) を使用する際にセキュリティが確保されません。 PCI 協議会も新しいバージョンの TLS への移行を推奨しています。 詳しくは、[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0) に関する記事をご覧ください。

Azure Storage では 2015 年以降 SSL 3.0 の使用を停止しており、パブリック HTTPs エンドポイントでは TLS 1.2 を使用しています。ただし、下位互換性を確保するために TLS 1.0 と TLS 1.1 は引き続きサポートされています。

Azure Storage に対するセキュリティで保護され、コンプライアンスに準拠した接続を確保するには、クライアント側で TLS 1.2 を有効にしてから Azure Storage サービスを操作する要求を送信する必要があります。

## <a name="enable-tls-12-in-net-client"></a>.NET クライアントでの TLS 1.2 の有効化

クライアントが TLS 1.2 をネゴシエートするには、OS と .NET Framework の両方のバージョンで TLS 1.2 がサポートされている必要があります。 詳しくは、「[TLS 1.2 のサポート](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12)」をご覧ください。

次のサンプルは、.NET クライアントで TLS 1.2 を有効にする方法を示しています。

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>PowerShell クライアントでの TLS 1.2 の有効化

次のサンプルは、PowerShell クライアントで TLS 1.2 を有効にする方法を示しています。

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>TLS 1.2 接続の確認

Fiddler を使用すると、TLS 1.2 が実際に使用されるかどうかを確認できます。 Fiddler を開いてクライアントのネットワーク トラフィックのキャプチャを開始してから、前述のサンプルを実行します。 これで、サンプルが確立する接続における TLS バージョンを確認できます。

次のスクリーンショットは、この確認のサンプルを示しています。

![Fiddler での TLS バージョンの確認のスクリーンショット](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>関連項目

* [トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Java クライアントで TLS を有効にする](https://www.java.com/en/configure_crypto.html)
