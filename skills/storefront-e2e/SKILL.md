---
name: storefront-e2e
description: Run this project's storefront checkout flow with the project-installed Chrome DevTools MCP, including the ECPay staging ATM path, and provide screenshot evidence.
---

# Storefront E2E testing

Use this skill to manually drive this project's customer purchase flow with the project-scoped Chrome DevTools MCP. This is a local/staging test workflow, not authorization to place real orders or transfer money.

## Environment

- Start in the repository root. The MCP dependency and project configuration are `chrome-devtools-mcp` and `.vscode/mcp.json`. The server config launches Chrome headed and grants screenshot file access to the workspace. Use Chrome DevTools MCP browser tools for all browser interaction; do not substitute Playwright or direct API/database changes.
- Use `http://localhost:3001` so the ECPay return URL shares the storefront's cookie origin. Check port 3001 first; if unavailable, start `npm run dev:server` and wait until ready.
- Development seed login defaults to `admin@hexschool.com` / `12345678`. Use `E2E_ADMIN_EMAIL` and `E2E_ADMIN_PASSWORD` if the project provides them. Never include secrets in screenshots or handoff.
- Use clearly fake recipient/contact information. Confirm that ECPay is the staging host before proceeding with any payment step.

## Purchase and payment

1. Sign in through the storefront UI, select a product, add it to the cart, and verify the cart item and quantity.
2. Continue through checkout, fill test-only recipient details, submit, and verify the browser reaches ECPay staging.
3. In ECPay select **網路ATM**, choose **台灣土地銀行** (the request may spell it **臺灣土地銀行**), and continue.
4. Complete only the provider's staging/mock WebATM flow. The mock form may expose a prefilled successful test result; submit it through the UI. Never enter real bank credentials, perform a real transfer, or fake the provider callback by editing local app data.
5. Follow **返回商店** and verify the order detail on `localhost:3001` reports **已付款** and shows the expected item/amount. An intermediate provider message alone is not sufficient evidence.

Before retrying, inspect the existing test order and its status. Avoid creating duplicate orders when an existing order can confirm the desired checkpoint. If the provider is unavailable or the shop does not confirm payment, stop at that checkpoint, report the order status accurately, and do not keep retrying.

## Screenshots and handoff

Every run must finish with screenshot evidence, including failed runs. Use Chrome DevTools MCP's `take_screenshot` tool to save the final storefront order state to `test-results/` (for example, `test-results/storefront-e2e-order.png`). Capture an additional ECPay checkpoint when it helps establish which payment route was selected or where a failure occurred. Keep screenshots free of secrets and real personal data.

Report the result, the verified order number/status and amount, and whether the payment was staging/mock. Include a clickable link to the screenshot artifact in the final response; for failures, link the last meaningful checkpoint and name what remains unverified. `test-results/` is git-ignored, so attach/link the local artifact rather than expecting it in a commit.

## MCP startup note

For a standalone local MCP session, launch from the project root with `npm exec -- chrome-devtools-mcp --no-headless --workspace "$PWD"`. The MCP process itself is the server; `--no-headless` belongs on that process, not on individual browser commands. Keep the server running while using its tools. If a visible browser was requested, verify the headed Chrome window actually opens before driving the checkout.
