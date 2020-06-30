---
title: Azure Notification Hubs と REST API を使用して Swift iOS アプリにプッシュ通知を送信する
description: このチュートリアルでは、Azure Notification Hubs と REST API を使用して iOS デバイスにプッシュ通知を送信する方法について学習します。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127265"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>チュートリアル:Notification Hubs REST API を使用して Swift iOS アプリにプッシュ通知を送信する

このチュートリアルでは、REST API を使用して iOS アプリケーションにプッシュ通知を送信するために、Azure Notification Hubs を使用する方法について説明します。

このチュートリアルに含まれる手順は次のとおりです。

- サンプル iOS アプリを作成します。
- iOS アプリを Azure Notification Hubs に接続します。
- テスト プッシュ通知を送信します。
- アプリが通知を受信することを確認します。

このチュートリアルの完全なコードは、[GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples) からダウンロードできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。

- iOS バージョン 10 以降を実行している iPhone または iPad。

- [Apple ポータル](https://developer.apple.com/) に登録され、自分の証明書に関連付けられている物理デバイス。

作業を進める前に、[iOS アプリ向けに Azure Notification Hubs](https://go.microsoft.com/fwlink/?linkid=2129801) の使用を開始することに関する前のチュートリアルを終え、通知ハブでプッシュ資格情報の設定と構成を行ってください。 今までに iOS 開発の経験がなくても、以下の手順に従うことができます。

> [!NOTE]
> プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、iOS エミュレーターではなく物理 iOS デバイス (iPhone または iPad) で行う必要があります。

以下のセクションでは、通知ハブに接続する iOS アプリを作成します。

## <a name="create-an-ios-project"></a>iOS プロジェクトを作成する

1. Xcode で、新しい iOS プロジェクトを作成し、 **[Single View Application]**   テンプレートを選択します。

2. 新しいプロジェクトのオプションを設定するときは:
   - Apple Developer ポータルで**バンドル識別子** を設定するときに使用した **製品名**  (PushDemo) と **組織 ID** (`com.<organization>`) を指定します。
   -  **アプリ ID** の設定対象となっている **チーム** を選択します。
   -  **言語** は **Swift**に設定します。
   -  **[次へ]** を選択します。

3.  **SupportingFiles**という名前の新しいフォルダーを作成します。

4.  **SupportingFiles** フォルダー内に **devsettings.plist** という名前の新しい p-list ファイルを作成します。 このフォルダーは、git リポジトリを操作するときにコミットされないように、必ず **gitignore** ファイルに追加してください。 運用アプリでは、自動ビルド プロセスの一部としてこれらのシークレットを条件付きで設定する場合があります。 これらの設定については、このチュートリアルでは説明しません。

5. プロビジョニングした通知ハブからの独自の値を使用して **devsettings.plist** を更新し、以下の構成エントリが含まれるようにます。

   | **キー**                  | **Type** | **Value**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | notificationHubKeyName   | String   | `<hubKeyName>`   |
   | notificationHubName      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   Azure portal の通知ハブ リソースに移動して、必要な値を見つけることができます。 特に、 **notificationHubName**  値と  **notificationHubNamespace**  値は、 **[概要]**   ページ内の  **[要点]**   サマリーの右上隅にあります。

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="必要な値":::

    **notificationHubKeyName**  値と  **notificationHubKey**  値は、 **[アクセス ポリシー]**   に移動し、 **DefaultFullSharedAccessSignature** などの **アクセス ポリシー**を選択して見つけることもできます。 その後、 **プライマリ接続文字列** から、 **notificationHubKeyName**の `SharedAccessKeyName=` がプレフィックスとして付けられている値と、 **notificationHubKey**の `SharedAccessKey=` がプレフィックスとして付けられている値をコピーします。 接続文字列は、次の形式にする必要があります。

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   簡潔になるように、 **DefaultFullSharedAccessSignature**を指定し、トークンを使用して通知を送信できるようにします。 実際には、通知を受信したいだけの状況では、 **DefaultListenSharedAccessSignature** がより適切な選択肢です。

6.  **プロジェクト ナビゲーター**で、 **[プロジェクト名]**   を選択してから、 **[全般]**   タブを選択します。

7.  **[Identity] (ID)**   を見つけて、 **[Bundle Identifier] (バンドル識別子)**   の値を、前の手順で  **[アプリ ID]**   に使用した値である  `com.<organization>.PushDemo` と一致するように設定します。

8.  **[Signing & Capabilities] (署名と機能)** を見つけて、お使いの **Apple Developer アカウント**にとって適切な **チーム** を選択します。  **[チーム]**   の値は、自分の証明書とプロファイルを作成したときのものに一致させます。

9. Xcode により、 **バンドル識別子**に基づいて適切な **プロビジョニング プロファイル** の値が自動的にダウンロードされます。 新しい **プロビジョニング プロファイル** の値が表示されない場合は、 **[Xcode]**  **[Preferences] (環境設定)**  **[アカウント]** を選択することで  **[Signing Identity] (署名 ID)**   のプロファイルの更新を試みてから、 **[Download Manual Profiles] (手動プロファイルのダウンロード)**   ボタンを選択してプロファイルをダウンロードします。

10. 引き続き  **[Signing & Capabilities] (署名と機能)**   タブで、 **[+ Capability] (+ 機能)**   ボタンをクリックし、一覧の  **[プッシュ通知]**   をダブルタップして  **[プッシュ通知]**   が有効になっているようにします。

11.  **AppDelegate.swift** ファイルを開いて **UNUserNotificationCenterDelegate** プロトコルを実装し、クラスの先頭に次のコードを追加します。

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

       ...

       var configValues: NSDictionary?
       var notificationHubNamespace : String?
       var notificationHubName : String?
       var notificationHubKeyName : String?
       var notificationHubKey : String?
       let tags = ["12345"]

       ...

    }
    ```

    後でこれらのメンバーを使用します。 具体的には、 **カスタム テンプレート**を使用した登録の一部として **tags** メンバーを使用します。 タグの詳細については、 [登録用のタグ](notification-hubs-tags-segment-push-message.md) に関するページと [テンプレートの登録](notification-hubs-templates-cross-platform-push-messages.md)に関するページを参照してください。

12. 同じファイルで、**didFinishLaunchingWithOptions** 関数に次のコードを追加します。

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
       if let configValues = NSDictionary(contentsOfFile: path) {
           self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
           self.notificationHubName = configValues["notificationHubName"] as? String
           self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
           self.notificationHubKey = configValues["notificationHubKey"] as? String
       }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    このコードでは、 **devsettings.plist**から設定を取得し、 **AppDelegate** クラスを **UNUserNotificationCenter** デリゲートとして設定します。次に、プッシュ通知の承認を要求してから、 **registerForRemoteNotifications**を呼び出します。

    簡潔になるように、コードでは iOS 10 以降のみがサポートされています。 通常行うように、対応する API とアプローチを条件付きで使用することで、以前の iOS バージョンのサポートを追加できます。

13. 同じファイルで、次のコードを追加します。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    このコードでは、 **installationId** 値と **pushChannel** 値を使用して通知ハブに登録します。 この場合は、デバイスを識別するための一意の値を、 **UIDevice.current.identifierForVendor** を使用して指定してから、必要な **pushChannel** 値を指定するために **deviceToken** を書式設定します。  **showAlert** 関数は、デモンストレーション目的のメッセージ テキストをいくつか表示するためだけに存在します。

14. 引き続き **AppDelegate.swift** ファイルで、 **UNUserNotificationCenterDelegate**に、 **willPresent** 関数と **didReceive** 関数を追加します。 これらの関数により、フォアグラウンドまたはバックグラウンドのどちらかでアプリが実行中であることが通知されるとアラートが表示されます。

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter,
        willPresent notification: UNNotification,
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter,
        didReceive response: UNNotificationResponse,
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

15.  **didRegisterForRemoteNotificationsWithDeviceToken** 関数の末尾に `print` ステートメントを追加し、 **installationId** と **pushChannel** が割り当てられた値になっていることを確認します。

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. 後でプロジェクトに追加する基本コンポーネント用のフォルダーとして **Models**、 **Services**、および **Utilities** を作成します。

17. プロジェクトが物理デバイス上でビルドおよび実行されることを確認します。 プッシュ通知は、シミュレーターを使用してテストすることはできません。

## <a name="create-models"></a>モデルを作成する

この手順では、 [Notification Hubs REST API](/rest/api/notificationhubs/) ペイロードを表し、必要な共有アクセス署名 (SAS) トークン データを格納するためのモデルのセットを作成します。

1.  **PushTemplate.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このモデルでは、 **DeviceInstallation** ペイロードの一部として、個々のテンプレートの **本文** を表す構造体を定義します。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2.  **DeviceInstallation.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このファイルでは、 **デバイス インストール**を作成または更新するためのペイロードを表す構造体を定義します。 次のコードをファイルに追加します。

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

3.  **TokenData.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このモデルは、SAS トークンを、その有効期限と一緒に格納するために使用されます。 次のコードをファイルに追加します。

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

## <a name="generate-a-sas-token"></a>SAS トークンを生成する

Notification Hubs では、Azure Service Bus と同じセキュリティ インフラストラクチャが使用されます。 REST API を呼び出すには、要求の **Authorization** ヘッダーで使用できる[SAS トークンをプログラムで生成する](/rest/api/eventhub/generate-sas-token) 必要があります。

結果のトークンは次の形式になります。

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

プロセス自体には、6 つの同じ手順が必要です。

1. 有効期限は、 [UNIX エポック時間](https://en.wikipedia.org/wiki/Unix_time) 形式、つまり、1970 年 1 月 1 日の午前 0 時 (世界協定時) から経過した秒数で計算します。

2. アクセスしようとしているリソースを表す  **ResourceUrl** を書式設定して、パーセントでエンコードされた小文字表記にします。  **ResourceUrl** の形式は`https://<namespace>.servicebus.windows.net/<hubName>`となります。

3.  **StringToSign**を準備します。これは `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` のように書式設定されています。

4.  **StringToSign** 値の HMAC SHA256 ハッシュを使用して、 **署名** の計算と Base64 エンコードを行います。 ハッシュ値は、それぞれの **承認規則**の、 **接続文字列** の **キー** 部分と共に使用されます。

5. Base64 でエンコードされた **署名** を書式設定して、パーセントでエンコードされた表記にします。

6.  **UrlEncodedSignature**値、 **ExpiryEpoch**値、 **KeyName**値、および **UrlEncodedResourceUrl** 値を使用して、想定されている形式でトークンを構築します。

共有アクセス署名の概要全般と、それが Azure Service Bus や Notification Hubs でどのように使用されているかについては、 [Azure Service Bus のドキュメント](../service-bus-messaging/service-bus-sas.md) を参照してください。

この Swift の例では、その目的上、署名のハッシュ処理に役立てるため、Apple のオープンソースの **CommonCrypto** ライブラリを使用します。 これは C ライブラリであるため、そのままでは Swift で使用できません。 ブリッジ ヘッダーを使用すると、ライブラリを使用できるようになります。

ブリッジ ヘッダーを追加して構成するには:

1. Xcode で、 **[ファイル]** 、 **[New] (新規)** 、 **[ファイル]** の順に選択してから、 **[ヘッダー ファイル]** を選択します。 ヘッダー ファイルの名前を **BridgingHeader.h**にします。

2.  **CommonHMAC.h**をインポートするようにファイルを編集します。

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. ブリッジ ヘッダーを参照するようにターゲットの **ビルド設定** を更新します。

   1.  **[PushDemo]**   プロジェクトを開き、 **[Swift コンパイラ]**   セクションまで下へスクロールします。

   2.  **[Install Objective-C Compatibility Header]\(Objective-C 互換性ヘッダーをインストールする\)**   オプションが  **[Yes]\(はい\)** に設定されていることを確認します。

   3. ファイル パス `<ProjectName>/BridgingHeader.h` を  **[Objective-C bridging Header] (Objective-C ブリッジ ヘッダー)**   オプションに入力します。 これがブリッジ ヘッダーのファイル パスです。

   これらのオプションが見つからない場合は、 **[Basic] (基本)**   や  **[Customized] (カスタマイズ)** ではなく  **[すべて]**   ビューが選択されていることを確認してください。

    **CommonCrypto** の利用が少し容易になる可能性があるサードパーティ製のオープンソース ラッパー ライブラリが多数あります。 ただしこの記事では、これらのライブラリについて説明しません。

4.  **TokenUtility.swift** という名前の新しい Swift ファイルを **Utilities** フォルダー内に追加し、次のコードを追加します。

   ```swift
   import Foundation

   struct TokenUtility {
      typealias Context = UnsafeMutablePointer<CCHmacContext>

      static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
        let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
        let encodedUrl = urlEncodedString(withString: resourceUrl)
        let stringToSign = "\(encodedUrl)\n\(expiry)"
        let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
        let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
        let encodedSignature = urlEncodedString(withString: signature)
        let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
        let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

        return tokenData
      }

      private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
         let context = Context.allocate(capacity: 1)
         CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
         CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
         var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
         CCHmacFinal(context, &hmac)

         let result = NSData(bytes: hmac, length: hmac.count)
         context.deallocate()

         return result as Data
      }

      private static func urlEncodedString(withString stringToConvert : String) -> String {
         var encodedString = ""
         let sourceUtf8 = (stringToConvert as NSString).utf8String
         let length = strlen(sourceUtf8)

         let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
         let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

         for i in 0..<length {
             let currentChar = sourceUtf8![i]

             if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                 encodedString += String(format:"%c", currentChar)
             }
             else {
                 encodedString += String(format:"%%%02x", currentChar)
             }
         }

         return encodedString
      }
   }
   ```

   このユーティリティでは、SAS トークンの生成を担うロジックをカプセル化しています。

   前に概要を説明したように、 **getSasToken** 関数は、トークンを準備するために必要とされる大まかな手順を調整します。 この関数は、このチュートリアルの後半でインストール サービスによって呼び出されます。

    **getSasToken** 関数によって、他の 2 つの関数が呼び出されます。署名を計算するための **sha256HMac** と、関連付けられた URL 文字列をエンコードするための **urlEncodedString** です。  **urlEncodedString** 関数が必要なのは、組み込みの **addingPercentEncoding** 関数を使用しても必要な出力が得られないからです。

    [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) は、Objective-C でこれらの操作にアプローチする方法の優れた例です。 Azure Service Bus SAS トークンの詳細については、 [Azure Service Bus に関するドキュメント](../service-bus-messaging/service-bus-sas.md)を参照してください。

5.  **AppDelegate.swift**で、 `didRegisterForRemoteNotificationsWithDeviceToken` 関数に次のコードを追加して、 **TokenUtility.getSasToken** によって有効なトークンを生成中であることを確認します。

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

    **baseAddress** 文字列のプレースホルダーは、実際の独自の値に置き換えてください。

## <a name="verify-the-sas-token"></a>SAS トークンを検証する

クライアントにインストール サービスを実装する前に、HTTP ユーティリティを使用して、アプリから正常に SAS トークンが生成されていることを確認します。 このチュートリアルのために Microsoft が選んだツールは **Postman**です。

アプリによって生成された **installationId** 値と **token** 値をメモしておきます。

次の手順に従って、 **installations** API を呼び出します。

1.  **Postman**で新しいタブを開きます。
2.  **GET** の要求を設定し、次のアドレスを指定します。

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. 次のように要求ヘッダーを構成します。

   | **キー**       | **Value**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 承認 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 右上の  **[保存]**   ボタンの下に表示される  **[コード]**   ボタンを選択します。 要求は次の例のようになります。

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5.  **[送信]**   ボタンを選択します。

この時点では、指定した **installationId** の登録は存在しません。 検証の結果は、"401 Unauthorized" 応答ではなく "404 Not Found" 応答になります。 この結果により、SAS トークンが受け入れられたことを確認できます。

## <a name="implement-the-installation-service-class"></a>インストール サービス クラスを実装する

次に、 [Installations REST API](/rest/api/notificationhubs/create-overwrite-installation)の基本的なラッパーを実装します。

 **NotificationRegistrationService.swift** という名前の新しい Swift ファイルを **Services** フォルダーの下に追加してから、このファイルに次のコードを追加します。

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil &&
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {

            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)

            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

必要条件の詳細については、初期化の一部として提供されています。 タグとテンプレートは、オプションで **register** 関数に渡され、 **デバイスのインストール** に関する JSON ペイロードの一部を形成します。

 **register** 関数では、他のプライベート関数を呼び出して要求を準備します。 応答が受信された後、completion が呼び出され、登録が成功したかどうかを示します。

要求エンドポイントは **getBaseAddress** 関数によって構築されます。 構築には、初期化時に指定された通知ハブ パラメーターの *namespace* と *name* が使用されます。

 **getSasToken** 関数では、現在格納されているトークンが有効かどうかを調べます。 トークンが有効でない場合、関数は値を返す前に **TokenUtility** を呼び出して、新しいトークンを生成してそれを格納します。

最後に、 **encodeToJson** により、要求本文の一部として使用するために、それぞれのモデル オブジェクトが JSON に変換されます。

## <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API を呼び出す

最後の手順では、 **NotificationRegistrationService** を使用して **NotificationHub**に登録するように **AppDelegate** を更新します。

1.  **AppDelegate.swift** を開き、 **NoficiationRegistrationService** と汎用の **PushTemplate**への参照を格納するクラス レベルの変数を追加します。

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. 同じファイルで、必要なパラメーターを使用して **NotificationRegistrationService** を初期化してから **register** 関数を呼び出すように、 **didRegisterForRemoteNotificationsWithDeviceToken** 関数を更新します。

   ```swift
   func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    let installationId = (UIDevice.current.identifierForVendor?.description)!
    let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

    // Initialize the Notification Registration Service
    self.registrationService = NotificationRegistrationService(
        withInstallationId: installationId,
        andPushChannel: pushChannel,
        andHubNamespace: notificationHubNamespace!,
        andHubName: notificationHubName!,
        andKeyName: notificationHubKeyName!,
        andKey: notificationHubKey!)

    // Call register, passing in the tags and template parameters
    self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
        if !result {
            print("Registration issue")
        } else {
            print("Registered")
          }
       }
   }
   ```

   簡潔になるように、コードでは `print` ステートメントを使用して、 **register** 操作の結果によって出力ウィンドウを更新しています。

3. 物理デバイスでアプリをビルドして実行します。 出力ウィンドウに "**Registered**" と表示されます。

## <a name="test-the-solution"></a>ソリューションをテストする

この時点でアプリは **NotificationHub** に登録されており、プッシュ通知を受信することができます。 Xcode で、デバッガーを停止し、アプリが現在実行中の場合はアプリを終了します。 次に、 **デバイスのインストール** の詳細が想定したとおりであること、およびアプリがプッシュ通知を受信できるようになっていることを確認します。

### <a name="verify-the-device-installation"></a>デバイスのインストールを確認する

これで、 [SAS トークンの検証](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token)のために **Postman** を使用して、以前に行ったのと同じ要求を作成できます。 SAS トークンの有効期限が切れていないと仮定すると、応答には、指定したインストールの詳細 (テンプレート、タグなど) が含まれるようになっているはずです。

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

以前の SAS トークンの有効期限が切れている場合は、 **TokenUtility** クラスの行 24 にブレークポイントを追加して新しい SAS トークンを取得し、その新しい値で **Authorization** ヘッダーを更新することができます。

### <a name="send-a-test-notification-azure-portal"></a>テスト通知を送信する (Azure portal)

通知を受信できるようになったことをテストする最も簡単な方法は、Azure portal の通知ハブを参照することです。

1. Azure portal で、お使いの通知ハブの **[概要]**   タブに移動します。

2.  **[テスト送信]** を選択します。これは、ポータル ウィンドウ左上の、 **[要点]**   サマリーの上にあります。

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Notification Hubs の [要点] サマリーの [テスト送信]":::

3.  **[プラットフォーム]**   の一覧から  **[カスタムテンプレート]**   を選択します。

4.  **[Send to Tag Expression] (タグ式に送信)** には、「 **12345** 」と入力します。 このタグは、インストールで以前指定したものです。

5. オプションで、JSON ペイロード内の **message** を編集します。

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs でのテスト送信":::

6.  **[送信]** を選択します。 ポータルに、通知がデバイスに正常に送信されたかどうかが示されます。

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="テスト送信の結果":::

   アプリがフォアグラウンドで実行中ではないと仮定すると、お使いのデバイスの **通知センター** にも通知が表示されるはずです。 通知をタップすると、アプリが開き、アラートが表示されます。

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="テスト通知":::

### <a name="send-a-test-notification-mail-carrier"></a>テスト通知を送信する (メール通信事業者)

 **Postman**を使用して、 [REST API](/rest/api/notificationhubs/) を介して通知を送信できます。これは、より便利なテスト方法の可能性があります。

1.  **Postman**で新しいタブを開きます。

2. 要求を **POST**に設定し、次のアドレスを入力します。

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. 次のように要求ヘッダーを構成します。

   | Key                           | 値                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | 承認                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Tags                          | "12345"                        |

4. 次の JSON ペイロードを指定して、 **RAW - JSON (application.json)**  を使用するように要求の **本文** を構成します。

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5.  **[コード]**   ボタンを選択します。これはウィンドウ右上の  **[保存]**   ボタンの下にあります。 要求は次の例のようになります。

   ```xml
   POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json;charset=utf-8.
   ServiceBusNotification-Format: template
   Tags: "12345"
   Authorization: <sasToken>
   Cache-Control: no-cache
   Postman-Token: <postmanToken>

   {
       "message" : "Hello from Postman!"
   }
   ```

6.  **[送信]**   ボタンを選択します。

 **201 作成されました** という成功状態コードが取得され、クライアント デバイスで通知が受信されるはずです。

## <a name="next-steps"></a>次のステップ

これで、 [Notification Hubs REST API](/rest/api/notificationhubs/)を介して通知ハブに接続される基本的な iOS Swift アプリが用意されて、通知を送受信できるようになりました。 特定のデバイスに通知を送信する方法についてさらに学習するには、次のチュートリアルに進んでください。

- [チュートリアル:特定のデバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

詳細については、次の記事を参照してください。

- [Azure Notification Hubs の概要](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [バックエンド操作用の Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上の Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
- [アプリケーション バックエンドへの登録](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [登録管理](notification-hubs-push-notification-registration-management.md)
- [タグの使用](notification-hubs-tags-segment-push-message.md)
- [カスタム テンプレートの使用](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus access control with shared access signatures](../service-bus-messaging/service-bus-sas.md) (共有アクセス署名による Service Bus のアクセスの制御)
- [プログラムによる SAS トークンの生成](/rest/api/eventhub/generate-sas-token)
- [Apple のセキュリティ: 一般的な暗号化](https://developer.apple.com/security/)
- [UNIX エポック時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
