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
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "89042864"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> データは、既定の [Apache Avro](https://avro.apache.org/) 形式または JSON (プレビュー) のいずれかで BLOB ストレージに書き込むことができます。 
>    
> JSON 形式にエンコードする機能は、IoT Hub が提供されているすべてのリージョンでプレビュー段階です (米国東部、米国西部、西ヨーロッパを除く)。 エンコード形式は、BLOB ストレージ エンドポイントの構成時にのみ設定できます。 既に設定されているエンドポイントの形式は変更できません。 JSON エンコードを使用する場合は、メッセージのシステム プロパティで contentType を JSON に設定し、contentEncoding を UTF-8 に設定する必要があります。 
>
> BLOB ストレージ エンドポイントの使用に関する詳細については、[ストレージへのルーティングに関するガイダンス](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)を参照してください。
>