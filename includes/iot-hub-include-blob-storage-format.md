---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b566fc5c8d490d9fbad8ba64217d9ab3110cd709
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313626"
---
> [!NOTE]
> データは、既定の [Apache Avro](https://avro.apache.org/) 形式または JSON のいずれかで Blob Storage に書き込むことができます。 
>    
> エンコード形式は、BLOB ストレージ エンドポイントの構成時にのみ設定できます。 既に設定されているエンドポイントの形式は変更できません。 JSON エンコードを使用する場合は、メッセージのシステム プロパティで contentType を JSON に設定し、contentEncoding を UTF-8 に設定する必要があります。 
>
> BLOB ストレージ エンドポイントの使用に関する詳細については、[ストレージへのルーティングに関するガイダンス](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)を参照してください。
>