---
title: "Azure IoT Suite に関する FAQ | Microsoft Docs"
description: "IoT スイートに関してよく寄せられる質問"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 14e2fcea9a6afbac640d665d5e44a700f855db4b
ms.openlocfilehash: ff2e371f0c75dca36cceda36f477e30588dea8e4


---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT スイートに関してよく寄せられる質問
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Azure ポータルでのリソース グループの削除と、azureiotsuite.com の構成済みソリューションで削除をクリックすることの違いは何ですか?
* [azureiotsuite.com][lnk-azureiotsuite] で構成済みのソリューションを削除すると、構成済みソリューションの作成時にプロビジョニングしたすべてのリソースが削除されます。 リソース グループにリソースを追加してある場合、それらも削除されます。 
* [Azure ポータル][lnk-azure-portal] でリソース グループを削除した場合は、そのリソース グループ内のリソースのみが削除されます。 さらに、[Azure クラシック ポータル][lnk-classic-portal]で構成済みソリューションに関連付けられている Azure Active Directory アプリケーションを削除する必要があります。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる IoT Hub インスタンスはいくつですか?
サブスクリプションごとに 10 個の IoT Hub をプロビジョニングできます。 この上限は、[Azure サポート チケット][link-azuresupportticket]を作成して引き上げることができます。ただし、[Azure サブスクリプションの制限][link-azuresublimits]に関するページで説明されているように、既定ではサブスクリプションごとにプロビジョニングできる IoT Hub は 10 個のみです。 よって、構成済みソリューションごとに新しい IoT Hub をプロビジョニングするため、特定のサブスクリプションでプロビジョニングできる構成済みソリューションの数は最大 10 個になります。 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>サブスクリプションには何個の DocumentDB インスタンスをプロビジョニングできますか?
50 個です。 [Azure サポート チケット][link-azuresupportticket]を作成してこの上限を引き上げることができますが、既定では、サブスクリプションごとにプロビジョニングできる DocumentDB インスタンスは 50 個のみです。 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる無料の Bing マップ API の数はいくつですか?
2 個です。 1 つの Azure サブスクリプションでは、内部トランザクション レベル 1 のエンタープライズ向け Bing 地図プランを最大で 2 個作成できます。 リモート監視ソリューションは、既定では内部トランザクション レベル 1 のプランでプロビジョニングされます。 その結果、変更なしでサブスクリプションにプロビジョニングできるリモート監視ソリューションは最大&2; 個です。

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>静止マップを含むリモート監視ソリューションのデプロイがあります。これに対話型の Bing マップを追加するにはどうすればよいですか。
1. [Azure Portal][lnk-azure-portal] から Bing Maps API for Enterprise の QueryKey を取得してください。 
   
   1. [Azure Portal][lnk-azure-portal] で、Bing Maps API for Enterprise が含まれるリソース グループに移動します。
   2. **[すべての設定]**、**[キーの管理]** の順にクリックします。 
   3. **MasterKey** と **QueryKey** という&2; つのキーが表示されます。 **QueryKey** の値をコピーします。
      
      > [!NOTE]
      > Bing Maps API for Enterprise アカウントがない場合は、 [Azure Portal][lnk-azure-portal] で [+ 新規] をクリックし、Bing Maps API for Enterprise を検索して、画面の指示に従ってアカウントを作成してください。
      > 
      > 
2. [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] から最新のコードをダウンロードします。
3. リポジトリの /docs/ フォルダーにあるコマンドライン デプロイ ガイダンスに従って、ローカルまたはクラウドのデプロイを実行します。 
4. ローカルまたはクラウドのデプロイを実行したら、ルート フォルダーでデプロイ時に作成された *.user.config ファイルを探します。 このファイルをテキスト エディターで開きます。 
5. 次の行に、**QueryKey** からコピーした値を含めます。 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Microsoft Azure for DreamSpark があれば、構成済みソリューションを作成できますか?
現時点では、[Microsoft Azure for DreamSpark][lnk-dreamspark] アカウントを使用して構成済みソリューションを作成することはできません。 ただし、[Azure の無料試用版アカウント][lnk-30daytrial]を数分で作成して、構成済みソリューションを作成できるようにすることはできます。

### <a name="how-do-i-delete-an-aad-tenant"></a>AAD テナントはどうすれば削除できますか?
[Azure AD テナントの削除方法][lnk-delete-aad-tennant]に関する Eric Golpe のブログ投稿を参照してください。

### <a name="next-steps"></a>次のステップ
IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

* [予測的なメンテナンスの構成済みソリューションの概要][lnk-predictive-overview]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Feb17_HO2-->


