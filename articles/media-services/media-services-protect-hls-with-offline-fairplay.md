---
title: "オフラインの Apple FairPlay で HLS コンテンツを保護する - Azure | Microsoft Docs"
description: "このトピックでは、Azure Media Services を使って HTTP ライブ ストリーミング (HLS) コンテンツをオフライン モードの Apple FairPlay で動的に暗号化する方法の概要を説明します。"
services: media-services
keywords: "HLS, DRM, FairPlay Streaming (FPS), オフライン, iOS 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: b68ceac2056f0a9a7a9c4df7984789858c77a626
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="offline-fairplay-streaming"></a>オフラインの FairPlay Streaming
Microsoft Azure Media Services では、次のものを対象にして、適切に設計された[コンテンツ保護サービス](https://azure.microsoft.com/services/media-services/content-protection/)のセットが提供されます。
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 暗号化

コンテンツの DRM/AES 暗号化は、さまざまなストリーミング プロトコルの要求時に動的に実行されます。 DRM ライセンス/AES 暗号化解除キー配信サービスも、Azure Media Services によって提供されます。

さまざまなストリーミング プロトコルでのオンライン ストリーミングのコンテンツの保護だけでなく、保護されたコンテンツのオフライン モードも、よく要求される機能です。 オフライン モードのサポートは、次のシナリオで必要です。
1. 旅行中など、インターネット接続を利用できないときに再生します。
2. コンテンツ プロバイダーによっては、国境を超えた DRM ライセンス配信を許可しないことがあります。 ユーザーが外国旅行中にコンテンツの視聴を望む場合は、オフライン ダウンロードが必要です。
3. 一部の国では、インターネットの使用や帯域幅がまだ制限されています。 ユーザーは先にダウンロードしておくことで、満足できる十分に高い解像度でコンテンツを視聴できます。 通常、この場合の問題は、ネットワークを使えるかどうかではなく、ネットワーク帯域幅の制限です。 OTT/OVP プロバイダーは、オフライン モードのサポートを求めています。

この記事では、iOS 10 以降を搭載するデバイスを対象とする FairPlay Streaming (FPS) のオフライン モードのサポートについて説明します。 この機能は、watchOS、tvOS、Safari on macOS など、他の Apple プラットフォームではサポートされていません。

## <a name="preliminary-steps"></a>準備作業
iOS 10 以降のデバイスの FairPlay 用にオフライン DRM を実装する前に、次のことを行っておく必要があります。
1. FairPlay のオンライン コンテンツ保護について理解します。 これについては、次の記事/サンプルで詳しく説明されています。
- [Apple FairPlay Streaming for Azure Media Services の一般公開](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [Apple FairPlay または Microsoft PlayReady による HLS コンテンツの保護](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [オンライン FPS ストリーミングのサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Apple Developer Network から FPS SDK を入手します。 FPS SDK には、2 つのコンポーネントが含まれています。
- FPS Server SDK には、KSM (キー セキュリティ モジュール)、クライアントのサンプル、仕様、テスト ベクターのセットが含まれています。
- FPS Deployment Pack には、D 関数、仕様および FPS 証明書生成方法の説明、顧客固有の秘密キー、アプリケーション秘密鍵 (ASK) が含まれています。 Apple は、ライセンスのあるコンテンツ プロバイダーに対してのみ、FPS Deployment Pack を発行します。

## <a name="configuration-in-azure-media-services"></a>Azure Media Services での構成
[Azure Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) による FPS オフライン モードの構成の場合、Azure Media Services .NET SDK v. 4.0.0.4 以降を使う必要があります。この SDK では、FPS オフライン モードの構成に必要な API が提供されています。
上記の前提条件で示されているように、オンライン モードの FPS コンテンツ保護を構成するための動作するコードは既にあるものとします。 FPS のオンライン モード コンテンツ保護構成用のコードがある場合は、変更する必要があるのは次の 2 つだけです。

## <a name="code-change-in-fairplay-configuration"></a>FairPlay 構成でのコードの変更
"オフライン モードを有効にする" ブール値を、objDRMSettings.EnableOfflineMode という名前で定義します。オフライン DRM シナリオを有効にするときは、これを true にします。 このインジケーターに応じて、FairPlay の構成を次のように変更します。

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>資産の配信ポリシーの構成でのコード変更
2 番目の変更では、ディクショナリ Dictionary<AssetDeliveryPolicyConfigurationKey, string> に第 3 のキーを追加します。
3 番目の AssetDeliveryPolicyConfigurationKey は、次のように追加する必要があります。 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

このステップの後、FPS の資産の配信ポリシーの Dictionary<AssetDeliveryPolicyConfigurationKey, string> には、次の 3 つのエントリが含まれるようになります。
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl または AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl: どちらにするかは、使われている FPS KSM/キー サーバーや、同じ資産の配信ポリシーを複数の資産で再利用するかどうか、などの要因によります
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

これで、Media Services アカウントはオフライン FairPlay ライセンスを配信するように構成されました。

## <a name="sample-ios-player"></a>サンプル iOS プレーヤー
最初に、FPS オフライン モードのサポートは iOS 10 以降でのみ利用可能であることに注意してください。 FPS オフライン モードのドキュメントとサンプルが含まれている FPS Server SDK (v3.0 以降) を入手する必要があります。 具体的には、FPS Server SDK (v3.0 以降) には、オフライン モードに関連する次の 2 つの項目が含まれます。
1. ドキュメント: 『Offline Playback with FairPlay Streaming and HTTP Live Streaming』(FairPlay Streaming と HTTP ライブ ストリーミングでのオフライン再生)。 Apple、2016 年 9 月 14 日。 FPS Server SDK v 4.0 では、このドキュメントはFPS ストリーミングのメイン ドキュメントにまとめられています。
2. サンプル コード: FPS オフライン モードの HLSCatalog サンプル (\FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\)。 HLSCatalog サンプル アプリでは、特に次のコード ファイルがオフライン モード機能の実装に関係します。
- AssetPersistenceManager.swift コード ファイル: AssetPersistenceManager が、次の方法を示すこのサンプルのメイン クラスです
    - HLS ストリームのダウンロードを管理する方法。ダウンロードの開始やキャンセル、ユーザー デバイスからの既存資産の削除を行うための API など。
    - ダウンロードの進行状況を監視する方法。
- AssetListTableViewController.swift および AssetListTableViewCell.swift コード ファイル: AssetListTableViewController はこのサンプルのメイン インターフェイスです。 サンプルが再生、ダウンロード、削除、またはダウンロードのキャンセルを行うことができる資産の一覧を提供します。 

iOS プレーヤー実行セットアップの詳細な手順を次に示します。 FPS Server SDK v 4.0.1 の HLSCatalog サンプルから始めるものとします。  次のようにコードを変更する必要があります。

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift で、次のコードを使って `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` メソッドを実装します。変数 drmUr には、HLS ストリーミング URL を割り当てます。

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift で、`requestApplicationCertificate()` メソッドを実装します。 この実装は、デバイスに証明書 (公開キーのみ) を埋め込むか、Web 上で証明書をホストするかによって異なります。 次に示すのは、テスト サンプルで使われるホスト型アプリケーション証明書を使う実装です。 変数 certUrl には、アプリケーション証明書の URL が格納されます。

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

最終的な統合テストでのビデオ URL とアプリケーション証明書 URL については、「統合テスト」セクションで提供します。

HLSCatalog\Shared\Resources\Streams.plist では、テスト ビデオの URL を追加し、コンテンツ キー ID については、SKD プロトコルの FairPlay ライセンス取得 URL を一意値として使うことができます。

![オフライン FairPlay iOS アプリ ストリーム](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

テスト ビデオ URL、FairPlay ライセンス取得 URL、およびアプリケーション証明書 URL については、設定してある場合は独自のものを使うことができ、または次のセクションのテスト サンプルを使ってもかまいません。

## <a name="integrated-test"></a>統合テスト
次の 3 つのシナリオに対応する 3 つのテストのサンプルが、Azure Media Services に設定されています。
1.  FPS 保護、ビデオ、オーディオ、代替オーディオ トラックあり。
2.  FPS 保護、ビデオ、オーディオあり、代替オーディオ トラックなし。
3.  FPS 保護、ビデオのみ、オーディオなし。

これらのサンプルはこちらの[デモ サイト](http://aka.ms/poc#22)にあり、対応するアプリケーション証明書は Azure Web アプリでホストされています。
FPS Server SDK の v3 または v4 サンプルでは、マスター再生リストに代替オーディオが含まれる場合、オフライン モードのときに、オーディオのみが再生されることがわかっています。 そのため、代替オーディオを削除する必要があります。 つまり、上記の 3 つのサンプルのうち、(2) と (3) はオンライン モードとオフライン モードの両方で動作します。 しかし、(1) は、オフライン モードの間はオーディオのみが再生され、オンライン ストリーミングでは正常に動作します。

## <a name="faq"></a>FAQ
トラブルシューティングに関してよく寄せられる質問
- **オフライン モードの間は、オーディオのみが再生されて、ビデオは再生されないのはなぜですか。** この動作は、サンプル アプリの本来の設計のようです。 代替オーディオ トラックがある場合 (HLS の場合)、オフライン モードでは、iOS 10 と iOS 11 の両方が、既定で代替オーディオ トラックになります。FPS オフライン モードでこの動作を補正するには、ストリームから代替オーディオ トラックを削除する必要があります。 Azure Media Services 側でこれを行うには、動的マニフェスト フィルター "audio-only=false" を追加するだけです。 つまり、HLS URL の最後が .ism/manifest(format=m3u8-aapl,audio-only=false) になります。 
- **audio-only=false を追加した後もまだ、オフライン モードの間は、オーディオのみが再生されて、ビデオが再生されないのはなぜですか。** CDN キャッシュ キーの設計によっては、コンテンツがキャッシュされる場合があります。 キャッシュを消去する必要があります。
- **FPS オフライン モードは、iOS 10 だけでなく iOS 11 でもサポートされますか。** はい、FPS オフライン モードは iOS 10 と iOS 11 の両方でサポートされています。
- **ドキュメント『Offline Playback with FairPlay Streaming and HTTP Live Streaming』(FairPlay Streaming と HTTP ライブ ストリーミングでのオフライン再生) が FPS Server SDK で見つからないのはなぜですか。** FPS Server SDK v 4 以降、このドキュメントは『FairPlay Streaming Programming Guide』(FairPlay Streaming プログラミング ガイド) ドキュメントにまとめられています。
- **FPS オフライン モードの次の API で、最後のパラメーターは何を表していますか。**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

この API のドキュメントは[こちら](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)にあります。 このパラメーターはオフライン レンタルの期間を表します (単位は時間)。
- **iOS デバイスでのダウンロード済み/オフライン ファイルの構造はどのようなものですか。** iOS デバイスにダウンロードされたファイルは、次のような構造になっています (スクリーンショット)。 `_keys` フォルダーには、ダウンロードされた FPS ライセンスが格納されます (ライセンス サービス ホストごとに 1 つのストア ファイル)。 `.movpkg` フォルダーには、オーディオとビデオのコンテンツが格納されます。 名前の最後がダッシュと数字になっている最初のフォルダーには、ビデオのコンテンツが含まれます。 数字はビデオ再生の "PeakBandwidth" です。 名前の最後がダッシュと 0 になっている 2 番目のフォルダーには、オーディオのコンテンツが含まれます。 "Data" という名前の 3 番目のフォルダーには、FPS コンテンツのマスター再生リストが含まれます。 boot.xml では、`.movpkg` フォルダーの内容の完全な説明が提供されています (boot.xml ファイルのサンプルは以下を参照してください)。

![オフライン FairPlay の iOS サンプル アプリ ファイルの構造](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

boot.xml ファイルのサンプル:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>概要
このドキュメントでは、FPS オフライン モードの実装に関して、次のような詳細手順と情報を提供しました。
1. AMS .NET API による Azure Media Services のコンテンツ保護の構成。 AMS での動的 FairPlay 暗号化と FairPlay ライセンス配信を構成します。
2. Apple FPS Server SDK のサンプルに基づく iOS プレーヤー。 オンライン ストリーミング モードまたはオフライン モードで FPS コンテンツを再生できる iOS プレーヤーをセットアップします。
3. オフライン モードとオンライン ストリーミング両方のテスト用のサンプル FPS ビデオ。
4. FPS オフライン モードに関する FAQ。
