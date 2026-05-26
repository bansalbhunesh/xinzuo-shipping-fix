## What I picked

The cart drawer shows conflicting shipping info. The progress bar at the top says "Free Delivery Unlocked!" once you hit $100, but the delivery charges line below it still says "Calculated at checkout" — at the same dollar amount, in the same drawer.

The root cause is a comparison operator mismatch. The progress bar checks `< shipping_value` (correct — it flips at $100). The delivery charges check `> shipping_value` (wrong — strict greater-than excludes exactly $100). Two-character fix: `>` to `>=`, in two files.

## Why it's the highest-impact thing here

$100 is the round number customers aim for. It's the whole point of showing a progress bar. If someone adds items until the bar says "unlocked" and then sees "Calculated at checkout" right below it, the store just lied to them. That's a trust problem, and on an e-commerce site selling $200+ knives, trust is revenue.

I also couldn't find this by browsing xinzuo.com.au — I had to read the Liquid and spot the operator mismatch between line 488 and line 640. That's what convinced me it's a real undiscovered bug, not something cosmetic that's already been noticed.

## What I did

Fixed `>` to `>=` in four comparisons across two files:

- `snippets/cart-drawer.liquid` lines 640, 642 — the slide-out cart drawer
- `snippets/cart-summary.liquid` lines 124, 126 — the /cart page summary

Also caught a typo while I was in cart-summary.liquid: "Calcuated" → "Calculated" on line 129.

The progress bar logic in `main-cart.liquid` was already correct (uses `<`), which is actually what made the inconsistency obvious — same feature, different operators.

## What I'd do next

Write a Theme Check test that asserts all threshold comparisons across cart surfaces use consistent operators. Also check if the bundle builder discount tiers have the same pattern.
