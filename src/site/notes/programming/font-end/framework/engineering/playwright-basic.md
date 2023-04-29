---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/playwright-basic/"}
---


官网写的很牛了, 没必要

# Quick Start

```
npm i -D playwright
```

```ts
import { firefox } from 'playwright';

export async function fetchTwitterDetail() {
    const browser = await firefox.launch({ headless: false, slowMo: 50 });
    // Create pages, interact with UI elements, assert values
    const page = await browser.newPage();
    await page.goto('https://twitter.com/home');
    await page.screenshot({ path: 'example.png' });
    await browser.close();
}
```
