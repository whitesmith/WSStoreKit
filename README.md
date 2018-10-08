# WSStoreKit (The MKStoreKit Whitesmith version)

[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg)](https://github.com/Carthage/Carthage)

This is version 7.0 of WSStoreKit. iOS 9.3+ only.

MKStoreKit is a **complete revamp** is not API compatible with previous versions of MKStoreKit. Refactoring should however be fairly simple.

The `MKStoreKit` is a singleton class that takes care of *everything*. You then have to initialize it by calling `[[MKStoreKit sharedKit] startProductRequest]` in your `application:didFinishLaunchingWithOptions:`. From then on, it does the magic. The MKStoreKit purchases, remembers and even handles remote validation of auto-renewable subscriptions.

## Features

* Super simple in app purchasing
* Built-in support for remembering your purchases
* Built-in receipt validation (remote)
* Built-in virtual currency manager
* Built-in Hosted Content Download Manager

## Installation

#### <img src="https://cloud.githubusercontent.com/assets/432536/5252404/443d64f4-7952-11e4-9d26-fc5cc664cb61.png" width="24" height="24"> [Carthage]

[Carthage]: https://github.com/Carthage/Carthage

To install it, simply add the following line to your **Cartfile**:

```ruby
github "whitesmith/WSStoreKit"
```

Then run `carthage update`.

Follow the current instructions in [Carthage's README][carthage-installation]
for up to date installation instructions.

[carthage-installation]: https://github.com/Carthage/Carthage#adding-frameworks-to-an-application

#### Manually

The library contains just three files, `MKStoreKit.h/m` and `MKStoreKitConfigs.plist`. Just drag these four files into the project.

## Sample Code 

The Sample Code shows how to use MKStoreKit.

### Config File Format

`MKStoreKit` uses a config file, `MKStoreKitConfigs.plist` for managing your product identifiers.
The config file is a Plist dictionary containing three keys, "Consumables", "Others" and "SharedSecret".

_Consumables_ is the key where you provide a list of consumables in your app that should be managed as a virtual currency. _Others_ is the key where you provide a list of in app purchasable products. _SharedSecret_ is the key where you provide the shared secret generated on your App Store Connect account.

### Initialization

Initialization is as simple as calling
In **Objective-C**:

``` objective-c
[[MKStoreKit sharedKit] startProductRequest]
```

In **Swift**:

``` swift
MKStoreKit.sharedKit().startProductRequest()
```
A sample initializiation code that you can add to your `application:didFinishLaunchingWithOptions:` is below

In **Objective-C**:

``` objective-c
[[MKStoreKit sharedKit] startProductRequest];
  
[[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitProductsAvailableNotification
                                                  object:nil
                                                   queue:[[NSOperationQueue alloc] init]
                                              usingBlock:^(NSNotification *note) {
                                                  NSLog(@"Products available: %@", [[MKStoreKit sharedKit] availableProducts]);
                                              }];


[[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitProductPurchasedNotification
                                                  object:nil
                                                   queue:[[NSOperationQueue alloc] init]
                                              usingBlock:^(NSNotification *note) {
                                                  NSLog(@"Purchased/Subscribed to product with id: %@", [note object]);
                                              }];

[[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitRestoredPurchasesNotification
                                                  object:nil
                                                   queue:[[NSOperationQueue alloc] init]
                                              usingBlock:^(NSNotification *note) {
                                                  NSLog(@"Restored Purchases");
                                              }];

[[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitRestoringPurchasesFailedNotification
                                                  object:nil
                                                   queue:[[NSOperationQueue alloc] init]
                                              usingBlock:^(NSNotification *note) {
                                                  NSLog(@"Failed restoring purchases with error: %@", [note object]);
                                                  }];
```

In **Swift**:

``` swift
MKStoreKit.shared().startProductRequest()

NotificationCenter.default.addObserver(
    forName: NSNotification.Name.mkStoreKitProductsAvailable,
    object: nil,
    queue: OperationQueue.main,
    using: { note in
        print(MKStoreKit.shared().availableProducts)
    }
)

NotificationCenter.default.addObserver(
    forName: NSNotification.Name.mkStoreKitProductPurchased,
    object: nil,
    queue: OperationQueue.main,
    using: { note in
        print ("Purchased product: \(note.object ?? "none")")
    }
)
```

### Checking Product Status

You can check if a product was previously purchased using -isProductPurchased as shown below.

``` objective-c
if ([MKStoreManager isProductPurchased:productIdentifier]) {
    //unlock it
}
```

You can check for a product's expiry date using -expiryDateForProduct as shown below.

``` objective-c
if ([MKStoreManager expiryDateForProduct:productIdentifier]) {
    //unlock it
}
```

Warning: This method will return ```[NSNull null]``` for products that are not auto-renewing subscriptions.


### Making a Purchase
To purchase a feature or to subscribe to a auto-renewing subscription, just call

``` objective-c
[[MKStoreKit sharedKit] initiatePaymentRequestForProductWithIdentifier:productIdentifier];
```

Observe `kMKStoreKitProductPurchasedNotification` to get notified when the purchase completes

``` objective-c
[[NSNotificationCenter defaultCenter] addObserverForName:kMKStoreKitProductPurchasedNotification
                                                  object:nil
                                                   queue:[[NSOperationQueue alloc] init]
                                              usingBlock:^(NSNotification *note) {
                                                  NSLog(@"Purchased/Subscribed to product with id: %@", [note object]);
                                                  NSLog(@"%@", [[MKStoreKit sharedKit] valueForKey:@"purchaseRecord"]);
                                                  }];
```

It can't get simpler than this!

## Missing Features

* Keychain support for products
* Local receipt verification using asn1c

## Untested Features

* Non-renewing subscriptions
* Free subscriptions for news stand
* Mac OS X support

These three features might work, but they are not tested yet.

## Requirements

 - Xcode 10 (Swift 4.2)

### Licensing

MKStoreKit is licensed under MIT License
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

---

### Attribution free licensing

In case you need attribution free licensing for MKNetworkKit, you can buy one from [my license store](http://blog.mugunthkumar.com/license-store/).
