# How to search for products

How Claude finds things for Alejo to buy on amazon.co.uk. Claude fills the basket; Alejo places the order.

**Bottom line:** Size the search to the cost of a wrong choice, not to the price. For a commodity, one search and a known brand sold or shipped by Amazon is enough. When quality or fit changes the outcome, first learn from one expert or enthusiast source which features matter and which models are good, then use Amazon to find those models at a good price. Check every pick against the measurements and constraints in [inputs.md](inputs.md) before it goes in the basket.

## 1. Pick the effort level

Stakes = price + hassle of returning + harm if it fails. Decide the level before searching and say which one you used.

1. **Commodity** (paper, batteries, bin bags, gloves). A bad pick is cheap and obvious. One search; take a known brand or the Amazon Basics equivalent, sold or shipped by Amazon, with 1,000+ ratings at 4.4 or above. Open no product pages unless something looks off. About 2 minutes. Pick without asking.
2. **Fit or durability matters** (hex keys, extension lead, footrest, pull-up bar, bike lock, rug). Two to four phrasings of the search, 60 results each, deduplicated. Open 3–5 product pages. Check one outside source (section 2) and one other UK retailer if the category has one. About 10 minutes.
3. **Quality changes the outcome** (used daily or for years, touches the body, or costs over about £60: knife, pot, desk lamp, panniers, shoes). Start outside Amazon: find the two or three models that experts or enthusiasts agree on, and the features that separate good from bad. Then price those models on Amazon and elsewhere. About 20–30 minutes.

Safety items (anything plugged into the mains, anything holding body weight) are at least level 2 even when cheap: counterfeit and unbranded versions fail dangerously.

## 2. Sources, and when to use them

