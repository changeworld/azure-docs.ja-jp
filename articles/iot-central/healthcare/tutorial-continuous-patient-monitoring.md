---
title: チュートリアル - Azure IoT 患者の継続的なモニタリング | Microsoft Docs
description: このチュートリアルでは、IoT Central の患者の継続的なモニタリング アプリケーション テンプレートをデプロイして使用する方法について説明します。
author: philmea
ms.author: philmea
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 4d5b00526219b0a4ade24b7522ea4826236fd70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057763"
---
# <a name="tutorial-deploy-and-walkthrough-the-continuous-patient-monitoring-app-template"></a>チュートリアル: 患者の継続的なモニタリング アプリ テンプレートをデプロイして利用する

:::image type="content" source="media/cpm-architecture.png" alt-text="患者の継続的なモニタリングのアーキテクチャ":::

## <a name="bluetooth-low-energy-ble-medical-devices"></a>Bluetooth Low Energy (BLE) の医療機器

医療の IoT ソリューションで使用される多くの医療ウェアラブルは BLE デバイスです。 それらのデバイスはクラウドと直接通信することができません。クラウド ソリューションとは、ゲートウェイを使用してデータを交換する必要があります。 このアーキテクチャでは、ゲートウェイとして携帯電話アプリケーションを使用します。

## <a name="mobile-phone-gateway"></a>携帯電話ゲートウェイ

携帯電話アプリケーションの主な機能は、医療機器から BLE データを収集し、それを IoT Central に伝達することです。 また、患者は、このアプリの指示に従ってデバイスのセットアップを行ったり、個人の健康データを閲覧したりします。 ソリューションによっては、タブレット ゲートウェイや、病院の部屋に据え付けられた静的ゲートウェイが使用される場合もあります。 アプリケーション開発作業を開始するための出発点としては、Android および iOS 用のオープンソースのサンプル モバイル アプリケーションを使用できます。 詳細については、[GitHub の Continuous Patient Monitoring サンプル モバイル アプリ](https://github.com/iot-for-all/iotc-cpm-sample)に関するページを参照してください。

## <a name="export-to-azure-api-for-fhirreg"></a>Azure API for FHIR にエクスポートする&reg;

Azure IoT Central は HIPAA に準拠し、HITRUST&reg; 認定を受けています。 [Azure API for FHIR](../../healthcare-apis/fhir/overview.md) を使用して他のサービスに患者の健康データを送信することもできます。 Azure API for FHIR は、臨床医療データを対象とした標準ベースの API です。 [Azure IoT Connector for FHIR](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md) を介すことで、IoT Central からの継続的なデータ エクスポート先として Azure API for FHIR を使用することができます。

## <a name="machine-learning"></a>機械学習

医療チームの意思決定を支援するため、FHIR データに対し機械学習モデルを使用して分析情報を生成します。 詳細については、[Azure Machine Learning のドキュメント](../../machine-learning/index.yml)を参照してください。

## <a name="provider-dashboard"></a>プロバイダー ダッシュボード

Azure API for FHIR データを使用して患者分析情報ダッシュボードを作成するか、医療チームが使う電子医療記録に直接データを統合します。 医療チームはダッシュボードを使用して、患者を補助したり、早い段階で悪化の徴候を発見したりすることができます。 詳細については、「[Power BI プロバイダー ダッシュボードを構築する](tutorial-health-data-triage.md)」のチュートリアルを参照してください。

このチュートリアルでは、以下の内容を学習します。

- アプリケーション テンプレートを作成する
- アプリケーション テンプレートのウォークスルーを行う

## <a name="prerequisites"></a>前提条件

- このアプリをデプロイするために必要な前提条件は特にありません。
- 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。

## <a name="create-continuous-patient-monitoring-application"></a>患者の継続的なモニタリング アプリケーションを作成する

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Healthcare]\(医療\)** タブを選択します。:::image type="content" source="media/app-manager-health.png" alt-text="アプリケーション テンプレート":::

1. **[患者の継続的なモニタリング]** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboards"></a>ダッシュボード

アプリ テンプレートをデプロイすると、最初に **[Lamna in-patient monitoring dashboard]\(Lamna 入院患者のモニタリング ダッシュボード\)** が表示されます。 Lamna ヘルスケアは、次の 2 つの病院を含む架空の病院システムです。Woodgrove 病院と Burkville 病院。 Woodgrove 病院のオペレーター ダッシュボードの機能を次に示します。

* デバイスの **バッテリ レベル** や **接続** 状態など、デバイスのテレメトリとプロパティを確認する。

* Smart Vitals Patch デバイスの **間取り図** と場所を表示する。

* 新しい患者の Smart Vitals Patch を **再プロビジョニング** する。

* 病院のケア チームが患者を追跡するために見る可能性がある **プロバイダー ダッシュボード** の例を表示する。

* デバイスが入院患者と遠隔のどちらのシナリオに使用されているかを示すために、デバイスの **患者の状態** を変更する。

:::image type="content" source="media/lamna-in-patient.png" alt-text="入院患者の状態":::

