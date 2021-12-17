---
title: IoT プラグ アンド プレイ デバイスのトラブルシューティング
description: IoT プラグ アンド プレイ デバイスを認定するパートナー向けの推奨されるトラブルシューティング手順のガイド。
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e87051bb27e097f507435582cc919f05fd78aeb
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403061"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>IoT プラグ アンド プレイ認定プロジェクトのトラブルシューティング

IoT プラグ アンド プレイ認定プロジェクトの接続とテストのフェーズで、Azure for IoT Certification Service (AICS) テストに合格することを妨げるいくつかのシナリオが発生する場合があります。

## <a name="prerequisites"></a>前提条件

- [Azure Certified Device ポータル](https://certify.azure.com)にサインインして、そこでデバイス用のプロジェクトを作成する必要があります。 詳細については、[チュートリアル](tutorial-01-creating-your-project.md)を参照してください。

## <a name="when-aics-tests-arent-passing"></a>AICS テストに合格しない場合

複数の原因により、AIC テストに合格しない場合があります。 こちらの手順に従い、一般的な問題がないか確認し、デバイスをトラブルシューティングしてください。

1. DPS プロビジョニング中にデバイス コードでモデル ID ペイロードが設定されているか再確認します。 これは、AICS でデバイスを検証するために必要です。
1. テストが失敗した原因が何か特定するために、`View Logs` ボタンを押して、前に実行したテストのテレメトリ ログを表示できます。 テスト メッセージと生データの両方を確認できます。  

    ![テスト データを確認する](./media/images/review-logs.png)

1. ログで `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` が示される場合、デバイスを再起動して、デバイスのプロビジョニング プロセスを再開してください。
1. 自動テストが引き続き失敗する場合は、その結果の `request a manual review` を実行して置き換えることができます。 これにより、Azure Certified Device チームによる **手動検証** の要求がトリガーされます。  

    ![手動レビューを要求する](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>"合格しましたが、警告が発生しました" と表示される場合

テストの実行中に、`Passed with warnings` という結果を受け取った場合、テスト期間中に一部のテレメトリが受信されなかったことを意味します。 この原因として考えられるのは、長い時間間隔でのテレメトリの依存関係、または外部トリガーが使用不可であったことです。 レビューを受けるためにデバイスの提出に進むことができます。その間、レビュー チームは、今後 **手動検証** が必要があるかどうかを判断します。

## <a name="when-you-need-help-with-the-model-repository"></a>モデル リポジトリに関するヘルプが必要な場合

モデル リポジトリに関連する IoT プラグ アンド プレイの問題については、[デバイスのモデル リポジトリに関するドキュメント ガイダンス](../iot-develop/concepts-model-repository.md)を参照してください。

## <a name="next-steps"></a>次のステップ

このガイドは、IoT プラグ アンド プレイ認定の手順を続行する場合に役立ちます。 AICS に合格した後、チュートリアルに進んで、デバイスを提出および公開できます。

- [チュートリアル: デバイスをテストする](tutorial-03-testing-your-device.md)