---
title: Azure Certified Device プログラム | Microsoft Docs
description: Azure Certified Device プログラムについて説明します。
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048102"
---
# <a name="what-is-the-azure-certified-device-program"></a>Azure Certified Device プログラムとは

Azure Certified Device プログラムを使用すると、顧客ソリューションを Azure サービスとシームレスに連携させることができます。 このプログラムでは、ツール、サービス、およびマーケットプレースを使用して、業界の知識およびベスト プラクティスをデバイスおよびソリューション ビルダーのコミュニティと共有できます。

このプログラムは次のように設計されています。

- **顧客に自信を与える:** 顧客は、Azure サービス用に Microsoft が認定しているデバイスを自信を持って購入できます。

- **顧客が自分のソリューションに適したデバイスを見つけられるように支援する:** デバイス ビルダーは、認定プロセスの一環として、自分のデバイスの固有の機能を発行できます。 顧客は、ニーズに合った製品を簡単に見つけることができます。

- **認定デバイスをレベルアップする:** デバイス ビルダーによって、可視性の向上、顧客との連絡、および Azure Certified Device ブランドの使用を実現できます。

この記事では、次の方法について説明します。

- 会社を Azure Certified Device プログラムにオンボードする。
- デバイスに適用できる認定を判断する。
- プログラムの要件とその他のリソースを見つけて、テストを開始する。
- Azure Certified Device ポータルを使用してデバイスを確認する。

## <a name="onboarding"></a>オンボード

