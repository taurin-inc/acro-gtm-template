# Acro Pixel — Google Tag Manager template

A [Google Tag Manager](https://tagmanager.google.com) custom tag template for the
[Acro](https://acro.sh) pixel. It loads `acro.js` for your brand's write key and sends
page views, standard events (Purchase, AddToCart, …), custom events and identify calls, so
Acro can attribute your conversions to the ad creatives that drove them.

## Install

The template is awaiting review for the GTM Community Template Gallery. Until it is listed
there, import the template file:

1. Download [`template.tpl`](https://raw.githubusercontent.com/taurin-inc/acro-gtm-template/main/template.tpl)
   (right-click → Save link as…).
2. In your GTM container: **Templates** → **Tag Templates** → **New** → the ⋮ menu → **Import**,
   pick `template.tpl`, then **Save**.

## Set up the tags

Your write key (`acro_pk_…`) is in Acro's analytics settings. Every tag takes it.

| Tag                           | Tag type                   | Trigger                                           |
| ----------------------------- | -------------------------- | ------------------------------------------------- |
| Acro – Base code              | Base code (page views)     | All Pages                                         |
| Acro – Purchase               | Standard event → Purchase  | Your purchase event, e.g. Custom Event `purchase` |
| Acro – Add to cart (optional) | Standard event → AddToCart | e.g. Custom Event `add_to_cart`                   |

The base code tracks every page view itself, including single-page app navigations
(`pushState`), so don't add a History Change trigger for it. Each other tag also loads
`acro.js` if the base code hasn't yet, and calls made before it loads are queued.

### Event data

- **Read from the GA4 ecommerce object**: if your site already pushes GA4 ecommerce events
  (`{ event: "purchase", ecommerce: { transaction_id, value, currency, items } }`), tick this
  box and the tag takes the value, currency, order ID and items from it.
- **Value, Currency, Order ID, Items, Email, Phone**: set any of them with a variable, e.g. a
  Data Layer Variable `{{DLV - value}}`; a field set here wins over the ecommerce object.
  Items is a variable returning an array of `{ id, name, category, price, quantity }` (GA4's
  `item_id`, `item_name`, `item_category` work too). The order ID deduplicates a purchase you
  also send from your server.
- **Properties**: any other name/value pairs to send with the event.

Standard events: Purchase, AddToCart, InitiateCheckout, AddPaymentInfo, ViewContent, Lead,
CompleteRegistration, Search, Subscribe. A custom event takes any name.

### Users

- **Identify user**: fire it once the user signs in, with your user ID (e.g.
  `{{DLV - user_id}}`) and optional traits. Later events carry the user ID.
- **Reset user (logout)**: fire it on logout, so the next person on the same browser starts
  fresh.

## Permissions

| Permission       | Why                                                  |
| ---------------- | ---------------------------------------------------- |
| Injects scripts  | `https://t.acro.sh/s/*` — acro.js for your write key |
| Accesses globals | `window.acro`, the pixel and its call queue          |
| Reads data layer | `ecommerce`, only when the GA4 option is ticked      |
| Logs to console  | Only in GTM preview (debug) mode                     |

## Development

`template.tpl` is the template as GTM exports it; its tests (the **Tests** tab of the template
editor) run there. After changing it in GTM, export it over `template.tpl`, commit, and add the
commit's SHA to the top of `versions` in `metadata.yaml` in a second commit.

## License

[Apache 2.0](LICENSE)
