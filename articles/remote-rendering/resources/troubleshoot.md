---
title: トラブルシューティング
description: Azure Remote Rendering のトラブルシューティング情報
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678829"
---
# <a name="troubleshoot"></a>トラブルシューティング

このページでは、Azure Remote Rendering に影響を及ぼす一般的な問題とその解決方法の一覧を示します。

## <a name="client-cant-connect-to-server"></a>クライアントがサーバーに接続できない

(デバイス上やルーター内などの) ファイアウォールが次のポートをブロックしていないことを確認します。

* **50051 (TCP)** - 初期接続 (HTTP ハンドシェイク) に必要です
* **8266 (TCP+UDP)** - データ転送に必要です
* **5000 (TCP)** 、**5433 (TCP)** 、**8443 (TCP)** - [ArrInspector](tools/arr-inspector.md) に必要です

## <a name="error-disconnected-videoformatnotavailable"></a>エラー '切断されました:VideoFormatNotAvailable'

GPU でハードウェアによる動画のデコードがサポートされていることを確認します。 「[開発用 PC](../overview/system-requirements.md#development-pc)」をご覧ください。

GPU を 2 基搭載したノート パソコンで作業している場合、既定で実行されている方の GPU がハードウェアによる動画のデコード機能を提供していない可能性があります。 その場合は、アプリで強制的にもう一方の GPU を使用するようにしてください。 多くの場合、この操作は GPU ドライバーの設定で実行できます。

## <a name="h265-codec-not-available"></a>H265 コーデックが使用できない

**コーデックが使用できない**というエラーでサーバーが接続を拒否する可能性のある理由は 2 つあります。

**H265 コーデックがインストールされていない:**

最初に、システム要件の「[ソフトウェア](../overview/system-requirements.md#software)」セクションに記載されている **HEVC Video Extensions** を必ずインストールしてください。

それでも問題が発生する場合は、使用しているグラフィックス カードで H265 がサポートされていて、最新のグラフィックス ドライバーがインストールされていることを確認してください。 ベンダー固有の情報については、システム要件の「[開発用 PC](../overview/system-requirements.md#development-pc)」セクションをご覧ください。

**H265 コーデックがインストールされているが、使用できない:**

この問題の原因は、DLL のセキュリティ設定が正しくないことです。 H265 でエンコードされた動画を視聴しようとした場合、この問題は発生しません。 コーデックを再インストールしても問題は解決されません。 代わりに、次の手順を実行してください。

1. **管理者権限を使用して PowerShell** を開き、次のコマンドを実行します

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    このコマンドによって、次のようなコーデックの `InstallLocation` が出力されます。
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows エクスプローラーでそのフォルダーを開きます
1. **x86** と **x64** のサブフォルダーがあります。 いずれかのフォルダーを右クリックして、 **[プロパティ]** を選択します
    1. **[セキュリティ]** タブを選択し、 **[詳細設定]** ボタンをクリックします
    1. **[所有者]** で **[変更]** をクリックします
    1. テキスト フィールドに「**Administrators**」と入力します
    1. **[名前の確認]** 、 **[OK]** の順にクリックします
1. もう一方のフォルダーについて、上記の手順を繰り返します
1. 同様に、両方のフォルダー内の DLL ファイルごとに上記の手順を繰り返します。 全部で 4 つの DLL があります。

設定が正しいことを検証するには、4 つの DLL ごとに次の手順を実行します。

1. **[プロパティ] > [セキュリティ] > [編集]** の順に選択します
1. すべての**グループまたはユーザー**の一覧を 1 行ずつ確認し、それぞれに **[読み取りと実行]** 権限が設定されていることを確認します ( **[許可]** 列のチェックマークがオンになっている必要があります)

## <a name="low-video-quality"></a>動画の画質が低下している

動画の画質は、ネットワーク品質または H265 動画コーデックの欠落によって低下する可能性があります。

* [ネットワークに関する問題を特定する](#unstable-holograms)ための手順をご覧ください。
* 最新のグラフィックス ドライバーのインストールについては、「[システム要件](../overview/system-requirements.md#development-pc)」をご覧ください。

## <a name="black-screen-after-successful-model-loading"></a>モデルの読み込みが成功した後にブラック スクリーンが発生する

レンダリング ランタイムに接続し、モデルが正常に読み込まれましたが、その後、ブラック スクリーンしか表示されない場合は、いくつかの明確な原因が考えられます。

詳細な分析を行う前に、次の点をテストすることをお勧めします。

* H265 コーデックがインストールされているか確認してください。 H264 コーデックへのフォールバックは用意されていますが、このフォールバックが正しく機能しなかったケースも生じています。 最新のグラフィックス ドライバーのインストールについては、「[システム要件](../overview/system-requirements.md#development-pc)」をご覧ください。
* Unity プロジェクトを使用している場合は、Unity を閉じて、プロジェクト ディレクトリ内の一時*ライブラリ*と *obj* フォルダーを削除してから、プロジェクトを再度読み込むかビルドしてください。 場合によっては、明確な理由もなく、キャッシュ データによってサンプルが正しく機能しなくなることがあります。

これらの 2 つの手順で問題が解決しなかった場合は、ビデオ フレームがクライアントで受信されているかどうかを確認する必要があります。 これは、「[サーバー側のパフォーマンス クエリ」](../overview/features/performance-queries.md)の章で説明しているように、プログラムでクエリできます。 `FrameStatistics struct` には、受信したビデオ フレームの数を示すメンバーがあります。 この数が 0 より大きく、時間の経過と共に増加している場合、クライアントは実際のビデオ フレームをサーバーから受信しています。 したがって、これはクライアント側の問題です。

### <a name="common-client-side-issues"></a>クライアント側の一般的な問題

**モデルがビューの視錐台の内側にない:**

多くの場合、そのモデルは正しく表示されますが、カメラの視錐台の外側にあります。 一般的な理由の 1 つは、そのモデルが中心から外れた遠くのピボットを使用してエクスポートされたために、カメラの遠クリップ面で切り取られてしまうことです。 これは、モデルの境界ボックスに対してプログラムでクエリを実行し、Unity でそのボックスを線のボックスとして視覚化したり、その値をデバッグ ログに出力したりするのに役立ちます。

**Unity のレンダリング パイプラインにレンダリング フックが含まれていない:**

Azure Remote Rendering では、動画を使用してフレーム合成を行ったり、再投影を行ったりするために、Unity のレンダリング パイプラインにフックします。 これらのフックが存在することを確認するには、メニューの *[Window] (ウィンドウ) > [Analysis] (分析) > [Frame debugger] (フレーム デバッガー)* を開きます。 これを有効にしてから、パイプライン内に `HolographicRemotingCallbackPass` のエントリが 2 つあることを確認します。

![Unity のフレーム デバッガー](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Remote Rendering API を使用する Unity コードがコンパイルされない

Unity ソリューションの*ビルドの種類*を **[デバッグ]** に切り替えます。 Unity エディターで ARR をテストする場合、`UNITY_EDITOR` の定義は 'デバッグ' ビルドでのみ使用できます。 これは、[デプロイされたアプリケーション](../quickstarts/deploy-to-hololens.md)に使用されるビルドの種類とは関係がないので注意してください。デプロイされたアプリケーションでは、'リリース' ビルドが推奨されます。

## <a name="unstable-holograms"></a>ホログラムが不安定である

レンダリングされたオブジェクトが頭部の動きに連動して動くように見える場合は、*Late Stage Reprojection* (LSR) に関する問題が発生している可能性があります。 このような状況への対処方法のガイダンスについては、[Late Stage Reprojection](../overview/features/late-stage-reprojection.md) に関するセクションをご覧ください。

不安定なホログラム (揺れ、ゆがみ、ジッター、ジャンプなどが発生しているホログラム) のもう 1 つの理由として、ネットワーク接続の問題、特にネットワーク帯域幅が不足していたり、待機時間が長すぎたりすることがあります。 ネットワーク接続の品質を示す適切な指標の 1 つは、[パフォーマンス統計](../overview/features/performance-queries.md)値 `ARRServiceStats.VideoFramesReused` です。 再利用されたフレームは、クライアント側で (パケットの損失やネットワーク待機時間のばらつきなどにより) 新しいビデオ フレームが利用できなかったために、前のビデオ フレームを再利用する必要があったという状況を示しています。 `ARRServiceStats.VideoFramesReused` が 0 より大きいことが多い場合は、ネットワークの問題を示しています。

確認すべきもう 1 つの値は `ARRServiceStats.LatencyPoseToReceiveAvg` です。 この値は、常に 100 ミリ秒未満である必要があります。 値がそれよりも大きい場合は、接続されているデータ センターが離れすぎていることを示しています。

考えられる軽減策の一覧については、[ネットワーク接続のガイドライン](../reference/network-requirements.md#guidelines-for-network-connectivity)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [システム要件](../overview/system-requirements.md)
* [ネットワークの要件](../reference/network-requirements.md)
