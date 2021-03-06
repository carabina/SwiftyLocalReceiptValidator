# Swifty Local Receipt Validator
This repository contains an example implementation of local receipt validation logic for iOS in Swift.

* [Prerequisites](#prerequisites)
* [Disclaimer](#disclaimer)
* [Usage](#usage)
	* [Output Types](#output-types)
	* [Call Site](#call-site)
* [Explanatory Guides](#explanatory-guides)

# Prerequisites
1. You need a copy of Apple's Root Certificate included in your application bundle for local receipt validation to succeed. I wrote "[Receipt Validation – Verifying a Receipt Signature in Swift](https://www.andrewcbancroft.com/2017/07/16/receipt-validation-verifying-a-receipt-signature-in-swift/)" to guide you through this process if you need help.
2. You need OpenSSL to be statically-linked to your project. I wrote "[OpenSSL for iOS & Swift the Easy Way](https://www.andrewcbancroft.com/2015/09/21/openssl-for-ios-swift-the-easy-way/)" to guide you through this process if you need help.

# Disclaimer
Preventing software piracy is hard. The code presented in this repository is not meant to protect you against unauthorized usage of your app or its features. This code is meant to be used for learning purposes only. If you use this code in your app, you do it at your own risk. 

You must take additional efforts to obfuscate the code presented here to thwart an attacker's attempt at circumventing the receipt validation logic contained within this repository.

# Usage
## Output Types
In order to make sense of the call site, I thought it might be helpful to include the *output* that you can expect from the `ReceiptValidator`:
```swift
enum ReceiptValidationResult {
	case success(ParsedReceipt)
	case error(ReceiptValidationError)
}

enum ReceiptValidationError : Error {
	case couldNotFindReceipt
	case emptyReceiptContents
	case receiptNotSigned
	case appleRootCertificateNotFound
	case receiptSignatureInvalid
	case malformedReceipt
	case malformedInAppPurchaseReceipt
	case incorrectHash
}

struct ParsedReceipt {
	let bundleIdentifier: String?
	let bundleIdData: NSData?
	let appVersion: String?
	let opaqueValue: NSData?
	let sha1Hash: NSData?
	let inAppPurchaseReceipts: [ParsedInAppPurchaseReceipt]?
	let originalAppVersion: String?
	let receiptCreationDate: Date?
	let expirationDate: Date?
}

struct ParsedInAppPurchaseReceipt {
	let quantity: Int?
	let productIdentifier: String?
	let transactionIdentifier: String?
	let originalTransactionIdentifier: String?
	let purchaseDate: Date?
	let originalPurchaseDate: Date?
	let subscriptionExpirationDate: Date?
	let cancellationDate: Date?
	let webOrderLineItemId: Int?
}
```
## Call Site
```swift
let receiptValidator = ReceiptValidator()
let validationResult = receiptValidator.validateReceipt()
		
switch validationResult {
case .success(let receipt):
  // Work with parsed receipt data. Possibilities might be...
    // enable a feature of your app
    // remove ads
    // etc...
case .error(let error):
  // Handle receipt validation failure. Possibilities might be...
    // use StoreKit to request a new receipt
    // enter a "grace period"
    // disable a feature of your app
    // etc...
}
```

# Explanatory Guides
Throughout the development of the code in this repository, I wrote up several guides at https://andrewcbancroft.com explain what each step along the way in the receipt validation process is doing. If you'd like to understand more about what's going on under the hood, you can read up on any step below:

* [Preparing to Test Receipt Validation for iOS](https://www.andrewcbancroft.com/2015/10/05/preparing-to-test-receipt-validation-for-ios/)
* [OpenSSL for iOS & Swift the Easy Way](https://www.andrewcbancroft.com/2015/09/21/openssl-for-ios-swift-the-easy-way/)
* [Loading a Receipt for Validation with Swift](https://www.andrewcbancroft.com/2015/10/13/loading-a-receipt-for-validation-with-swift/)
* [Extracting a PKCS7 Container for Receipt Validation with Swift](https://www.andrewcbancroft.com/2016/06/09/extracting-a-pkcs7-container-for-receipt-validation-with-swift/)
* [Receipt Validation – Verifying a Receipt Signature in Swift](https://www.andrewcbancroft.com/2017/07/16/receipt-validation-verifying-a-receipt-signature-in-swift/)
* [Receipt Validation – Parse and Decode a Receipt with Swift](https://www.andrewcbancroft.com/2017/07/27/receipt-validation-parsing-a-receipt-with-swift/)
* [Finalizing Receipt Validation in Swift – Computing a GUID Hash](https://www.andrewcbancroft.com/2017/07/31/finalizing-receipt-validation-in-swift-computing-a-guid-hash/)
