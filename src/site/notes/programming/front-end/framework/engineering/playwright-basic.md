---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-06-03-Fri, 2:58:49 pm","date-modified":"2023-04-29-Sat, 8:10:37 pm","permalink":"/programming/front-end/framework/engineering/playwright-basic/","dgPassFrontmatter":true}
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
