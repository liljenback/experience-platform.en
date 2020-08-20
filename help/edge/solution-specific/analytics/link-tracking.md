---
title: Page and Link Tracking with Adobe Analytics
seo-title: Link tracking to Adobe Analytics with Adobe Experience Platform Web SDK
description: Learn how to send Link Data to Adobe Analytics with Experience Platform Web SDK
seo-description: Learn how to send Link Data to Adobe Analytics with Experience Platform Web SDK
---

# Sending Data to Adobe Analytics

Whereas in the past there were different functions to distinquish between a page view and a link (for example, `s.t(), s.tl()`), in the Web SDK there is just the `sendEvent` command. The data you send with an event determines whether it should be a page view or a link.

## Sending a page view

You can specify a page view by setting the the `web.webPageDetails.pageViews.value=1` variable.

```javascript
alloy("sendEvent", {
  "xdm": {
    "web": {
      "webPageDetailsr": {
        "pageViews": {
            "value":1
      }
    }
  }
});
```

Although analytics technically records a page view even if this variable is not set, it is a best practice to set this variable whenever you want to record a page view to be explicit in your data and to futureproof you implementation. 

## Tracking Links

Links can be set manually or tracked [automatically](#automaticLinkTracking). Manual tracking is done by adding the details under the `web.webInteraction` part of the schema. There are three required variables: `web.webInteraction.name`, `web.webInteraction.type` and `web.webInteraction.linkClicks.value`.

```javascript
alloy("sendEvent", {
  "xdm": {
    "web": {
      "webInteraction": {
        "linkClicks": {
            "value":1
      },
      "name":"My Custom Link", //Name that shows up in the custom links report
      "URL":"https://myurl.com", //the URL of the link
      "type":"other", // values: other, download, exit
    }
  }
});
```

The link type can be one of three values: 

* **`other`:** A custom link
* **`download`:** A download link
* **`exit`:** An exit link

### Automatic Link Tracking {#automaticLinkTracking}

By default the Web SDK will capture, [label]((#labelingLinks)), and [record](https://github.com/adobe/xdm/blob/master/docs/reference/context/webinteraction.schema.md) clicks on [qualifying](#qualifyingLinks) link tags. Clicks are captured with a [capture](https://www.w3.org/TR/uievents/#capture-phase) click event listener that is attached to the document.

Disabling automatic link tracking can be done by [configuring](../../fundamentals/configuring-the-sdk.md#clickCollectionEnabled) the Web SDK.

```javascript
clickCollectionEnabled: false
```

#### What tags qualify for link-tracking?{#qualifyingLinks}

Automatic link tracking is done for anchor `A` and `AREA` tags. However, these tags are not considered for link tracking if they have an attached `onclick` handler.

#### How are links labled?{#labelingLinks}

Links are labled as a download link if the anchor tag includes a download attribute or if the link ends with a popular file extension. The download link qualifier can be [configured](../../fundamentals/configuring-the-sdk.md) with a regular expression:

```javascript
downloadLinkQualifier: "\\.(exe|zip|wav|mp3|mov|mpg|avi|wmv|pdf|doc|docx|xls|xlsx|ppt|pptx)$"
```
Links are labled as an exit links if the link target domain differ from the current `window.location.hostname`.

Links that does not qualify as a download or exit link are labled as other.
