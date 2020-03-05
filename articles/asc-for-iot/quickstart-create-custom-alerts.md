---
title: クイック スタート:Azure Security Center for IoT のカスタム アラートを作成する
description: Azure Security Center for IoT セキュリティ サービスのカスタム デバイス アラートを取り上げます。それらを作成し、割り当てる方法について説明しています。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303530"
---
# <a name="quickstart-create-custom-alerts"></a>クイック スタート:カスタム アラートの作成


カスタムのセキュリティ グループおよびアラートを使用することで、エンド ツー エンドのセキュリティ情報とカテゴリ別のデバイス知識を最大限に活用し、IoT ソリューション全体のセキュリティを確実に向上させます。 

## <a name="why-use-custom-alerts"></a>カスタム アラートを使用する理由 

お客様の IoT デバイスについて最もよく知っているのはお客様自身です。

予想されるデバイスの動作を完全に理解しているお客様のために、Azure Security Center for IoT には、その理解をデバイスの動作ポリシーに変換し、予想される通常の動作からの逸脱に基づいてアラートする機能があります。

## <a name="security-groups"></a>セキュリティ グループ

セキュリティ グループでは、デバイスの論理グループを定義して、それらのセキュリティ状態を一元的に管理できます。

これらのグループは、特定のハードウェアを備えたデバイス、一定の場所にデプロイされたデバイス、または特定のニーズに適したその他のグループを表す場合があります。

セキュリティ グループは、**SecurityGroup** という名前のデバイス ツイン タグ プロパティによって定義されます。 既定では、IoT Hub 上の各 IoT ソリューションに、**default** という名前のセキュリティ グループが 1 つあります。 **SecurityGroup** プロパティの値を変更して、デバイスのセキュリティ グループを変更します。
 
次に例を示します。

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

セキュリティ グループを使用して、デバイスを論理カテゴリにグループ化します。 グループの作成後、それらを任意のカスタム アラートに割り当てて、最も効果的なエンド ツー エンド IoT セキュリティ ソリューションを実現します。 

## <a name="customize-an-alert"></a>アラートのカスタマイズ

1. IoT ハブを開きます。 
2. **[セキュリティ]** セクションで **[カスタム アラート]** をクリックします。 
3. カスタマイズの適用先にしたいセキュリティ グループを選択します。 
4. **[Add a custom alert]\(カスタム アラートの追加\)** をクリックします。
5. ドロップダウン リストからカスタムのアラートを選択します。 
6. 必須のプロパティを編集して、 **[OK]** をクリックします。
7. 必ず **[保存]** をクリックします。 新しいアラートは、保存しないと IoT ハブを次に閉じたときに削除されます。

 
## <a name="alerts-available-for-customization"></a>カスタマイズに使用できるアラート

Azure Security Center for IoT には、ユーザーの特定のニーズに応じてカスタマイズできるアラートが多数用意されています。 アラートの重要度、データ ソース、説明、および各アラートを受信した場合の推奨される修復手順については、[カスタマイズ可能なアラートの表](concept-customizable-security-alerts.md)を確認してください。 


## <a name="next-steps"></a>次のステップ

次の記事に進んで、セキュリティ エージェントのデプロイ方法を確認してください。

> [!div class="nextstepaction"]
> [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
