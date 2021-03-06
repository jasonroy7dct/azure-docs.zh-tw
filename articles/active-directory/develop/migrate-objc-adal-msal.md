---
title: ADAL 至 MSAL 遷移指南（MSAL iOS/macOS） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解 iOS/macOS 的 MSAL 與 ObjectiveC 的 Azure AD 驗證程式庫之間的差異（ADAL）。ObjC），以及如何遷移至適用于 iOS/macOS 的 MSAL。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085170"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>將應用程式遷移至適用于 iOS 和 macOS 的 MSAL

建立 Azure Active Directory Authentication Library （[ADAL 目標-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)）以透過 v1.0 端點與 Azure Active Directory 帳戶搭配使用。

適用于 iOS 和 macOS 的 Microsoft Authentication Library （MSAL）是專為使用 microsoft 身分識別平臺（如 Azure Active Directory （Azure AD）帳戶、個人 Microsoft 帳戶，以及透過 Microsoft 身分識別平臺 Azure AD B2C 帳戶（正式Azure AD v2.0 端點）。

Microsoft 身分識別平臺與 Azure Active Directory v1.0 有一些主要的差異。 本文重點說明這些差異，並提供將應用程式從 ADAL 遷移至 MSAL 的指引。

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 和 MSAL 應用程式功能差異

### <a name="who-can-sign-in"></a>誰可以登入

* ADAL 僅支援工作和學校帳戶，也稱為 Azure AD 帳戶。
* MSAL 支援個人 Microsoft 帳戶（MSA 帳戶），例如 Hotmail.com、Outlook.com 和 Live.com。
* MSAL 支援工作和學校帳戶，以及 Azure AD B2C 帳戶。

### <a name="standards-compliance"></a>標準合規性

* Microsoft 身分識別平臺端點遵循 OAuth 2.0 和 OpenId Connect 標準。

### <a name="incremental-and-dynamic-consent"></a>增量和動態同意

* Azure Active Directory v1.0 端點需要在應用程式註冊期間預先宣告擁有權限。 這表示這些許可權是靜態的。
* Microsoft 身分識別平臺可讓您以動態方式要求許可權。 應用程式可以視需要要求許可權，並在應用程式需要時要求更多許可權。

