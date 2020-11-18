---
title: Red Hat Enterprise Linux 延長ライフサイクル サポート
description: Red Hat Enterprise 延長ライフサイクル サポート アドオンを追加する方法について説明します
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: e7c9f9b158d01204536712f8024ed098cd7a5037
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372859"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) Extended Lifecycle Support
この記事では、次の Red Hat Enterprise イメージに対する延長ライフサイクル サポートについて説明します。
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 のライフサイクル
2020 年 11 月 30 日をもって、Red Hat Enterprise Linux 6 のメンテナンス フェーズは終了します。 メンテナンス フェーズの後に、延長ライフサイクル フェーズが続きます。 Red Hat Enterprise Linux 6 はフルまたはメンテナンスのフェーズから移行するため、Red Hat Enterprise Linux 7 または 8 にアップグレードすることを強くお勧めします。 顧客が Red Hat Enterprise Linux 6 を使い続ける必要がある場合は、Red Hat Red Hat Enterprise Linux Extended Life Cycle Support (ELS) アドオンを追加することをお勧めします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6 を実行しています。現時点では、新しいバージョンに移行することはできません。 どのようなオプションがありますか
* 引き続き Red Hat Enterprise Linux 6 を実行し、Extended Life Cycle Support (ELS) アドオン リポジトリを購入すると、制限されたソフトウェア メンテナンスとテクニカル サポートを引き続き受けることができます (アップグレードのプロセスと価格の詳細については、以下を参照してください)。
* できるだけ早く Red Hat Enterprise Linux 7 または 8 に移行してください。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Red Hat Enterprise Linux Extended Life Cycle Support (ELS) アドオンを使用する場合、追加料金はいくらですか?

|VM サイズ|追加料金の時間枠|追加の金額 (米国ドル)| Notes|
|---|---|---|---|
| 小規模な仮想ゲスト (4 コア以下) | 1 時間ごとの追加料金 | 未定 | |
|  | 1 か月ごとの追加料金 | 未定 | 予約インスタンス向け |
|  | 1 年ごとの追加料金 | 未定 | 予約インスタンス向け |
| 大規模な仮想ゲスト (5 コア以上) | 1 時間ごとの追加料金 | 未定 | |
|  | 1 か月ごとの追加料金 | 未定 | 予約インスタンス向け |
|  | 1 年ごとの追加料金 | 未定 | 予約インスタンス向け |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Extended Life Cycle Support (ELS) リポジトリを追加して、引き続きソフトウェア メンテナンス (バグとセキュリティの修正) を受信し、Red Hat Enterprise Linux 6 をサポートするプロセスは何ですか?

ELS にサインアップするエンドツーエンドのプロセスは、こちらでまもなく入手できるようになる予定です (最新情報では 2020 年 11 月 15 日までに)。

## <a name="next-steps"></a>次のステップ

* Azure の RHEL イメージの詳細な一覧については、「[Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ](./redhat-imagelist.md)」を参照してください。
* Azure Red Hat Update Infrastructure の詳細については、「[Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure](./redhat-rhui.md)」を参照してください。
* RHEL BYOS オファーの詳細については、「[Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image](./byos.md)」を参照してください。
* すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。