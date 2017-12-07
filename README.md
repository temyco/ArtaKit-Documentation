# ArtaKit Documentation
ARTA - Augmented Reality for Retail.

ArtaKit (ARTA iOS SDK) allows you to easily integrate augmented reality experiences of your products from ARTA AR CLOUD into your existing shopping apps for iPhone and/or iPad. ArtaKit handles augmented reality preview, access management, download and caching of the virtual products. 


## Installation and Setup

#### ApiKey
Go to [ARTA Website](http://arta.azurewebsites.net) and create subscription to obtain ApiKey.

#### Cocoa pods
Install SSZipArchive - popular, fast and simple utility class for zipping and unzipping files.

In your Podfile:
```pod 'SSZipArchive'```

## Usage

Initialize ArtaKit with your ApiKey:
```swift
import ArtaKit

// Call this func once in the main entry point of your application
ArtaKit.instance.initialize(apiKey: "YOUR_API_KEY")
```
Additionally you can enable some useful debug information:
- Set log level for ArtaKit to see messages in output. By default log level is none.
```swift
ArtaKit.instance.setLogLevel(logLevel: ArtaLogLevel)
```

- Enable augmented debug mode for ArtaKit to display a point cloud showing intermediate results of the scene analysis with a coordinate axis visualization indicating the position and orientation of the AR world coordinate system. By default debug mode is disabled.
```swift
ArtaKit.instance.augmentedDebugEnabled = true
```

## Initialization example
Enabled Info log level and augmented debug mode:
```swift
ArtaKit.instance.initialize(apiKey: "YOUR_API_KEY");
ArtaKit.instance.setLogLevel(logLevel: .info)
ArtaKit.instance.augmentedDebugEnabled = true
```

## Implementation steps

1. Load virtual product by product code:
```swift
import ArtaKit

ArtaKit.instance.downloadVirtualProduct(productCode: "PRODUCT_CODE")
```
2. Implement **ArtaKitProductLoaderDelegate** to observe download progress and completion. It has three optional functions:
```swift
import ArtaKit

// provides real-time information about current download progress
public func productLoadProgress(progress: Float, totalBytesWritten: Int64, totalBytesExpectedToWrite: Int64)

// called when virtual product download is completed
public func productLoadCompleted(success: Bool, path: String)

// called when error occurred while download process
public func productLoadCompletedWithError(code: ArtaClientError, message: String)
```
3. Implement **productLoadCompleted** callback function from **step 2** to present augmented reality view controller
```swift
func productLoadCompleted(success: Bool, path: String) {
   DispatchQueue.main.async {
      guard let viewController = ArtaKitVirtualProductViewController.instantiate(productCode: "PRODUCT_CODE") else {
         // something went wrong with view controller instantiation
         return
      }
      self.present(viewController, animated: true, completion: nil)
   }
}
```

ArtaKit gives additional features to improve user experience, such as checking if virtual product exists in ARTA AR CLOUD (without downloading virtual model files):
```swift
import ArtaKit

ArtaKit.instance.hasVirtualProduct(productCode: "PRODUCT_CODE") { (hasProduct) in
   if hasProduct {
      // virtual product exists in cloud catalogue
   }
}
```

## Cache management
In addition, framework has lightweight and handy features for managing device cache, such as eliminating and free-up resources, in just a single call:
```swift
ArtaKit.instance.clearCache()
```

## Handling messages
ArtaKit provides an ability to handle different kinds of warnigns / errors. This can be done through **ArtaKitErrorMessagingDelegate**, which has two optional functions:
```swift
onARKitError(code: ArtaKitError, message: String)
onNetworkError(code: ArtaClientError, message: String)
```

## Enums description for handling messages:
#### ArtaKitError
```
errorARSession
(augmented reality session failed to initialize)
```

#### ArtaClientError
```
apiKeyRequired
(apiKey was not found or was not set)

insufficientStorage
(smth went wrong while saving virtual product)

unauthorized
(apiKey not valid)

requestTimeout
(request to cloud was timeouted)

downloadProductFailed
(failed download virtual product from cloud)
```

## Supported devices:
- [x] iPhone 6s and 6s Plus
- [x] iPhone 7 and 7 Plus
- [x] iPhone SE
- [x] iPad Pro (9.7, 10.5 or 12.9) – both first-gen and 2nd-gen
- [x] iPad (2017)
- [x] iPhone 8 and 8 Plus
- [x] iPhone X

## Supported iOS versions:
- [x] Apple’s iOS 11 and above