如需 Azure Active Directory v1.0 與 Microsoft 身分識別平臺之間差異的詳細資訊，請參閱[為何要更新至 microsoft 身分識別平臺（v2.0）？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

## <a name="adal-and-msal-library-differences"></a>ADAL 與 MSAL 程式庫的差異

MSAL 公用 API 會反映 Azure AD v1.0 與 Microsoft 身分識別平臺之間的一些主要差異。

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication 而不是 ADAuthenticationCoNtext

`ADAuthenticationContext` 是 ADAL 應用程式所建立的第一個物件。 它代表 ADAL 的具現化。 應用程式會針對每個 Azure Active Directory 雲端和租使用者（授權單位）組合，建立新的 `ADAuthenticationContext` 實例。 您可以使用相同的 `ADAuthenticationContext` 來取得多個公用用戶端應用程式的權杖。

在 MSAL 中，主要的互動是透過在[OAuth 2.0 公用用戶端](https://tools.ietf.org/html/rfc6749#section-2.1)之後模型化的 `MSALPublicClientApplication` 物件。 `MSALPublicClientApplication` 的一個實例可以用來與多個 AAD 雲端和租使用者互動，而不需要為每個授權單位建立新的實例。 對於大部分的應用程式而言，一個 `MSALPublicClientApplication` 實例就已足夠。

### <a name="scopes-instead-of-resources"></a>範圍，而不是資源

在 ADAL 中，應用程式必須提供*資源*識別碼（例如 `https://graph.microsoft.com`）來取得來自 Azure Active Directory v1.0 端點的權杖。 資源可以在應用程式資訊清單中定義一些範圍，或在其瞭解的 oAuth2Permissions。 這可讓用戶端應用程式針對在應用程式註冊期間預先定義的一組特定範圍，要求該資源的權杖。

在 MSAL 中，應用程式會針對每個要求提供一組範圍，而不是單一資源識別碼。 範圍是資源識別碼，後面接著許可權名稱，其格式為資源/許可權。 例如， `https://graph.microsoft.com/user.read`

有兩種方式可以在 MSAL 中提供範圍：

* 提供您的應用程式所需的擁有權限清單。 例如： 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    在此情況下，應用程式會要求 `directory.read` 並 `directory.write` 許可權。 系統會要求使用者同意這些許可權，但在此應用程式之前尚未同意。 應用程式可能也會收到使用者已同意應用程式的其他許可權。 系統只會提示使用者同意新許可權或尚未授與的許可權。

* `/.default` 範圍。

這是每個應用程式的內建範圍。 它是指註冊應用程式時所設定的靜態許可權清單。 其行為類似于 `resource`。 這在遷移時很有用，可確保維護一組類似的範圍和使用者體驗。

若要使用 `/.default` 範圍，請將 `/.default` 附加至資源識別碼。 例如： `https://graph.microsoft.com/.default` 。 如果您的資源以斜線（`/`）結尾，您仍然應該附加 `/.default`，包括前置正斜線，因而導致範圍中具有雙正斜線（`//`）。

您可以在[這裡](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)閱讀更多有關使用 "/.default" 範圍的資訊

### <a name="supporting-different-webview-types--browsers"></a>& 瀏覽器支援不同的 Web 類型

ADAL 僅支援適用于 iOS 的 UIWebView/WKWebView，以及 macOS 的 Web 程式。 MSAL for iOS 支援在要求授權碼時顯示 web 內容的更多選項，不再支援 `UIWebView`;這可以改善使用者體驗和安全性。

根據預設，iOS 上的 MSAL 會使用[ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)，這是 Apple 建議在 iOS 12 + 裝置上進行驗證的 web 元件。 它透過應用程式與 Safari 瀏覽器之間的 cookie 共用，提供單一登入（SSO）權益。

根據應用程式需求和您想要的使用者體驗，您可以選擇使用不同的 web 元件。 如需更多選項，請參閱[支援的 web 檢視類型](customize-webviews.md)。

從 ADAL 遷移至 MSAL 時，`WKWebView` 提供與 iOS 和 macOS 上的 ADAL 最相似的使用者體驗。 我們鼓勵您盡可能遷移至 iOS 上的 `ASWebAuthenticationSession`。 針對 macOS，我們鼓勵您使用 `WKWebView`。

### <a name="account-management-api-differences"></a>帳戶管理 API 差異

當您呼叫 ADAL 方法 `acquireToken()` 或 `acquireTokenSilent()`時，您會收到一個 `ADUserInformation` 物件，其中包含 `id_token` 中代表所要驗證之帳戶的宣告清單。 此外，`ADUserInformation` 會根據 `upn` 宣告傳回 `userId`。 取得初始互動式權杖之後，ADAL 預期開發人員會在所有無訊息呼叫中提供 `userId`。

ADAL 不會提供 API 來取得已知的使用者身分識別。 它依賴應用程式來儲存和管理這些帳戶。

MSAL 提供一組 Api 來列出已知 MSAL 的所有帳戶，而不需要取得權杖。

就像 ADAL 一樣，MSAL 會傳回保存來自 `id_token`之宣告清單的帳戶資訊。 它是 `MSALResult` 物件內 `MSALAccount` 物件的一部分。

MSAL 會提供一組 Api 來移除帳戶，讓移除的帳戶無法供應用程式存取。 移除帳戶之後，稍後的權杖取得呼叫將會提示使用者執行互動式權杖取得。 帳戶移除僅適用于啟動它的用戶端應用程式，而且不會從裝置上執行的其他應用程式或從系統瀏覽器移除帳戶。 這可確保即使在登出個別應用程式之後，使用者仍然可以在裝置上使用 SSO 體驗。

此外，MSAL 也會傳回可用來在稍後以無訊息方式要求權杖的帳戶識別碼。 不過，帳戶識別碼（可透過 `MSALAccount` 物件中的 `identifier` 屬性存取）無法顯示，而且您無法在嘗試解讀或剖析它時，假設其格式為何。

### <a name="migrating-the-account-cache"></a>遷移帳戶快取

從 ADAL 進行遷移時，應用程式通常會儲存 ADAL 的 `userId`，而不會有 MSAL 所需的 `identifier`。 在一次性的遷移步驟中，應用程式可以使用 ADAL 的 userId 搭配下列 API 來查詢 MSAL 帳戶：

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

此 API 會讀取 MSAL 和 ADAL 的快取，以依 ADAL userId （UPN）尋找帳戶。

如果找到帳戶，開發人員應該使用此帳戶來執行無訊息的權杖取得。 第一個無訊息的取得會有效地升級帳戶，而開發人員會在 MSAL 結果（`identifier`）中取得 MSAL 相容的帳戶識別碼。 之後，使用下列 API，只應將 `identifier` 用於帳戶查閱：

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

雖然可以繼續針對 MSAL 中的所有作業使用 ADAL 的 `userId`，因為 `userId` 是以 UPN 為基礎，所以受限於導致使用者體驗不佳的多項限制。 例如，如果 UPN 變更，使用者必須重新登入。 建議所有的應用程式都使用非可顯示帳戶 `identifier` 進行所有作業。

深入瞭解快取[狀態遷移](sso-between-adal-msal-apps-macos-ios.md)。

### <a name="token-acquisition-changes"></a>權杖取得變更

MSAL 引進了一些權杖取得呼叫的變更：

* 就像 ADAL 一樣，`acquireTokenSilent` 一律會產生無訊息的要求。
* 與 ADAL 不同的是，`acquireToken` 一律會透過 web view 或 Microsoft Authenticator 應用程式，使使用者可操作的 UI。 視 web Microsoft Authenticator 中的 SSO 狀態而定，系統可能會提示使用者輸入其認證。
* 在 ADAL 中，具有 `AD_PROMPT_AUTO` 的 `acquireToken` 會先嘗試無訊息的權杖取得，而且只有在無訊息要求失敗時，才會顯示 UI。 在 MSAL 中，您可以藉由先呼叫 `acquireTokenSilent` 來達到此邏輯，而且只有在無訊息取得失敗時，才呼叫 `acquireToken`。 這可讓開發人員自訂使用者體驗，再開始進行互動式權杖取得。

### <a name="error-handling-differences"></a>錯誤處理差異

MSAL 可讓您的應用程式所能處理的錯誤和需要使用者介入的錯誤之間更清楚。 開發人員必須處理的錯誤數目有限：

* `MSALErrorInteractionRequired`：使用者必須執行互動式要求。 這可能是因為各種原因所致，例如過期的驗證會話、條件式存取原則已變更、重新整理權杖已過期或已撤銷、快取中沒有有效的權杖等等。
* `MSALErrorServerDeclinedScopes`：要求未完全完成，且未授與某些範圍的存取權。 這可能是因為使用者拒絕一或多個範圍的同意所造成。

處理[`MSALError` 清單](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中的所有其他錯誤是選擇性的。 您可以使用這些錯誤中的資訊來改善使用者體驗。

如需 MSAL 錯誤處理的詳細資訊，請參閱[使用 MSAL 處理例外狀況和錯誤](msal-handling-exceptions.md)。

### <a name="broker-support"></a>Broker 支援

從版本0.3.0 開始，MSAL 可支援使用 Microsoft Authenticator 應用程式進行代理驗證。 Microsoft Authenticator 也會啟用條件式存取案例的支援。 條件式存取案例的範例包括需要使用者透過 Intune 註冊裝置或向 AAD 註冊以取得權杖的裝置合規性政策。 和行動應用程式管理（MAM）條件式存取原則，在您的應用程式可以取得權杖之前，需要證明合規性。

若要為您的應用程式啟用 broker：

1. 為應用程式註冊 broker 相容的重新導向 URI 格式。 Broker 相容的重新導向 URI 格式為 `msauth.<app.bundle.id>://auth`。 以您應用程式的套件組合識別碼取代 `<app.bundle.id>`。 如果您是從 ADAL 進行遷移，而且您的應用程式已經可以進行 broker，則不需要執行任何額外的動作。 先前的重新導向 URI 與 MSAL 完全相容，因此您可以跳到步驟3。

2. 將應用程式的重新導向 URI 配置新增至 plist 檔案。 若為預設的 MSAL 重新導向 URI，格式為 `msauth.<app.bundle.id>`。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. 將下列配置新增至您應用程式的資訊。 plist 下的 LSApplicationQueriesSchemes：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. 將下列內容新增至您的 AppDelegate 檔案以處理回呼：目標-C：
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift：
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>企業對企業（B2B）

在 ADAL 中，您會為應用程式要求權杖的每個租使用者建立個別的 `ADAuthenticationContext` 實例。 這不再是 MSAL 的需求。 在 MSAL 中，您可以建立 `MSALPublicClientApplication` 的單一實例，並將它用於任何 AAD 雲端和組織，方法是為 acquireToken 和 acquireTokenSilent 呼叫指定不同的授權單位。

## <a name="sso-in-partnership-with-other-sdks"></a>與其他 Sdk 合作的 SSO

適用于 iOS 的 MSAL 可以透過具有下列 Sdk 的統一快取來達到 SSO：

- ADAL 目標-C 2.7. x +
- 適用于 Xamarin 2.4. x + 的 MSAL.NET
- 適用于 Xamarin 4.4. x + 的 ADAL.NET

SSO 是透過 iOS keychain 共用來達成，只有從相同的 Apple 開發人員帳戶發佈的應用程式才可使用。

SSO 到 iOS keychain 共用是唯一的無訊息 SSO 類型。

在 macOS 上，MSAL 可以針對 iOS 和 macOS 應用程式和 ADAL 以 C 為基礎的應用程式，與其他 MSAL 達成 SSO。

IOS 上的 MSAL 也支援兩種其他類型的 SSO：

* 透過網頁瀏覽器進行 SSO。 MSAL for iOS 支援 `ASWebAuthenticationSession`，可透過裝置上的其他應用程式之間共用的 cookie 來提供 SSO，特別是 Safari 瀏覽器。
* 透過驗證訊息代理程式的 SSO。 在 iOS 裝置上，Microsoft Authenticator 會作為驗證代理人。 它可以遵循條件式存取原則，例如要求符合規範的裝置，並為已註冊的裝置提供 SSO。 從版本0.3.0 開始的 MSAL Sdk 預設支援訊息代理程式。

## <a name="intune-mam-sdk"></a>Intune MAM SDK

從版本[11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)開始， [Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started)支援 MSAL for iOS

## <a name="msal-and-adal-in-the-same-app"></a>相同應用程式中的 MSAL 和 ADAL

在同一個應用程式中，2.7.0 和更新版本的 ADAL 無法與 MSAL 並存。 主要的原因是共用的子模組通用程式碼。 因為目標-C 不支援命名空間，所以如果您將 ADAL 和 MSAL 架構加入至應用程式，則會有兩個相同類別的實例。 不保證會在執行時間挑選哪一個。 如果這兩個 Sdk 都使用相同版本的衝突類別，您的應用程式可能仍然可以使用。 不過，如果它是不同的版本，您的應用程式可能會遇到非預期的損毀，而難以診斷。

不支援在同一個生產應用程式中執行 ADAL 和 MSAL。 不過，如果您只是要測試您的使用者，並將其從 ADAL-C 遷移至 MSAL for iOS 和 macOS，您可以繼續使用[Adal 目標-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)。 這是在相同應用程式中搭配 MSAL 使用的唯一版本。 此 ADAL 版本不會有任何新的功能更新，因此應該僅用於遷移和測試用途。 您的應用程式不應該依賴 ADAL 和 MSAL 共存長期。

不支援相同應用程式中的 ADAL 和 MSAL 共存。
完全支援多個應用程式之間的 ADAL 和 MSAL 共存。

## <a name="practical-migration-steps"></a>實際的遷移步驟

### <a name="app-registration-migration"></a>應用程式註冊遷移

您不需要變更現有的 AAD 應用程式，即可切換至 MSAL 並啟用 AAD 帳戶。 不過，如果您的 ADAL 應用程式不支援代理驗證，則您必須先註冊應用程式的新重新導向 URI，才能切換至 MSAL。

重新導向 URI 應採用下列格式： `msauth.<app.bundle.id>://auth`。 以您應用程式的套件組合識別碼取代 `<app.bundle.id>`。 在[Azure 入口網站](https://aka.ms/MobileAppReg)中指定 [重新導向 URI]。

僅適用于 iOS，若要支援以憑證為基礎的驗證，必須在您的應用程式中註冊額外的重新導向 URI，並以下列格式登錄 Azure 入口網站： `msauth://code/<broker-redirect-uri-in-url-encoded-form>`。 例如， `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

建議所有應用程式都註冊這兩個重新導向 Uri。

如果您想要新增對累加式同意的支援，請在 [ **api 許可權**] 索引標籤下，選取您的應用程式已設定為要求存取權的 api 和許可權。

如果您要從 ADAL 進行遷移，而且想要同時支援 AAD 和 MSA 帳戶，則必須更新現有的應用程式註冊，以支援這兩者。 我們不建議您更新現有的生產應用程式，以立即支援 AAD 和 MSA。 相反地，請建立另一個支援 AAD 和 MSA 以進行測試的用戶端識別碼，並在確認所有案例都能正常執行之後，更新現有的應用程式。

### <a name="add-msal-to-your-app"></a>將 MSAL 新增至您的應用程式

您可以使用慣用的封裝管理工具，將 MSAL SDK 新增至您的應用程式。 請參閱[這裡的詳細指示](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)。

### <a name="update-your-apps-infoplist-file"></a>更新您應用程式的 plist 檔案

僅適用于 iOS，請將應用程式的重新導向 URI 配置新增至 plist 檔案。 對於 ADAL broker 相容應用程式，它應該已經存在。 預設的 MSAL 重新導向 URI 配置會採用下列格式： `msauth.<app.bundle.id>`。  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

將下列配置新增至您應用程式的資訊. plist 下的 `LSApplicationQueriesSchemes`。

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>更新您的 AppDelegate 程式碼

僅適用于 iOS，將下列內容新增至您的 AppDelegate 檔案：

Objective-C：

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift：

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**如果您使用的是 Xcode 11**，則應該改為將 MSAL 回呼放入 `SceneDelegate` 檔案。
如果您同時支援 UISceneDelegate 和 UIApplicationDelegate 以便與舊版 iOS 相容，則必須將 MSAL 回呼放入這兩個檔案。

Objective-C：

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift：

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

這可讓 MSAL 處理來自訊息代理程式和 web 元件的回應。
這在 ADAL 中不是必要的，因為它會自動「swizzled」應用程式委派方法。 以手動方式加入會較不容易發生錯誤，並提供應用程式更多控制權。

### <a name="enable-token-caching"></a>啟用權杖快取

根據預設，MSAL 會快取 iOS 或 macOS 金鑰鏈中的應用程式權杖。 

啟用權杖快取：
1. 確定您的應用程式已正確簽署
2. 移至您的 Xcode 專案設定 > [功能] 索引標籤  >  [啟用 Keychain 共用]
3. 按一下 [ **+** ]，然後輸入下列**Keychain 群組**專案： 3. 若是 iOS，請輸入 `com.microsoft.adalcache` 3. b macOS 輸入 `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>建立 MSALPublicClientApplication，並切換至其 acquireToken 和 acquireTokeSilent 呼叫

您可以使用下列程式碼來建立 `MSALPublicClientApplication`：

Objective-C：

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

然後呼叫帳戶管理 API，以查看快取中是否有任何帳戶：

Objective-C：

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift：

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



或讀取所有帳戶：

Objective-C：

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift：

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



如果找到帳戶，請呼叫 MSAL `acquireTokenSilent` API：

Objective-C：

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift：

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
