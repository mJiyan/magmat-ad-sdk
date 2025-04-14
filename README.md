# Magmat SDK Documentation

## Introduction

Magmat SDK is a custom solution designed to track user interactions with your ads and capture important conversion metrics. Whether you’re looking to track link clicks, ad impressions, or app installs, Magmat SDK provides an easy and efficient way to monitor user engagement with your campaigns. This documentation will guide you through setting up the SDK, integrating it with your app, and using the features effectively.

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Installation](#installation)
3. [SDK Features](#sdk-features)
4. [Tracking User Clicks](#tracking-user-clicks)
5. [Postback Integration (SKAdNetwork)](#postback-integration-skadnetwork)
6. [API References](#api-references)
7. [FAQs](#faqs)
8. [Support](#support)

---

## 1. Getting Started

To get started with Magmat SDK, you need to first include the SDK in your project. Follow the steps below to integrate it into your app:

- Ensure your app is set up to handle deep links (iOS: Universal Links, Android: App Links).
- Integrate the Magmat SDK into your app.

---

## 2. Installation

### iOS Setup

1. **Install via CocoaPods (iOS only):**

    ```ruby
    pod 'MagmatSDK', '~> 1.0'
    ```

2. **Manually (iOS):**

    Download the latest version of Magmat SDK from [Magmat SDK Release] and add the files to your Xcode project.

### Android Setup

1. **Add to your `build.gradle` file:**

    ```gradle
    implementation 'com.magmat.sdk:magmat-sdk:1.0.0'
    ```

2. **Manually (Android):**

    Download the latest version of Magmat SDK from [Magmat SDK Release] and add the files to your Android project.

---

## 3. SDK Features

Magmat SDK provides the following key features:

- **Link Click Tracking**: Capture when a user clicks on a deep link and log it to the server.
- **App Install Tracking via SKAdNetwork**: Track when the user installs the app after clicking on a link and receive postback data from SKAdNetwork.
- **Custom Event Tracking**: Monitor custom events inside the app that may be relevant to your campaigns.

---

## 4. Tracking User Clicks

To track when a user clicks on a custom deep link, integrate the SDK’s click tracking API. Here’s how you can capture a click event:

### Example for iOS

```swift
import MagmatSDK

func trackLinkClick(source: String, campaign: String) {
    MagmatSDK.trackClick(source: source, campaign: campaign) { success in
        if success {
            print("Click successfully recorded")
        } else {
            print("Failed to record click")
        }
    }
}
```
Example for Android

```java
import com.magmat.sdk.MagmatSDK;

public void trackLinkClick(String source, String campaign) {
    MagmatSDK.trackClick(source, campaign, new MagmatSDK.Callback() {
        @Override
        public void onSuccess() {
            Log.d("MagmatSDK", "Click successfully recorded");
        }

        @Override
        public void onFailure() {
            Log.d("MagmatSDK", "Failed to record click");
        }
    });
}
```
## 5. Postback Integration (SKAdNetwork)

Magmat SDK also supports SKAdNetwork for tracking app installs from ad campaigns. When a user installs the app after clicking on an ad, SKAdNetwork sends a postback to your server. You can integrate this postback information to correlate the clicks with installs.

### Handling Postbacks (Server-side)

Your server needs to handle SKAdNetwork postbacks. Here’s an example of how to handle the postback and correlate it with click data:

```js
app.post('/skadnetwork/postback', async (req, res) => {
    const { campaign, sourceId, adNetworkId, conversionValue } = req.body;

    const postback = new Postback({
        campaign,
        sourceId,
        adNetworkId,
        conversionValue
    });

    try {
        await postback.save();
        
        // Match postback with stored click
        const click = await Click.findOne({ campaign, source: sourceId }).sort({ timestamp: -1 }).limit(1);
        if (click) {
            console.log(`Matched Postback for Click: ${click._id}`);
        }

        res.status(200).send({ message: 'Postback recorded' });
    } catch (error) {
        res.status(500).send({ message: 'Error recording postback', error: error });
    }
});
```
## 6. API References

## `trackClick`

**Description**: Tracks when a user clicks a custom link.

**Parameters**:
- `source`: The ad source (e.g., TikTok).
- `campaign`: The campaign ID or name.

**Returns**: A success or failure callback.

---

# 7. FAQs

**Q: How do I get started with SKAdNetwork?**  
A: Follow the integration steps above to configure your server and receive postbacks from SKAdNetwork after the app install.

**Q: How do I track events inside my app?**  
A: Use the `trackEvent` function from the SDK to monitor custom events that are relevant to your campaign.

---

# 8. Support

If you need further assistance, feel free to contact us at [support@magmat.app](mailto:jiyan.aslan@magmat.app) or visit our [support page](https://www.magmat.app/).