| Source | Use for | Skip for |
|---|---|---|
| [Wirecutter](https://www.nytimes.com/wirecutter/) | Level 3 household, kitchen and travel items: its "how we tested" and "what to look for" sections give the criteria. US models; check UK availability. | Commodities; items with no US equivalent (plugs, leads). |
| [RTINGS](https://www.rtings.com/) | Monitors, headphones, TVs, keyboards, mice: measured, not opinion. | Everything else; it doesn't cover it. |
| [Which?](https://www.which.co.uk/reviews) | UK appliances and electricals; free pages often name "Best Buys". | Anything behind the paywall; don't sign in. |
| Reddit ([r/BuyItForLife](https://www.reddit.com/r/BuyItForLife/), [r/bodyweightfitness](https://www.reddit.com/r/bodyweightfitness/), [r/bikecommuting](https://www.reddit.com/r/bikecommuting/), [r/chefknives](https://www.reddit.com/r/chefknives/)) | Durability and failure modes after a year of use, which Amazon reviews miss. Search `site:reddit.com <item> uk`. | Commodities. Ignore single enthusiastic posts; look for repeated names. |
| [Screwfix](https://www.screwfix.com/) / [Toolstation](https://www.toolstation.com/) | Tools, electrical, fixings. Often cheaper than Amazon for the same brand, with click-and-collect in central London. | Everything outside trade goods. |
| [Decathlon](https://www.decathlon.co.uk/) | Sports gear and clothing; its own brands are the value benchmark. | |
| [IKEA](https://www.ikea.com/gb/en/) / [Argos](https://www.argos.co.uk/) | Home goods and furniture (IKEA); same-day price check (Argos). | |
| [CamelCamelCamel](https://uk.camelcamelcamel.com/) | Level 3 items over £50, to see whether today's price is normal. | Anything cheaper. |

Skip "Customers also bought", brand storefronts and "Amazon's Choice" badges: they are mostly accessories, one brand's catalogue, or sales-driven, and add no independent judgement. When another retailer is cheaper or better, say so; Alejo can buy there instead of Amazon.

## 3. Judge quality

1. **Drop sponsored results** before ranking. Sponsored listings are the first several results of most searches (4–5 of the top 5 in a test on 30 September 2026).
2. **Weigh ratings against count.** 4.5 from 30 ratings is weaker evidence than 4.3 from 3,000. Amazon's "Avg. Customer Review" sort (`&s=review-rank`) ranks tiny-count products high, so apply your own floor (100+ ratings at level 2, several hundred at level 3).
3. **Check for merged or imported reviews.** Signs: reviews from many countries on a UK listing, reviews of a different product or colour, a title in another language, the same product under several ASINs with different counts. In the test, a doorway pull-up bar's page showed reviews from Spain, Germany, Poland, Sweden and Italy; count only UK reviews when judging fit to UK homes and plugs.
4. **Read the bad reviews.** The histogram shows the share of 1–3 star reviews; above about 15% is a warning. Read the 1–3 star reviews on the product page and classify each: product defect (counts), wrong expectation (counts only if it matches Alejo's use), delivery or seller issue (mostly ignore). A failure mode repeated three times is real.
5. **Prefer Amazon as seller or shipper.** "Ships from Amazon" means Amazon handles returns. "Sold by" a third party with its own shipping means slower, harder returns; accept it only for a known brand's own store. Note the return window.
6. **Distrust bundles of perfect five-star reviews** posted in the same weeks, very short praise, and "bought in past month" figures without a matching review count.

## 4. Check fit before recommending

1. Re-read [inputs.md](inputs.md) and the newest [log.md](log.md) entries for measurements and constraints (no drill, rented flat, 172 cm tall, small room, sixth floor).
2. Pull dimensions, weight limit, adjustment range and material from the product page's details table, not the title.
3. If a pick depends on a measurement Alejo hasn't given (door-frame width, bolt size, desk height), stop and ask for that one number rather than guessing. Say which range of the product it has to fall in.
4. Prefer the version that fits several needs (a mat that also serves calisthenics) over adding a second item, since Alejo wants to own little.

## 5. What to show Alejo

- **Level 1:** just pick and add it. Report name, price, ASIN in one line.
- **Levels 2–3:** pick when one option is clearly best for his inputs, and add it; say why in one sentence. When the choice turns on something only he knows (taste, how often he'll use it, budget), give two or three numbered options with price, rating and count, and the single trade-off that decides between them. Recommend one.
- Always state what you did not verify (for example "UK reviews only 12; fit to your door unconfirmed").

## 6. Browser mechanics

Drive Alejo's Chrome with Claude-in-Chrome in a new tab on `www.amazon.co.uk`, and run same-origin `fetch()` from the page, parsing with `DOMParser`. This was tested on 30 September 2026 and returns full search and product pages without navigating:

```js
const get = async u => new DOMParser().parseFromString(await (await fetch(u)).text(), 'text/html');
const s = await get('/s?k=' + encodeURIComponent('doorway pull up bar no screws'));
const rows = [...s.querySelectorAll('div[data-component-type="s-search-result"][data-asin]')]
  .filter(e => e.dataset.asin).map(e => ({
    asin: e.dataset.asin,
    sponsored: !!e.querySelector('.puis-sponsored-label-text'),
    title: e.querySelector('h2')?.textContent.trim(),
    price: e.querySelector('.a-price .a-offscreen')?.textContent,
    rating: e.querySelector('.a-icon-alt')?.textContent,           // "4.4 out of 5 stars"
    ratings: e.querySelector('a[href*="customerReviews"]')?.getAttribute('aria-label'), // "420 ratings"
  }));
```

Product page (`/dp/<ASIN>`) selectors: `#productTitle`, `.a-price .a-offscreen`, `#acrPopover .a-icon-alt`, `#acrCustomerReviewText`, `#merchantInfoFeature_feature_div .offer-display-feature-text-message` (sold by), `#fulfillerInfoFeature_feature_div …` (ships from), `#returnsInfoFeature_feature_div …` (returns), `#histogramTable [aria-label]` (star shares), `#productOverview_feature_div tr` and `#productDetails_techSpec_section_1 tr` (specs), `[data-hook="review"]` (about 8 top reviews, each with its country and date).

- **Batching:** run one search phrasing per call and fetch product pages 3–5 at a time, sequentially with a second or two between requests. Deduplicate by ASIN across phrasings. A level 2 item needs about 10 requests.
- **Currency:** Alejo's account shows prices in USD. He pays in GBP, and the USD figures include Amazon's conversion, so compare products in USD, report "about £X" at the day's rate, and read the exact GBP price in the basket. The `&currency=GBP` URL parameter does not change it.
- **Full review pages** (`/product-reviews/<ASIN>`, including the critical-only filter) redirect to a password prompt. Don't sign in; use the reviews on the product page, or ask Alejo to open the page himself if a level 3 decision hinges on it.
- **Bot detection:** stay under about 30 requests per item. If a response contains a CAPTCHA ("Enter the characters you see"), stop, never solve it, and tell Alejo.
- **Privacy:** the page shows his delivery postcode; never copy it, order details or payment details into this public repository.
- **Basket:** adding to the basket is allowed; never proceed to checkout. Log what you added in [log.md](log.md).