また、 **[Go to remote patient dashboard]\(遠隔患者のダッシュボードに移動する\)** を選択して、Burkville 病院のオペレーター ダッシュボードを表示することもできます。 このダッシュボードには、類似した一連のアクション、テレメトリ、および情報が含まれています。 さらに、使用されている複数のデバイスを表示し、それぞれの **ファームウェアを更新** することもできます。

:::image type="content" source="media/lamna-remote.png" alt-text="リモート オペレーター ダッシュボード":::

### <a name="device-templates"></a>デバイス テンプレート

**[デバイス テンプレート]** を選択した場合、デバイスの種類が 2 つテンプレートに表示されます。

- **Smart Vitals Patch**:このデバイスは、各種の生存徴候を測定する修正プログラムを表します。 病院の内外の患者を監視するために使用します。 テンプレートを選択すると、パッチがバッテリ レベルやデバイス温度などのデバイス データを送信するだけでなく、呼吸数や血圧などの患者の健康データも送信していることを確認できます。

- **Smart Knee Brace**:このデバイスは、患者が膝関節置換術から回復する際に使用する膝関節支持帯を表します。 このテンプレートを選択すると、デバイス データ、関節可動域、加速などの能力が表示されます。

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Smart patch テンプレート":::

### <a name="device-groups"></a>Device groups

デバイス グループを使用すると、一連のデバイスを論理的にグループ化して、それらに対してクエリや操作を一括で実行できます。

[デバイス グループ] タブを選択すると、アプリケーションのデバイス テンプレートごとに既定のデバイス グループが表示されます。 また、"**Provision devices (デバイスのプロビジョニング)** " および "**Devices with outdated firmware (古いファームウェアがインストールされたデバイス)** " という 2 つの追加のサンプル デバイス グループが作成されています。 これらのサンプル デバイス グループを入力に使用して、いくつかの[ジョブ](#jobs)をアプリケーションで実行することができます。

### <a name="rules"></a>ルール

**[Rules]\(ルール\)** を選択すると、次の 3 つのルールがテンプレートに表示されます。

- **Brace temperature high (関節支持帯の温度が高い)** :このルールは、Smart Knee Brace のデバイス温度について、95&deg;F を超える状態が 5 分を超えたときトリガーされます。 このルールを使用して、患者とケア チームにアラートを出し、遠隔操作でデバイスを冷却します。

- **Fall detected (転倒を検出)** :このルールは、患者の転倒が検出されるとトリガーされます。 このルールを使用して、転倒した患者を支援するためのオペレーション チームを配置するアクションを構成します。

- **Patch battery low (パッチのバッテリーが低下している)** :このルールは、デバイスのバッテリ レベルが 10% を下回ったときにトリガーされます。 このルールを使用して、デバイスの充電のための患者への通知をトリガーします。

:::image type="content" source="media/brace-temp-rule.png" alt-text="ルール":::

### <a name="jobs"></a>ジョブ

ジョブを使用すると、[デバイス グループ](#device-groups)を入力として使用して、一連のデバイスに対して一括操作を実行できます。 このアプリケーション テンプレートには、オペレーターが実行できる 2 つのサンプル ジョブがあります。

* **Update knee brace firmware (膝関節支持帯のファームウェアを更新する)** : このジョブでは、**Devices with outdated firmware (古いファームウェアがインストールされたデバイス)** デバイス グループのデバイスを検出し、最新ファームウェア バージョンに更新するコマンドを実行します。 このサンプル ジョブは、デバイスが、**更新** コマンドを処理してクラウドからファームウェア ファイルをフェッチできることを前提としています。  

* **Re-provision devices (デバイスを再プロビジョニングする)** : 最近病院に返されてきた一連のデバイスがあるとします。 このジョブは、**Provision devices (デバイスのプロビジョニング)** というデバイス グループからデバイスを検索し、次の一連の患者用にプロビジョニングします。

### <a name="devices"></a>デバイス

**[デバイス]** タブを選択し、**Smart Knee Brace** のインスタンスを選択します。 選択した特定のデバイスに関する情報を調べることができるビューが 3 つあります。 これらのビューは、デバイスのデバイス テンプレートを作成するときに作成されて発行されます。 そのため、これらのビューは、接続またはシミュレートするすべてのデバイスで一貫しています。

**[Dashboard]\(ダッシュボード\)** ビューには、オペレーター向けに、デバイスのテレメトリとプロパティの概要が表示されます。

**[Properties]\(プロパティ\)** タブでは、クラウド プロパティの編集やデバイス プロパティの読み取りと書き込みを行うことができます。

**[コマンド]** タブでは、デバイスに対してコマンドを実行できます。

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="膝関節支持帯のダッシュボード":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションをもう使わない場合は、 **[管理] > [アプリケーションの設定]** の順に選択し、 **[削除]** をクリックして、アプリケーションを削除します。

:::image type="content" source="media/admin-delete.png" alt-text="リソースをクリーンアップする":::

## <a name="next-steps"></a>次のステップ

次の記事に進んで、IoT Central アプリケーションに接続するプロバイダー ダッシュボードを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [プロバイダー ダッシュボードを構築する](tutorial-health-data-triage.md)