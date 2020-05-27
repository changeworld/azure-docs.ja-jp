---
title: Azure portal を使用して Azure SQL Edge (プレビュー) をデプロイする
description: Azure portal を使用して Azure SQL Edge (プレビュー) をデプロイする方法について説明します
keywords: SQL Edge をデプロイする
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7dfc7b680c6b7d6b2a3641b25c0f82665143bde1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594671"
---
# <a name="deploy-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) をデプロイする 

Azure SQL Edge (プレビュー) は、IoT および Azure IoT Edge のデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは、IoT のアプリケーションおよびソリューションに向けて、パフォーマンスの高いデータ ストレージと処理レイヤーを作成する機能を提供します。 このクイックスタートでは、Azure portal を使用して Azure IoT Edge を介して Azure SQL Edge モジュールの作成を開始する方法を示します。

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure portal](https://portal.azure.com/) にサインインします。
* [Azure IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)を作成します。
* [Azure portal からIoT Edge デバイス](../iot-edge/how-to-register-device-portal.md)を登録します。
* IoT Edge デバイスを準備して、[Azure portal から IoT Edge モジュールをデプロイします](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> Azure Linux VM を IoT Edge デバイスとしてデプロイする場合は、こちらの[クイックスタート ガイド](../iot-edge/quickstart-linux.md)を参照してください。

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Azure Marketplace から SQL Edge モジュールをデプロイする

Azure Marketplace は、アプリケーションとサービスのオンライン マーケットプレースであり、[IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)など、Azure での実行を認定されてそれに最適化されている、さまざまなエンタープライズ アプリケーションとソリューションを参照できます。 Azure SQL Edge は、マーケットプレースからエッジ デバイスにデプロイできます。

1. Azure Marketplace で Azure SQL Edge モジュールを検索します。<br><br>

   ![MarketPlace での SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 要件に最も合うソフトウェア プランを選択し、 **[作成]** をクリックします。 <br><br>

   ![適切なソフトウェア プランの選択](media/deploy-portal/pick-correct-plan.png)

3. [IoT Edge モジュールのターゲット デバイス] ページで、次の詳細情報を指定し、 **[作成]** をクリックします。

   |**フィールド**  |**説明**  |
   |---------|---------|
   |サブスクリプション  |  IoT Hub が作成された Azure サブスクリプション |
   |IoT Hub   |  IoT Edge デバイスが登録され、その後 [デバイスへのデプロイ] オプションを選択する IoT Hub の名前|
   |IoT Edge デバイス名  |  SQL Edge がデプロイされる IoT Edge デバイスの名前 |

4. **[モジュールの設定]** ページで、デプロイ モジュールのセクションに移動し、SQL Edge モジュールに対して **[構成]** をクリックします。 

5. **[IoT Edge のカスタム モジュール]** ウィンドウで、環境変数に必要な値の指定や、モジュールの作成オプションと必要なプロパティのカスタマイズを行います。 サポートされている環境変数の完全な一覧については、[SQL Server コンテナーの環境変数](/sql/linux/sql-server-linux-configure-environment-variables/)に関する記事を参照してください。

   |**パラメーター**  |**説明**|
   |---------|---------|
   | 名前 | モジュールの名前です。 |
   |SA_PASSWORD  | SQL Edge 管理者アカウント用の強力なパスワードを指定します。 |
   |MSSQL_LCID   | SQL Server に使用する言語 ID を設定します。 たとえば、1036 はフランス語です。 |
   |MSSQL_COLLATION | SQL Server の既定の照合順序を設定します。 この設定は、照合順序に対する言語 ID (LCID) の既定のマッピングをオーバーライドします。 |

   > [!NOTE]
   > モジュールの **[イメージ のURI]** または **ACCEPT_EULA** の設定は変更および更新しないでください。

6. **[IoT Edge のカスタム モジュール]** ウィンドウで、 **[ホスト ポート]** のコンテナー作成オプションの必要な値を更新します。 複数の SQL DB Edge モジュールをデプロイする必要がある場合は、必ずマウントオプションを更新して、永続的なボリュームの新しいソースとターゲットのペアを作成してください。 マウントとボリュームの詳細については、docker ドキュメントの「[ボリュームの使用](https://docs.docker.com/storage/volumes/)」を参照してください。 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. **[IoT Edge のカスタム モジュール]** ウィンドウで、 *[モジュール ツインの必要なプロパティの設定]* を更新して、SQL パッケージの場所と Stream Analytics ジョブ情報を含めます。 この 2 つのフィールドは省略可能で、データベースとストリーミング ジョブと共に SQL Edge モジュールをデプロイする場合に使用する必要があります。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. **[IoT Edge のカスタム モジュール]** ウィンドウで、 *[再起動ポリシー]* を [常時] に設定し、 *[必要な状態]* を [実行中] に設定します。
9. **[IoT Edge のカスタム モジュール]** ウィンドウで、 **[保存]** をクリック します。
10. **[モジュールの設定]** ページで、 **[次へ]** をクリックします。
11. **[モジュールの設定]** ページの **[Specify Route (optional)]\(ルートの指定 (省略可能)\)** で、モジュール間またはモジュールから IoT Edge ハブへの通信ルートを指定します。[IoT Edge へのモジュールのデプロイと ルートの確立](../iot-edge/module-composition.md)に関する記事を参照してください。
12. **[次へ]** をクリックします。
13. **[送信]** をクリックします。

このクイックスタートでは、SQL Edge モジュールを IoT Edge デバイスにデプロイしました。

## <a name="next-steps"></a>次の手順

- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)。
- IoT Edge を使用して SQL Edge でエンドツーエンドの IoT ソリューションを構築する。