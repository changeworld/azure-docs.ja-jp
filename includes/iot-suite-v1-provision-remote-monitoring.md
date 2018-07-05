---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 04081a514d9b96a5289594a730ec0519e6ee0b01
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36329792"
---
## <a name="provision-the-solution"></a>ソリューションのプロビジョニング

リモート監視の事前構成済みソリューションをアカウントにまだプロビジョニングしていない場合は、次の手順を実施します。

1. Azure アカウントの資格情報を使用して [azureiotsuite.com][lnk-azureiotsuite] にサインインし、**[+]** をクリックしてソリューションを作成します。
2. **[リモート監視]** タイルで **[選択]** タイルを選択します。
3. リモート監視の事前構成済みソリューションの **[ソリューション名]** を入力します。
4. ソリューションのプロビジョニングに使用する**リージョン**と**サブスクリプション**を選択します。
5. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

### <a name="wait-for-the-provisioning-process-to-complete"></a>プロビジョニング プロセスが完了するまで待機します。
1. **プロビジョニング** の状態を表示する、目的のソリューションのタイルをクリックします。
2. Azure サービスが Azure サブスクリプションにデプロイされたら、 **プロビジョニングの状態** を確認します。
3. プロビジョニングが完了すると、状態が **[準備完了]** に変わります。
4. タイルをクリックし、右側のウィンドウでソリューションの詳細を確認します。

> [!NOTE]
> 構成済みソリューションのデプロイの問題が発生している場合は、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」と [FAQ][lnk-faq] を参照してください。 それでも問題が解決しない場合は、[ポータル][lnk-portal]でサービス チケットを作成してください。
> 
> 

ソリューションの一覧に予測どおりに表示されない詳細がありますか? [ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)のページで機能に関する提案をお寄せください。

[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-faq]: ../articles/iot-suite/iot-suite-v1-faq.md