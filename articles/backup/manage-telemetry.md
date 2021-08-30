---
title: Microsoft Azure Backup Server (MABS) のテレメトリ設定の管理
description: この記事では、MABS のテレメトリ設定の管理方法について説明します。
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722642"
---
# <a name="manage-telemetry-settings"></a>テレメトリ設定の管理

>[!NOTE]
>この機能は、MABS V3 UR2 以降に適用されます。

この記事では、Microsoft Azure Backup Server (MABS) のテレメトリ (診断およびユーティリティのデータ) 設定の管理方法について説明します。

既定では、MABS は診断および接続のデータを Microsoft に送信します。 Microsoft はこのデータを使用して、Microsoft の製品およびサービスの品質、セキュリティ、整合性の提供と改善を行います。

管理者は、任意の時点でこの機能を無効にできます。 収集されるデータの詳細については、[次のセクション](#telemetry-data-collected)を参照してください。

## <a name="turn-onoff-telemetry-from-console"></a>コンソールからテレメトリのオン/オフを切り替える

1. Microsoft Azure Backup Server コンソールで、 **[管理]** に移動して上部ペインの **[オプション]** をクリックします。
1. **[オプション]** ダイアログ ボックスで、 **[診断データと使用状況データの設定]** を選択します。

    ![コンソール テレメトリ オプション](./media/telemetry/telemetry-options.png)

1. 表示されるオプションから、診断と使用状況データの共有の設定を選択し、 **[OK]** をクリックします。

    >[!NOTE]
    >オプションを選択する前に、「[プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)」を読むことをお勧めします。
    >- テレメトリをオンにするには、 **[はい、Microsoft にデータを送信します]** を選択します。
    >- テレメトリをオフにするには、 **[いいえ、Microsoft にデータを送信しません]** を選択します。

## <a name="telemetry-data-collected"></a>収集されるテレメトリ データ

| データの種類 | 収集されるデータ* |
| --- | --- |
| **セットアップ** | インストールされている MABS のバージョン。 <br/><br/>インストールされている MABS 更新プログラム ロールアップのバージョン。 <br/><br/> 一意のマシンの識別子。 <br/><br/> MABS がインストールされているオペレーティング システム。 <br/><br/> 一意のクラウド サブスクリプション識別子。<br/><br/> MABS エージェント バージョン。<br/><br/> 階層型ストレージが有効かどうか。 <br/><br/> 使用されているストレージのサイズ。 |
| **保護されたワークロード** | ワークロードの一意の識別子。 <br/><br/>バックアップされているワークロードのサイズ。 <br/><br/>ワークロードの種類とそのバージョン番号。 <br/><br/>ワークロードが現在 MABS により保護されているかどうか。 <br/><br/>ワークロードが保護されている保護グループの一意の識別子。<br/><br/> ワークロードがバックアップされる場所 (ディスク、テープ、またはクラウド)。|
| **ジョブ** | バックアップ/復元ジョブの状態。 <br/><br/> バックアップ/復元されたデータのサイズ。 <br/><br/>バックアップ/復元ジョブが失敗した場合のエラー メッセージ。<br/><br/> 復元ジョブにかかった時間。<br/><br/>バックアップ/復元ジョブが実行されたワークロードの詳細。 |
| **テレメトリの変更状態** | テレメトリ設定の状態変更の詳細、有効か無効か、タイミング。 |
| **MABS コンソール クラッシュ エラー** | MABS コンソールがクラッシュしたときのエラーの詳細。|

## <a name="next-steps"></a>次のステップ

[ワークロードの保護](./back-up-hyper-v-virtual-machines-mabs.md)