---
title: リモート監視のデータ アクセス制御 - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する方法について説明します
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827173"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する

この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する方法について説明します。 ソリューション アクセラレータのユーザーが Time Series Insights エクスプ ローラーにアクセスできるようにするには、各ユーザーにデータのアクセス許可を付与する必要があります。

データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

## <a name="grant-data-access"></a>データ アクセスの許可

次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. [Azure portal](https://portal.azure.com) にサインインする

2. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。 

3. リストから Time Series Insights 環境を選択します。

4. **[データ アクセス ポリシー]** を選択し、 **[+ 追加]** をクリックします。
    ![Time Series Insights ソースの管理 - 環境](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. **[ユーザーの選択]** を選択します。  ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** をクリックして選択内容を確定します。 

    ![Time Series Insights ソースの管理 - 追加](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。
   - ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。 
   - それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

     **[OK]** を選び、ロールの選択を確定します。

     ![Time Series Insights ソースの管理 - ユーザーの選択](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

    ![Time Series Insights ソースの管理 - ロールの選択](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. **[データ アクセス ポリシー]** ページには、各ユーザーのユーザーとロールが一覧表示されます。

    ![Time Series Insights ソースの管理 - 結果](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>次のステップ

この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を付与する方法について説明します。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください

リモート監視ソリューションのカスタマイズの詳細については、「[Customize and redeploy a microservice](iot-accelerators-microservices-example.md)」 (マイクロサービスのカスタマイズと再デプロイ) をご覧ください
<!-- Next tutorials in the sequence -->