###  [<< back](./index.md)

# install playwright in vs code
- Precondition: nodejs installed, mpm installed, with elevated permission
- Install extention: View->Command palette->Install playwright (browsers and git workflow yml file)

# configuration in vs code
- .\playwright.config.ts  (can configure timeout and headless mode etc.)

# write a test in vs code
## basic code 

```typescript
import { test, expect } from '@playwright/test';

test('google test', async ({ page }) => {

  await page.goto('https://www.google.com.hk/');
  await page.getByLabel('Search', { exact: true }).click();
  await page.getByLabel('Search', { exact: true }).fill('playwright testing');
  
  await page.getByLabel('Google Search', { exact: true }).first().click();
  await page.getByRole('link', { name: 'Playwright: Fast and reliable' }).click();
  await page.close();
});
```

## run a test
- npx playwright test

## generate code
- npx playwright codegen

## report after test  [link](https://www.lambdatest.com/learning-hub/playwright-reporting)
- change report format in .\playwright.config.ts, the report can be json, html etc.
- change video or screenshot in .\playwright.config.ts  (can be configed to be only on success or failure and so on)
- npx playwright show report

## Handle with special cases
- handle alert
- handle drop down
- handle multiple elements ( nth(index) )

## page object module
- using class, method to put all the elements and actions in one file in order to avoid duplication of code and it is easy to read and maintain