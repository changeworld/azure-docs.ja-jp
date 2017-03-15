---
title: "Azure Stream Analytics ジョブを使用したサービスの中断の回避 | Microsoft Docs"
description: "Stream Analytics ジョブの障害耐性をアップグレードさせるガイダンスです。"
services: stream-analytics
documentationCenter: 
authors: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/27/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: a2892343432f7dced535efb3917d915736580dfb
ms.openlocfilehash: cf8eba0f68e1e803026079f02b91f1bbaec189da
ms.lasthandoff: 03/01/2017

---

# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>サービス更新中におけるStream Analytics ジョブの信頼性を保証する

完全に管理されたサービスの一部は、サービスの新機能と機能強化を迅速なペースで紹介する機能です。 そのため、Stream Analytics は週単位で (またはより頻繁に) サービスの更新をデプロイできます。 どれだけテストが行われても、バグが組み込まれることで実行中である既存のジョブが停止するリスクは依然として存在します。 重要なストリーミング処理ジョブを実行するユーザーは、これらのリスクを回避する必要があります。 このリスクを軽減するために利用できるメカニズムは、Azure の**[ペア リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**モデルです。 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure ペア リージョンによるこの問題への対応方法について

Stream Analytics では、ペア リージョンのジョブは個別のバッチで更新されることが保証されています。 そのため、潜在的な停止バグを認識する更新とその修正との間に十分な時間差が生じます。

_インド中部の例外を除き_ (ペア リージョンであるインド南部では Stream Analytics プレゼンスがない)、Stream analytics に対する更新のデプロイは、1 組のペア リージョン内では同時に実行されません。 **同じグループ内**の複数のリージョンにおけるデプロイは**同時に**起こる可能性があります。

ペア グループの一覧に関する以下の図をご覧ください。

グループ A リージョン |  | グループ B リージョン
------- | ------- | -------
東日本 | 対にして | 西日本
北ヨーロッパ |  | 西ヨーロッパ
米国中央部 |  | 米国東部&2;
東アジア |  | 東南アジア
米国中北部 |  | 米国中南部
オーストラリア東部 |  | オーストラリア東南部
米国東部 |  | 米国西部
ブラジル南部 |  | 米国中南部
中国 (北部) |  | 中国 (東部)
ドイツ北東部 |  | ドイツ中部

両方のペア リージョンに同じジョブをデプロイすることをお勧めします。 Stream Analytics の内部監視に加えて、**両方**が運用ジョブであるように監視することをお勧めします。 停止が Stream Analytics サービスの更新の結果起こったと特定された場合、サポートを適切に拡大し、正常なジョブ出力のためにダウンストリームの顧客をフェールオーバーします。 サポートの拡大によってペア リージョンが新たなデプロイによって影響を受けることを防ぎ、ペアになっているジョブの整合性を保ちます。