[Azure Certified Device ポータル](https://aka.ms/acdp)でデバイスを認定するには、次の手順を完了する必要があります。

1. 職場または学校のテナントを使用して、会社が Azure Active Directory アカウントを持っていることを確認します。
2. ご自分のアカウントを使用して、[Microsoft Partner Network (MPN)](https://partner.microsoft.com/) に参加します。
3. MPN に参加した後、[Azure Certified Device ポータル](https://aka.ms/acdp)にサインインします。
4. 会社のプロファイル ページで、[プログラム契約](https://aka.ms/acdagreement)を確認して署名します。

### <a name="company-profile"></a>会社のプロファイル

Azure Certified Device ポータルで会社のプロファイルを管理するには、 **[会社プロファイル]** を選択します。 会社のプロファイルには、会社の URL、電子メール アドレス、およびロゴが含まれています。 認定操作を続行する前に、このページでプログラム契約に同意します。

Azure Certified Device カタログ内のデバイスの説明ページで、会社のプロファイル情報が使用されます。

## <a name="choose-the-certification"></a>認定を選択する

3 種類の認定があり、それぞれ異なる顧客価値を提供することに重点が置かれています。 ビルドしているデバイスの種類と対象ユーザーに応じて、1 つまたは複数の最も適した認定を選択できます。

### <a name="azure-certified-device"></a>Azure Certified Device

"_Azure Certified Device 認定_" により、デバイスが Azure IoT Hub に接続し、Device Provisioning Service (DPS) を介して安全にプロビジョニングできることが検証されます。 この認定は、より高度な認定を行うために必要なベースラインである、デバイスの機能と相互運用性を反映しています。

- 詳細については、[認定要件](https://aka.ms/acdrequirements)に関するページを参照してください。
- DPS を使用してデバイスを Azure IoT Hub に接続する方法の詳細については、[デバイスのプロビジョニングの概要](../iot-dps/about-iot-dps.md)に関するページを参照してください。

### <a name="iot-plug-and-play"></a>IoT プラグ アンド プレイ

"_IoT プラグ アンド プレイ認定_" は Azure Certified Device 認定に対する増分認証であり、これにより、カスタム デバイス コードを使用せずにデバイスをビルドするプロセスが簡略化されます。 IoT プラグ アンド プレイを使用すると、ハードウェア パートナーは、Azure IoT Central やサードパーティのソリューションに基づいてクラウド ソリューションと簡単に統合できるデバイスをビルドできます。

- 詳細については、[認定要件](https://aka.ms/acdiotpnprequirements)に関するページを参照してください。
- IoT プラグ アンド プレイ テスト用にデバイスを準備する方法の詳細については、「[IoT プラグ アンド プレイ デバイスを認定する方法](howto-certify-device.md)」を参照してください。

### <a name="edge-managed"></a>Edge Managed

"_Edge Managed 認定_" は Azure Certified Device 認定に対する増分認定であり、Windows、Linux、または RTOS を実行する Azure IoT デバイスのデバイス管理標準に焦点が当てられます。 現時点では、このプログラム認定では、モジュールのデプロイと管理における Edge ランタイムの互換性に焦点が当てられます。

> [!TIP]
> このプログラムは、以前は "_IoT Edge 認定プログラム_" と呼ばれていました。

- 詳細については、[認定要件](https://aka.ms/acdedgemanagedrequirements)に関するページを参照してください。
- IoT Edge の詳細については、[IoT Edge の概要](../iot-edge/about-iot-edge.md)に関するページを参照してください。
- サポートされているオペレーティング システムおよびコンテナーの詳細については、[IoT Edge サポート システム](../iot-edge/support.md)に関するページを参照してください。

## <a name="use-the-azure-certified-device-portal"></a>Azure Certified Device ポータルを使用する

このセクションでは、[Azure Certified Device ポータル](https://certify.azure.com)を使用する方法の概要を示します。 詳細については、[ポータルのファースト ステップ ガイド](https://aka.ms/acdhelp)を参照してください。

Azure Certified Device プログラムでデバイスを認定するには、次の 4 つの手順を実行します。

1. デバイスの詳細を入力する
2. デバイスをテストする
3. レビューを送信して完了する
4. Azure Certified Device カタログに発行する (省略可能)

### <a name="provide-device-details"></a>デバイスの詳細を入力する

認定対象のデバイスごとに、認定ポータルのフォームを使用して、デバイス ハードウェア、セットアップ手順、およびマーケティング資料の詳細を記録します。

- **デバイス情報:** デバイスに関する情報 (その名前、説明、ハードウェアの詳細、オペレーティング システムなど) を収集します。
- **ファースト ステップ ガイド**:顧客が製品をすばやく使用するために使用できる PDF ドキュメント。 [サンプル テンプレート](https://aka.ms/GSTemplate)を使用できます。
- **マーケティングの詳細:** 画像やディストリビューターの情報など、デバイスに関する顧客向けのマーケティング情報を提供します。
- **その他の業界認定:** デバイスによって取得されたその他の認定に関する情報を提供するための、省略可能なセクション。

### <a name="test-the-device"></a>デバイスをテストする

このフェーズではデバイスとのやりとりが行われ、デバイスが DPS を使用して IoT Hub に接続された後、一連のテストが実行されます。 完了すると、デバイスのテスト結果と共に一連のログ ファイルを表示できます。

認定ポータルには、テストに使用する IoT Hub インスタンスに接続する方法が記載されています。 DPS 接続を確立するには、[サポートされている構成証明方法](../iot-dps/concepts-service.md#attestation-mechanism)のいずれかを使用します。

Azure Certified Device チームは、デバイスをさらに手動で検証するためにデバイス ビルダーに問い合わせることができます。

### <a name="submit-and-publish"></a>送信と発行

最後に必要なステージは、レビューのためにプロジェクトを送信することです。 この手順では、Azure Certified Device チーム メンバーに、デバイスとマーケティングの詳細やファースト ステップ ガイドを含め、完全性のためにプロジェクトをレビューするように通知します。 チーム メンバーは、事前に質問に関して指定された会社のメール アドレスに問い合わせたり、承認前に要求を編集したりできます。

デバイスで認定の一環としてさらに手動による検証が必要な場合は、現時点で通知を受け取ります。

デバイスが認定されると、製品概要ページの **[Publish to catalog]\(カタログに発行\)** 機能を使用して、Azure Certified Device カタログに製品の詳細を発行することができます。

## <a name="if-you-have-questions"></a>質問がある場合

認定プログラム、認定ポータル、または Azure Certified Device カタログに関して質問がある場合は、チーム ([iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)) にお問い合わせください。

## <a name="next-steps"></a>次のステップ

Azure Certified Device プログラムの概要について理解できたので、次の手順として、[IoT プラグ アンド プレイ デバイスを認定する方法](howto-certify-device.md)を学習することをお勧めします。