# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: selfservice-ui-rendering-simple.spec.js >> Self Service - Performance (simple start/stop) >> User 21: Performance Test
- Location: tests/selfservice-ui-rendering-simple.spec.js:102:5

# Error details

```
TimeoutError: locator.click: Timeout 10000ms exceeded.
Call log:
  - waiting for locator('iframe[name="ch-nc_self_service"]').contentFrame().getByRole('link', { name: 'Store', exact: true })

```

# Test source

```ts
  126 | 
  127 |       // —— Dismiss tour (Global Overview) ——
  128 |       await test.step(`${userLabel} Dismiss tour`, async () => {
  129 |         perf.start('Global Overview');
  130 | 
  131 |         const skipTour = page.getByRole('button', { name: /skip tour/i });
  132 | 
  133 |         try {
  134 |           await Promise.race([
  135 |             skipTour.waitFor({ state: 'visible', timeout: TIMEOUTS.WAIT }),
  136 |             page.waitForTimeout(1500)
  137 |           ]).catch(() => null);
  138 | 
  139 |           const canClick = await skipTour.click({ trial: true })
  140 |             .then(() => true)
  141 |             .catch(() => false);
  142 | 
  143 |           if (canClick) {
  144 |             await skipTour.click({ timeout: TIMEOUTS.ACTION });
  145 |             await page.waitForTimeout(800);
  146 |           }
  147 | 
  148 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  149 |         } finally {
  150 |           await perf.stop();
  151 |         }
  152 |       });
  153 | 
  154 |       // —— Navigate to Self Service ——
  155 |       await test.step(`${userLabel} Navigate to Self Service`, async () => {
  156 |         perf.start('Self Service Navigation');
  157 |         try {
  158 |           await page.getByLabel('App - Global Overview selected')
  159 |             .getByText('Global Overview')
  160 |             .click({ timeout: TIMEOUTS.ACTION });
  161 | 
  162 |           await page.getByLabel('Cloud Manager - Self Service')
  163 |             .click({ timeout: TIMEOUTS.ACTION });
  164 | 
  165 |           const selfServiceIframe = page.locator('iframe[name="ch-nc_self_service"]');
  166 |           await expect(selfServiceIframe).toBeVisible({ timeout: TIMEOUTS.NAVIGATION });
  167 | 
  168 |           const selfServiceFrame = selfServiceIframe.contentFrame();
  169 |           const welcomeText = selfServiceFrame.getByText('Welcome to Self Service');
  170 |           const deployText = selfServiceFrame.getByText('Deploy and manage');
  171 | 
  172 |           await Promise.race([
  173 |             welcomeText.waitFor({ state: 'visible', timeout: TIMEOUTS.NAVIGATION }),
  174 |             deployText.waitFor({ state: 'visible', timeout: TIMEOUTS.NAVIGATION }),
  175 |           ]).catch(() => {});
  176 | 
  177 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  178 |           await perf.stop();
  179 |         } catch (error) {
  180 |           await perf.captureFailure('Self-Service-Navigation');
  181 |           throw error;
  182 |         }
  183 |       });
  184 | 
  185 |       // —— Dismiss Welcome Modal (Self Service Home) ——
  186 |       await test.step(`${userLabel} Dismiss Welcome Modal`, async () => {
  187 |         perf.start('Self Service Home', { frameName: SELF_SERVICE_FRAME });
  188 | 
  189 |         try {
  190 |           const iframeLocator = page.frameLocator('iframe[name="ch-nc_self_service"]');
  191 | 
  192 |           await iframeLocator.locator('body')
  193 |             .waitFor({ state: 'visible', timeout: TIMEOUTS.NAVIGATION })
  194 |             .catch(() => {});
  195 | 
  196 |           const closeButton = iframeLocator.getByRole('button', { name: '×' });
  197 | 
  198 |           await Promise.race([
  199 |             closeButton.waitFor({ state: 'visible', timeout: TIMEOUTS.WAIT }),
  200 |             page.waitForTimeout(1500)
  201 |           ]).catch(() => null);
  202 | 
  203 |           const canClick = await closeButton.click({ trial: true })
  204 |             .then(() => true)
  205 |             .catch(() => false);
  206 | 
  207 |           if (canClick) {
  208 |             await closeButton.click({ timeout: TIMEOUTS.ACTION });
  209 |             await page.waitForTimeout(500);
  210 |           }
  211 | 
  212 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  213 |           await perf.stop();
  214 | 
  215 |         } catch (error) {
  216 |           await perf.captureFailure('Self-Service-Home');
  217 |           throw error;
  218 |         }
  219 |       });
  220 | 
  221 |       // —— Store ——
  222 |       await test.step(`${userLabel} Store Page`, async () => {
  223 |         perf.start('Store', { frameName: SELF_SERVICE_FRAME });
  224 |         try {
  225 |           const selfServiceFrame = page.locator('iframe[name="ch-nc_self_service"]').contentFrame();
> 226 |           await selfServiceFrame.getByRole('link', { name: 'Store', exact: true }).click({ timeout: TIMEOUTS.ACTION });
      |                                                                                    ^ TimeoutError: locator.click: Timeout 10000ms exceeded.
  227 |           await page.waitForTimeout(2000);
  228 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  229 |           await perf.stop();
  230 |         } catch (error) {
  231 |           await perf.captureFailure('Store-Page');
  232 |           throw error;
  233 |         }
  234 |       });
  235 | 
  236 |       // —— Store List ——
  237 |       await test.step(`${userLabel} Store List View`, async () => {
  238 |         perf.start('Store List', { frameName: SELF_SERVICE_FRAME });
  239 |         try {
  240 |           const selfServiceFrame = page.locator('iframe[name="ch-nc_self_service"]').contentFrame();
  241 |           await selfServiceFrame.getByRole('tablist').getByRole('tab').nth(1).click({ timeout: TIMEOUTS.ACTION });
  242 |           await page.waitForTimeout(2000);
  243 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  244 |           await perf.stop();
  245 |         } catch (error) {
  246 |           await perf.captureFailure('Store-List-View');
  247 |           throw error;
  248 |         }
  249 |       });
  250 | 
  251 |       // —— Blueprints ——
  252 |       await test.step(`${userLabel} Blueprints Page`, async () => {
  253 |         perf.start('Blueprints', { frameName: SELF_SERVICE_FRAME });
  254 |         try {
  255 |           const selfServiceFrame = page.locator('iframe[name="ch-nc_self_service"]').contentFrame();
  256 |           await selfServiceFrame.getByRole('link', { name: 'Blueprints' }).click({ timeout: TIMEOUTS.ACTION });
  257 |           await page.waitForTimeout(1000);
  258 |           await selfServiceFrame.getByRole('heading', { name: 'Blueprints', exact: true }).waitFor({ state: 'visible', timeout: TIMEOUTS.NAVIGATION });
  259 |           await page.waitForLoadState('networkidle', { timeout: TIMEOUTS.NAVIGATION }).catch(() => {});
  260 |           await page.waitForTimeout(1500);
  261 |           await perf.stop();
  262 |         } catch (error) {
  263 |           await perf.captureFailure('Blueprints-Page');
  264 |           throw error;
  265 |         }
  266 |       });
  267 | 
  268 |       await perf.finishUser();
  269 |     });
  270 |   }
  271 | });
  272 | 
  273 | test.afterAll(async ({}, testInfo) => {
  274 |   PerformanceTracker.generateReport(testInfo);
  275 | });
  276 | 
```