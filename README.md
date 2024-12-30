# react-native-thermal-receipt-printer-image-qr-enhance

- Fork from https://github.com/thiendangit/react-native-thermal-receipt-printer-image-qr
- Fix and enhance for printing BLUETOOTH image url, base64 ONLY!

## Main takeaway from the original
- When assigning printer width. Need to use string '58' instead of '58mm'

## Notes
From the original code, Android seems to be fine but i have problems when using iOS device. The issue mainly stems from the handling of connected multiple bluetooth thermal printer at once.
<br /><br />
And when using iOS the printer print image very slow. (WIP)

## New Example

**`printImageBase64`**

```tsx
if (Platform.OS === 'ios') {
  // Use react-native's Image.getSize if capture screen using ref to get real image rWIDTH and rHEIGHT
  BLEPrinter.printImageBase64(base64Image, {
    imageWidth: 384, // or 576 for 80mm
    imageHeight: targetHeight, // rHEIGHT * (imageWidth / rWIDTH) to keeps the scale
    printerWidthType: '58' // or '80'
    paddingX: 0
  });
  // if not specify paddingX = 0, the image will have default padding which makes the alignment wrong. (only happens to iOS. stems from the iOS printer code)
} else {
  BLEPrinter.printImageBase64(base64Image, {
    imageWidth: 384,
    imageHeight: targetHeight
  });
}
```

<br />
<br />

# react-native-thermal-receipt-printer-image-qr

![npm](https://img.shields.io/npm/dw/react-native-thermal-receipt-printer-image-qr?logo=github)
![npm](https://img.shields.io/npm/v/react-native-thermal-receipt-printer-image-qr?color=green&logo=npm&logoColor=green)

- I forked this for my quickly project, this is not the official project.
- Fork of [`react-native-thermal-receipt-printer`](https://www.npmjs.com/package/react-native-thermal-receipt-printer) and add implement :
  <br />

| Implement                 | Android            | IOS                |
| ------------------------- | ------------------ | ------------------ |
| Image & QR (URL & Base64) | :heavy_check_mark: | :heavy_check_mark: |
| Fix cut                   | :heavy_check_mark: | :heavy_check_mark: |
| Print With Column         | :heavy_check_mark: | :heavy_check_mark: |
| NET Connect Timeout       | :heavy_check_mark: | :heavy_check_mark: |

:grey_exclamation:**`Print Image & QR with bluetooth in IOS just implement not tested yet`**

## Support

| Printer    | Android            | IOS                |
| ---------- | ------------------ | ------------------ |
| USBPrinter | :heavy_check_mark: |                    |
| BLEPrinter | :heavy_check_mark: | :heavy_check_mark: |
| NetPrinter | :heavy_check_mark: | :heavy_check_mark: |

<br />
<div style="display: flex; flex-direction: row; align-self: center; align-items: center">
<img src="image/invoice.jpg" alt="bill" width="270" height="580"/>
<img src="image/_screenshot.jpg" alt="screenshot" width="270" height="580"/>
</div>

## Installation

```
npm i react-native-thermal-receipt-printer-image-qr
npm i react-native-ping
```

or

```
yarn add react-native-thermal-receipt-printer-image-qr
yarn add react-native-ping
```

next step

```
# RN >= 0.60
cd ios && pod install

# RN < 0.60
react-native link react-native-thermal-receipt-printer-image-qr
```

## API Reference

```tsx
    init: () => Promise;
    getDeviceList: () => Promise;
    /**
     * `timeout`
     * @default 4000ms
     */
    connectPrinter: (host: string, port: number, timeout?: number | undefined) => Promise;
    closeConn: () => Promise;
    /**
     * Print text
     */
    printText: (text: string, opts?: {}) => void;
    /**
     * Print text & end the bill & cut
     */
    printBill: (text: string, opts?: PrinterOptions) => void;
    /**
     * print with image url
     */
    printImage: (imgUrl: string, opts?: PrinterImageOptions) => void;
    /**
     * Base 64 string
     */
    printImageBase64: (Base64: string, opts?: PrinterImageOptions) => void;
    /**
     * Only android print with encoder
     */
    printRaw: (text: string) => void;
    /**
     * print column
     * 80mm => 46 character
     * 58mm => 30 character
     */
    printColumnsText: (texts: string[], columnWidth: number[], columnAlignment: ColumnAlignment[], columnStyle?: string[], opts?: PrinterOptions) => void;
```

## Styling

```js
import {
  COMMANDS,
  ColumnAlignment,
} from "react-native-thermal-receipt-printer-image-qr";
```

[See more here](https://github.com/thiendangit/react-native-thermal-receipt-printer-image-qr/blob/main/dist/utils/printer-commands.js)

## Example

**`Print Columns Text`**

```tsx
const BOLD_ON = COMMANDS.TEXT_FORMAT.TXT_BOLD_ON;
const BOLD_OFF = COMMANDS.TEXT_FORMAT.TXT_BOLD_OFF;
let orderList = [
  ["1. Skirt Palas Labuh Muslimah Fashion", "x2", "500$"],
  ["2. BLOUSE ROPOL VIRAL MUSLIMAH FASHION", "x4222", "500$"],
  [
    "3. Women Crew Neck Button Down Ruffle Collar Loose Blouse",
    "x1",
    "30000000000000$",
  ],
  ["4. Retro Buttons Up Full Sleeve Loose", "x10", "200$"],
  ["5. Retro Buttons Up", "x10", "200$"],
];
let columnAlignment = [
  ColumnAlignment.LEFT,
  ColumnAlignment.CENTER,
  ColumnAlignment.RIGHT,
];
let columnWidth = [46 - (7 + 12), 7, 12];
const header = ["Product list", "Qty", "Price"];
Printer.printColumnsText(header, columnWidth, columnAlignment, [
  `${BOLD_ON}`,
  "",
  "",
]);
for (let i in orderList) {
  Printer.printColumnsText(orderList[i], columnWidth, columnAlignment, [
    `${BOLD_OFF}`,
    "",
    "",
  ]);
}
Printer.printBill(`${CENTER}Thank you\n`);
```

**`Print image`**

```tsx
Printer.printImage(
  "https://media-cdn.tripadvisor.com/media/photo-m/1280/1b/3a/bd/b5/the-food-bill.jpg",
  {
    imageWidth: 575,
    // imageHeight: 1000,
    // paddingX: 100
  }
);
```

[See more here](https://github.com/thiendangit/react-native-thermal-receipt-printer-image-qr/blob/main/example/src/HomeScreen.tsx)

## Troubleshoot

- When installing `react-native` version >= 0.60, XCode shows this error:

```
duplicate symbols for architecture x86_64
```

That's because the .a library uses [CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket) library and Flipper uses it too.

_Podfile_

```diff
...
  use_native_modules!

  # Enables Flipper.
  #
  # Note that if you have use_frameworks! enabled, Flipper will not work and
  # you should disable these next few lines.
  # add_flipper_pods!
  # post_install do |installer|
  #   flipper_post_install(installer)
  # end
...
```

and comment out code related to Flipper in `ios/AppDelegate.m`
