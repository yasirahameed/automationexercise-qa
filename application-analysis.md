# Automation Exercise —Application Analysis

**Application:** https://automationexercise.com/  
**Document:** `manual-testing/application-analysis.md`  
**Purpose:** Manual application analysis before API/UI automation  
**QA level:** Senior QA / SDET (6+ years expected depth)  
**Analysis date:** 2026-08-31

---

## 1. Objective

The goal of this analysis is to understand the Automation Exercise application as a complete e-commerce system before designing automated tests.

This document intentionally goes beyond listing pages. It identifies:

- business capabilities and modules;
- critical end-to-end user journeys;
- positive, negative, boundary, and state-transition scenarios;
- field and business-rule validation targets;
- dependencies between UI modules and application state;
- high-risk and regression-sensitive areas;
- test data requirements;
- integration/API opportunities;
- exploratory testing charters;
- non-functional considerations;
- assumptions and unanswered product questions;
- automation candidates and areas that should remain primarily exploratory/manual.

A senior QA should use this analysis as input to later test design, API coverage, UI automation architecture, regression planning, and defect risk assessment.

---

## 2. Application Summary

Automation Exercise is a practice e-commerce application intended for UI and API testing.

The application currently exposes these major user-facing capabilities:

1. Home
2. Signup / Login
3. Account registration
4. Logout
5. Account deletion
6. Products catalog
7. Product details
8. Product search
9. Category filtering
10. Brand filtering
11. Recommended products
12. Cart
13. Checkout
14. Address verification
15. Payment
16. Order confirmation
17. Invoice download
18. Contact Us
19. Subscription
20. Product review
21. Public practice APIs
22. Public test-case documentation

The product catalog exposes:

- Women
  - Dress
  - Tops
  - Saree
- Men
  - Tshirts
  - Jeans
- Kids
  - Dress
  - Tops & Shirts

Observed brands include:

- Polo
- H&M
- Madame
- Mast & Harbour
- Babyhug
- Allen Solly Junior
- Kookie Kids
- Biba

The catalog contains product metadata such as:

- product name;
- price;
- category;
- availability;
- condition;
- brand;
- quantity.

---

## 3. System Context

At a conceptual level, the system can be viewed as the following domains:

```text
                           +------------------+
                           |      Visitor     |
                           +---------+--------+
                                     |
          +--------------------------+---------------------------+
          |                          |                           |
          v                          v                           v
 +----------------+        +------------------+         +------------------+
 | Account/Auth   |        | Product Catalog  |         | Site Services    |
 +-------+--------+        +--------+---------+         +--------+---------+
         |                          |                            |
         |                          v                            |
         |                  +---------------+                   |
         +----------------->|     Cart      |<------------------+
                            +-------+-------+
                                    |
                                    v
                            +---------------+
                            |   Checkout    |
                            +-------+-------+
                                    |
                                    v
                            +---------------+
                            |    Payment    |
                            +-------+-------+
                                    |
                                    v
                            +---------------+
                            | Order/Invoice |
                            +---------------+
```

The important QA point is that these are not independent screens. Authentication, cart state, address data, product state, and checkout interact.

---

# 4. Application Map

```text
User / Account
 ├── Register
 │    ├── Basic signup: name + email
 │    ├── Account information
 │    ├── Personal information
 │    ├── Address information
 │    ├── Newsletter preference
 │    └── Partner-offer preference
 ├── Login
 ├── Logout
 ├── Logged-in identity display
 └── Delete Account

Product
 ├── Browse all products
 ├── Search
 ├── Filter by Category
 │    ├── Women
 │    ├── Men
 │    └── Kids
 ├── Filter by Brand
 ├── Product Details
 │    ├── Name
 │    ├── Category
 │    ├── Price
 │    ├── Availability
 │    ├── Condition
 │    ├── Brand
 │    └── Quantity
 ├── Add Review
 └── Recommended Items

Cart
 ├── Add product
 ├── Add multiple products
 ├── Add product from recommendation
 ├── Preserve product quantity
 ├── Preserve cart across login
 ├── Calculate price/quantity/total
 ├── Remove product
 └── Proceed to Checkout

Checkout
 ├── Authentication gate
 ├── Delivery address
 ├── Billing address
 ├── Review order
 ├── Order comment
 └── Place Order

Payment
 ├── Name on Card
 ├── Card Number
 ├── CVC
 ├── Expiration Month/Year
 └── Pay and Confirm

Order
 ├── Success confirmation
 ├── Continue
 └── Download Invoice

Contact
 ├── Name
 ├── Email
 ├── Subject
 ├── Message
 └── File upload

Subscription
 ├── Home footer
 ├── Cart footer
 └── Email subscription

Supporting/Test Interfaces
 ├── Public Test Cases
 └── Public API endpoints
```

---

# 5. Module-Level Analysis

## 5.1 Home

### Business purpose

The Home page acts as the main navigation and product discovery entry point.

### Testable features

- page loads successfully;
- branding/title/content appears;
- global navigation;
- product listing;
- category sidebar;
- brand sidebar;
- product cards;
- product price display;
- View Product;
- Add to Cart;
- recommended items;
- subscription;
- scroll-to-top behavior;
- navigation to Test Cases;
- navigation to API Testing;
- navigation to Contact Us.

### Positive scenarios

- Home loads and expected navigation is visible.
- Product cards display valid names and prices.
- View Product opens correct product.
- Add to Cart adds the selected product.
- Continue Shopping closes cart modal and keeps user on the expected page.
- View Cart opens cart containing selected item.
- Category navigation displays matching products.
- Brand navigation displays matching products.
- Recommended item can be added to cart.
- Subscription accepts a valid email.

### Negative scenarios

- Broken product image.
- Broken navigation link.
- Add-to-cart triggered repeatedly by accidental double-click.
- Product displayed without price/name.
- Category or brand returns products outside selected filter.
- Recommendation references an unavailable/invalid product.
- Invalid subscription email.

### Boundary / edge scenarios

- first and last visible product;
- long product names;
- lowest/highest product price;
- no recommendations;
- slow-loading images;
- repeated scroll-top usage;
- page refresh after cart addition.

### Risks

**Medium-High** because the Home page is a major entry point and controls navigation to several revenue-critical flows.

---

## 5.2 Signup / Registration

### Business purpose

Create a persistent customer account and collect identity/address information required later by checkout.

### Observed registration data

Initial signup:

- Name
- Email

Account information:

- Title
- Name
- Email
- Password
- Date of Birth

Preferences:

- Sign up for newsletter
- Receive special offers from partners

Address/contact information:

- First name
- Last name
- Company
- Address
- Address2
- Country
- State
- City
- Zipcode
- Mobile Number

### Critical business rules to validate

Some rules below are confirmed by the application documentation, while others must be discovered through testing.

**Confirmed / explicitly represented:**

- registration starts with name and email;
- an already registered email must be rejected;
- successful registration creates an account;
- newly registered user becomes logged in;
- stored registration address is later reused in checkout.

**Rules to discover/verify:**

- mandatory vs optional fields;
- email syntax rules;
- email normalization/case sensitivity;
- leading/trailing whitespace behavior;
- name character restrictions;
- password minimum/maximum length;
- password complexity;
- date-of-birth range;
- future date rejection;
- age restrictions, if any;
- supported countries;
- zip/postal code format;
- mobile number format;
- duplicate account behavior under concurrent submissions;
- whether newsletter preferences are persisted.

### Positive scenarios

- register with all valid fields;
- register with only mandatory fields;
- register with optional company/address2 empty;
- register with newsletter selected;
- register without newsletter;
- register with partner offers selected/unselected;
- successful account creation logs user in;
- correct username shown after registration;
- account data appears correctly during checkout.

### Negative scenarios

- blank name;
- blank email;
- malformed email;
- already registered email;
- blank password;
- whitespace-only values;
- invalid date;
- overly long strings;
- special characters in unsupported fields;
- script/HTML input;
- invalid phone;
- invalid postal/zip code;
- repeated click on Create Account;
- refresh/back during multi-step registration.

### Boundary scenarios

Test around discovered field limits:

```text
0
1
min - 1
min
min + 1
max - 1
max
max + 1
```

Additional boundaries:

- earliest/latest supported DOB;
- one-character names;
- very long email local/domain portions;
- phone numbers around accepted digit length;
- address length;
- empty optional fields;
- maximum accepted whitespace.

### High-risk areas

- duplicate-account prevention;
- registration data persistence;
- checkout address mapping;
- input validation consistency between UI and API;
- personally identifiable data handling;
- account creation race conditions.

### Senior-QA observation

Do not only verify the `ACCOUNT CREATED!` message. A false-positive UI success with missing/incorrect persisted user data is a more serious defect than a cosmetic confirmation issue. Validate the created account by logging out/in and by verifying checkout address data.

---

## 5.3 Login

### Business purpose

Authenticate an existing user and unlock account-dependent checkout behavior.

### Inputs

- Email Address
- Password

### Positive scenarios

- valid email/password;
- logout then login again;
- login after items have been added to anonymous cart;
- cart remains available after login;
- correct logged-in username shown;
- direct navigation after successful login behaves correctly.

### Negative scenarios

- invalid email + valid-style password;
- valid registered email + wrong password;
- both invalid;
- blank email;
- blank password;
- both blank;
- malformed email;
- whitespace around credentials;
- different email casing;
- password casing differences;
- copied password with trailing space.

### Security-oriented scenarios

- password masked;
- credentials not exposed in URL;
- login submission via HTTPS;
- session cannot be reused after logout if server invalidates it;
- authenticated pages do not remain accessible incorrectly through stale state;
- rate-limiting/lockout behavior should be observed, although a practice site may intentionally omit it;
- error should not reveal whether only email or only password was correct.

### Risk

**Critical / High** because authentication controls checkout identity and account lifecycle.

---

## 5.4 Logout

### Testable behavior

- Logout is available only to authenticated user.
- Logout returns user to login page.
- Logged-in identity disappears.
- Account-specific operations are inaccessible.
- Anonymous cart behavior is validated after logout.
- Browser Back does not incorrectly restore an authenticated functional session.

### Risks

- stale session;
- cached personalized state;
- cart/account ownership confusion.

---

## 5.5 Delete Account

### Business purpose

Remove customer account.

### Positive scenarios

- authenticated user can delete own account;
- confirmation message shown;
- deleted credentials can no longer log in;
- re-registration with same email behavior is determined;
- deleted user no longer has an active authenticated session.

### Negative scenarios

- account deletion without authentication;
- repeated Delete Account request;
- deletion using stale session;
- direct endpoint access with another user's identity, if technically testable and authorized.

### High risk

**Critical** because deletion is destructive.

A senior QA should specifically validate idempotency or safe handling of repeated delete requests. A user should not get inconsistent application state simply because the browser repeated a request.

---

# 6. Product Catalog

## 6.1 All Products

### Testable product-card information

- image;
- product name;
- price;
- View Product;
- Add to Cart.

### Scenarios

- all products load;
- no duplicate product caused by rendering defect;
- correct product details open from card;
- price on catalog equals price on product detail;
- product added from catalog equals product later shown in cart;
- repeated add behavior is consistent;
- inventory metadata is sensible.

### Data integrity checks

Cross-check the same product across:

```text
Products Page
    ↓
Product Details
    ↓
Cart
    ↓
Checkout Review
    ↓
Invoice
```

At minimum, compare:

- product identity;
- unit price;
- quantity;
- calculated total.

This is a major cross-layer regression area.

---

# 7. Product Details

### Observed fields

- Product Name
- Category
- Price
- Quantity
- Availability
- Condition
- Brand
- Review form
- Add to Cart

### Positive scenarios

- correct product opens;
- metadata is complete;
- quantity defaults to valid value;
- quantity can be changed;
- selected quantity transfers to cart exactly;
- Add to Cart works;
- review form can be submitted successfully.

### Negative / boundary scenarios for quantity

Test:

- blank;
- 0;
- 1;
- 2;
- large valid number;
- negative number;
- decimal;
- alphabetic;
- special characters;
- whitespace;
- extremely large integer;
- leading zeros;
- paste invalid value;
- browser spinner controls if present.

### Product consistency validations

For one selected product:

```text
Catalog Name        = Detail Name
Catalog Price       = Detail Price
Detail Quantity     = Cart Quantity
Detail Unit Price   = Cart Unit Price
Cart Line Total     = Unit Price × Quantity
Checkout Line Total = Cart Line Total
Invoice Item        = Purchased Item
```

### Risk

**High** because product identity and pricing errors flow directly into checkout.

---

# 8. Product Search

### Business purpose

Allow customers to find products using text input.

### Positive scenarios

- exact product name;
- partial product name;
- generic keyword such as `top`;
- generic keyword such as `tshirt`;
- generic keyword such as `jean`;
- case variation;
- matching results are relevant.

### Negative scenarios

- no-match query;
- blank query;
- whitespace only;
- special characters;
- SQL-like characters;
- HTML/script text;
- extremely long query;
- Unicode;
- leading/trailing spaces.

### Search behavior questions

Determine:

- case-sensitive or insensitive?
- substring or token search?
- prefix-only?
- trim whitespace?
- search by brand?
- search by category?
- search by product name only?
- stable ordering?
- empty-search expected result?
- special-character escaping?

### API dependency

The public API exposes:

```text
POST /api/searchProduct
parameter: search_product
```

It also documents a 400-type application response when the required parameter is missing.

### Senior-QA coverage

Compare UI search and API search for equivalent keywords. Differences between UI and backend matching logic are excellent defect candidates and interview discussion material.

---

# 9. Category Filtering

### Observed categories

```text
Women
 ├── Dress
 ├── Tops
 └── Saree

Men
 ├── Tshirts
 └── Jeans

Kids
 ├── Dress
 └── Tops & Shirts
```

### Positive scenarios

- each parent category expands correctly;
- each child category can be selected;
- category heading reflects selection;
- only matching products displayed;
- move directly from one category to another;
- add filtered product to cart.

### Negative / edge scenarios

- category with zero products;
- product incorrectly tagged to category;
- duplicate product;
- stale products remain from previous selection;
- browser Back/Forward;
- refresh filtered URL;
- category selection after search.

### Risk

**Medium**, but category/product mapping defects can impact discoverability and cart conversion.

---

# 10. Brand Filtering

### Observed brands

- Polo
- H&M
- Madame
- Mast & Harbour
- Babyhug
- Allen Solly Junior
- Kookie Kids
- Biba

### Positive scenarios

- brand list visible;
- each brand opens corresponding products;
- switch from one brand directly to another;
- product brand on product detail matches selected brand;
- brand count, if displayed, is reasonably consistent with results.

### Negative / data-quality scenarios

- brand result contains different brand;
- stale results after switching;
- empty brand result;
- incorrect product count;
- URL tampering with invalid brand identifier;
- product references deleted/nonexistent brand.

---

# 11. Cart

## 11.1 Business purpose

Maintain selected products and quantities before checkout.

### Core cart state

For each line item:

- product;
- unit price;
- quantity;
- total;
- remove control.

### Positive scenarios

- add one product;
- add multiple different products;
- add product from Home;
- add product from Products;
- add from Product Details;
- add from Recommended Items;
- add same product multiple times;
- quantity chosen on Product Details persists;
- correct totals;
- remove one item;
- remove all items;
- continue shopping;
- cart persists while navigating;
- cart persists through login, as expected by documented scenario.

### Negative scenarios

- invalid quantity reaches cart;
- item price changes unexpectedly;
- remove wrong item;
- duplicate lines created unexpectedly;
- stale cart after removal;
- checkout with empty cart;
- cart refers to unavailable product;
- refresh during cart updates;
- double-click add/remove;
- concurrent tabs modify cart.

### Boundary scenarios

- quantity = 1;
- maximum supported quantity;
- one item;
- many items;
- highest price product;
- multiple quantities and mixed products;
- cart emptied after previous non-empty state.

### Critical arithmetic assertion

For each item:

```text
line_total = unit_price × quantity
```

For overall order, if an order total is shown:

```text
order_total = sum(all line_totals) + adjustments
```

No hidden adjustments should be assumed. Any tax/shipping/discount logic must first be observed and documented.

### Cart-state transition model

```text
EMPTY
  |
  | Add product
  v
NON_EMPTY
  | \
  |  \ Add/change item
  |   v
  | NON_EMPTY
  |
  | Remove last item
  v
EMPTY
```

Additional state dimension:

```text
Anonymous Cart
     |
     | Login
     v
Authenticated Cart
```

The documented behavior indicates items should remain visible after login.

### Risk

**Critical** because cart defects affect order composition and financial values.

---

# 12. Checkout

### Preconditions

Normally:

- cart contains at least one product;
- user is authenticated before completing checkout;
- account contains address information.

### Documented supported journeys

1. Register while checking out
2. Register before checkout
3. Login before checkout

### Testable features

- Proceed to Checkout;
- unauthenticated-user gate;
- registration/login redirect/overlay behavior;
- cart state preserved through authentication;
- delivery address;
- billing address;
- order review;
- order comments;
- Place Order.

### Address validation

The documented expected behavior is:

```text
Registration Address
        ↓
Delivery Address at Checkout

Registration Address
        ↓
Billing Address at Checkout
```

Verify all relevant components individually:

- title/name;
- company;
- address line 1;
- address line 2;
- city;
- state;
- zipcode;
- country;
- mobile number, if displayed.

Do not settle for a visual “looks the same” check in automation. Compare normalized field values.

### Negative scenarios

- checkout with empty cart;
- proceed while logged out;
- login failure during checkout;
- registration failure during checkout;
- cart lost during authentication;
- missing address fields;
- direct navigation to checkout URL;
- refresh checkout;
- browser Back from payment;
- product removed/changed between cart and checkout;
- multiple clicks on Place Order.

### Risk

**Critical** because checkout connects identity, cart, address, and payment.

---

# 13. Payment

### Observed inputs

- Name on Card
- Card Number
- CVC
- Expiration date

### Positive scenarios

- valid accepted data leads to order confirmation.

### Validation targets

Because the public test documentation describes the fields but not complete validation rules, discover:

- mandatory fields;
- card number digit requirements;
- minimum/maximum length;
- spaces/hyphens accepted or rejected;
- non-numeric characters;
- CVC length;
- expiration month format;
- expiration year format;
- expired card rejection;
- current month/year handling;
- far-future expiration;
- name-on-card length/characters;
- trimming behavior.

### Negative scenarios

- all fields blank;
- one field blank at a time;
- invalid card number;
- letters in number;
- invalid CVC;
- expired date;
- month 00;
- month 13;
- very large year;
- duplicate submit;
- browser refresh after payment submission.

### Important practice-site limitation

Do not assume this site implements a real payment gateway, card authorization, fraud controls, PCI workflow, or irreversible financial transaction.

Document what the site actually validates rather than inventing production payment behavior.

### Risk

**Critical in a real application; High for this practice application.**

---

# 14. Order Confirmation and Invoice

### Positive scenarios

- successful payment produces success confirmation;
- Continue works;
- invoice can be downloaded after purchase;
- downloaded file exists;
- filename is sensible;
- file is non-empty;
- invoice represents purchased order.

### Invoice validation

Where technically possible, compare:

- purchased products;
- quantities;
- unit prices;
- totals;
- customer information;
- order-related identifiers.

### Negative / resilience scenarios

- repeated download;
- download after refresh;
- direct invoice access without completed order;
- filename collisions;
- partial/zero-byte download.

### Risk

**High**, particularly because confirmation and invoice are the final evidence of a completed transaction.

---

# 15. Contact Us

### Observed fields

- Name
- Email
- Subject
- Message
- File upload
- Submit

### Positive scenarios

- valid form submission;
- valid file upload;
- expected success message;
- navigation back Home.

### Negative scenarios

- required fields blank;
- malformed email;
- unsupported file type;
- zero-byte file;
- very large file;
- filename with spaces;
- filename with Unicode;
- multiple dots/extensions;
- executable-like file if accepted;
- script content in text fields;
- extremely long message;
- repeated submit.

### File-upload security considerations

A senior QA should evaluate, within authorized test boundaries:

- extension validation;
- MIME-type validation;
- size limit;
- filename sanitization;
- executable files;
- path characters;
- user feedback on rejected upload.

### Risk

**Medium-High** because uploads increase attack surface even if the page is only a testing feature.

---

# 16. Subscription

### Locations

Subscription appears in at least:

- Home footer;
- Cart footer;
- other shared page footers.

### Positive scenarios

- valid email subscription;
- success feedback;
- same behavior across pages.

### Negative scenarios

- blank;
- invalid email;
- whitespace;
- duplicate email;
- mixed-case email;
- leading/trailing spaces;
- very long email;
- repeated click.

### Key consistency check

The same footer component should behave consistently across all pages.

A defect in a shared component may affect most of the site, making it a strong regression candidate despite relatively low business complexity.

---

# 17. Product Reviews

### Observed inputs

- Name
- Email Address
- Review text
- Submit

### Positive scenarios

- valid review submitted;
- success message displayed.

### Negative / validation scenarios

- missing name;
- missing email;
- missing review;
- invalid email;
- whitespace-only review;
- very short review;
- very long review;
- special characters;
- Unicode;
- HTML/script text;
- repeated submit.

### Questions

- Is review persisted?
- Is review publicly visible?
- Is moderation involved?
- Is login required?
- Can same email submit repeatedly?
- Are reviews associated with correct product?
- Is there a maximum review length?

### Risk

**Medium**.

---

# 18. Recommended Items

### Positive scenarios

- section visible where expected;
- recommended product has valid identity/price;
- Add to Cart works;
- correct product appears in cart.

### Edge scenarios

- no recommendations;
- duplicate recommendation;
- unavailable product recommended;
- recommendation changes after refresh;
- wrong product added from carousel.

### Risk

**Low-Medium**, except when recommendation-to-cart identity is incorrect.

---

# 19. Public API Surface

The application publishes practice endpoints that can later support Postman and Rest Assured exercises.

## Observed API inventory

| Area | Method | Endpoint | Purpose |
|---|---|---|---|
| Products | GET | `/api/productsList` | Get all products |
| Products | POST | `/api/productsList` | Unsupported-method scenario |
| Brands | GET | `/api/brandsList` | Get all brands |
| Brands | PUT | `/api/brandsList` | Unsupported-method scenario |
| Search | POST | `/api/searchProduct` | Search products |
| Search | POST | `/api/searchProduct` without parameter | Missing parameter scenario |
| Login | POST | `/api/verifyLogin` | Verify valid login |
| Login | POST | `/api/verifyLogin` missing parameter | Missing credentials scenario |
| Login | DELETE | `/api/verifyLogin` | Unsupported-method scenario |
| Login | POST | `/api/verifyLogin` invalid credentials | User-not-found scenario |
| Account | POST | `/api/createAccount` | Create account |
| Account | DELETE | `/api/deleteAccount` | Delete account |
| Account | PUT | `/api/updateAccount` | Update account |
| Account | GET | `/api/getUserDetailByEmail` | Get account by email |

## QA opportunities

Cross-check UI behavior against APIs:

```text
UI Registration      <-> createAccount
UI Login             <-> verifyLogin
UI Delete Account    <-> deleteAccount
UI Product Catalog   <-> productsList
UI Brand Filters     <-> brandsList
UI Search            <-> searchProduct
Checkout Address     <-> getUserDetailByEmail
```

Not every UI action necessarily calls the public practice endpoints internally. Treat these as logical cross-system comparison opportunities, not an architectural assumption.

---

# 20. Primary Business Flows

## Flow 1 — Register → Login state → Delete account

```text
Home
  ↓
Signup / Login
  ↓
Enter Name + Email
  ↓
Enter Account Information
  ↓
Enter Address
  ↓
Create Account
  ↓
Account Created
  ↓
Logged In
  ↓
Delete Account
  ↓
Account Deleted
```

### Main assertions

- unique user created;
- correct account data persisted;
- identity displayed;
- deleted account cannot authenticate.

---

## Flow 2 — Existing User Login → Logout

```text
Home
  ↓
Signup / Login
  ↓
Valid Credentials
  ↓
Logged In
  ↓
Logout
  ↓
Login Page
```

### Assertions

- session established correctly;
- correct user identity;
- session terminated on logout.

---

## Flow 3 — Browse Product → Product Detail → Cart

```text
Products
  ↓
Select Product
  ↓
Product Details
  ↓
Set Quantity
  ↓
Add to Cart
  ↓
View Cart
```

### Assertions

- product identity unchanged;
- price unchanged;
- quantity exact;
- line total correct.

---

## Flow 4 — Search → Add Results → Login → Cart Retained

```text
Products
  ↓
Search
  ↓
Search Results
  ↓
Add Product(s)
  ↓
Cart
  ↓
Login
  ↓
Cart
```

### Critical assertion

Products added before login remain visible after login.

This is a cross-session/state transition flow and deserves high automation priority.

---

## Flow 5 — Register Before Checkout → Purchase

```text
Register
  ↓
Logged In
  ↓
Add Product
  ↓
Cart
  ↓
Checkout
  ↓
Verify Address
  ↓
Review Order
  ↓
Place Order
  ↓
Payment
  ↓
Confirmation
```

---

## Flow 6 — Register During Checkout → Purchase

```text
Anonymous User
  ↓
Add Product
  ↓
Cart
  ↓
Proceed to Checkout
  ↓
Authentication Required
  ↓
Register
  ↓
Return to Cart / Checkout
  ↓
Address + Order Review
  ↓
Payment
  ↓
Confirmation
```

### Critical risks

- anonymous cart lost during registration;
- wrong redirect after registration;
- checkout state lost;
- incorrect address.

---

## Flow 7 — Existing User → Checkout → Invoice

```text
Login
  ↓
Add Product
  ↓
Cart
  ↓
Checkout
  ↓
Address
  ↓
Order Review
  ↓
Payment
  ↓
Order Success
  ↓
Download Invoice
```

---

# 21. Dependency Map

```text
Registration
 ├── creates Login credentials
 ├── creates stored customer identity
 └── creates Address data
        ↓
      Checkout

Login
 ├── enables authenticated state
 ├── identifies account
 └── interacts with pre-login Cart

Product Catalog
 ├── feeds Product Details
 ├── feeds Search
 ├── feeds Category
 ├── feeds Brand
 ├── feeds Recommended Items
 └── feeds Cart

Cart
 ├── depends on Product identity
 ├── depends on Product price
 ├── depends on Quantity
 ├── must survive selected navigation/state changes
 └── feeds Checkout

Checkout
 ├── depends on Authentication
 ├── depends on Account Address
 ├── depends on Cart
 └── feeds Payment

Payment
 └── feeds Order Confirmation

Order Confirmation
 └── enables Invoice
```

---

# 22. State-Based Testing

A senior QA should model important states explicitly.

## Authentication state

```text
ANONYMOUS
   |
   | successful registration/login
   v
AUTHENTICATED
   |
   | logout
   v
ANONYMOUS

AUTHENTICATED
   |
   | delete account
   v
ACCOUNT_DELETED
```

### Illegal/interesting transitions

- ACCOUNT_DELETED → login with deleted credentials;
- ANONYMOUS → checkout completion;
- stale authenticated page after logout;
- login twice;
- logout twice.

---

## Cart state

```text
EMPTY
  |
  | add item
  v
HAS_ITEMS
  |      |
  |      | add/update quantity
  |      v
  |   HAS_ITEMS
  |
  | remove last item
  v
EMPTY
```

### Cross-state dimensions

- anonymous + empty cart;
- anonymous + populated cart;
- authenticated + empty cart;
- authenticated + populated cart;
- populated cart + logout;
- populated anonymous cart + login.

---

## Order state

Conceptually:

```text
CART
 ↓
CHECKOUT_REVIEW
 ↓
PAYMENT_PENDING
 ↓
ORDER_CONFIRMED
 ↓
INVOICE_AVAILABLE
```

Verify the user cannot accidentally skip required transitions.

---

# 23. Positive Test Coverage

At minimum, cover:

1. Register a new user with valid information.
2. Login with valid credentials.
3. Logout.
4. Delete account.
5. Browse all products.
6. Open product details.
7. Search an existing product.
8. Filter each major category.
9. Filter brands.
10. Add one product to cart.
11. Add multiple products.
12. Set product quantity and verify cart.
13. Remove product.
14. Preserve cart after login.
15. Register during checkout.
16. Register before checkout.
17. Login before checkout.
18. Verify delivery address.
19. Verify billing address.
20. Complete payment.
21. Verify order success.
22. Download invoice.
23. Submit Contact Us form with attachment.
24. Subscribe from Home.
25. Subscribe from Cart.
26. Submit product review.
27. Add recommended product to cart.
28. Scroll-to-top behavior.

---

# 24. Negative Test Coverage

## Authentication/account

- existing registration email;
- wrong login credentials;
- malformed email;
- missing password;
- missing mandatory registration data;
- deleted account login;
- repeated deletion.

## Search/filter

- no-result query;
- blank query;
- special characters;
- invalid category/brand route.

## Cart

- checkout empty cart;
- invalid quantity;
- zero/negative quantity;
- duplicate requests;
- remove already removed product.

## Checkout/payment

- unauthenticated checkout;
- incomplete address data;
- missing payment fields;
- invalid numeric fields;
- expired date;
- double submission.

## Forms

- invalid subscription email;
- invalid review email;
- blank review;
- invalid contact email;
- bad upload.

---

# 25. Boundary-Value Strategy

For any input with a discovered length or numeric constraint:

```text
min - 1
min
min + 1

max - 1
max
max + 1
```

Apply to:

- name;
- email;
- password;
- address;
- city/state;
- zipcode;
- mobile number;
- search;
- quantity;
- review;
- contact subject;
- contact message;
- card number;
- CVC;
- expiry values.

For quantity:

```text
negative
0
1
2
reasonable high
maximum
maximum + 1
non-integer
```

For expiration month:

```text
0
1
2
11
12
13
```

For dates:

- past;
- present;
- future;
- leap day;
- invalid calendar date.

---

# 26. Validation Rules Matrix

The table below separates known behavior from rules that must be discovered.

| Field / Feature | Known / Observed | Must Verify |
|---|---|---|
| Signup Name | Input exists | Required? allowed chars? max length? trim? |
| Signup Email | Input exists; duplicate email rejected | format, case normalization, trim, max length |
| Password | Required in account creation/API | length, complexity, spaces, special chars |
| DOB | Day/month/year represented | valid date, future, age limits |
| First/Last Name | Collected | mandatory, character limits |
| Company | Collected | optional?, max length |
| Address 1 | Collected and later reused at checkout | mandatory, length, special chars |
| Address 2 | Collected | optional?, length |
| Country | Collected | supported values, default |
| State | Collected | mandatory, length |
| City | Collected | mandatory, length |
| Zipcode | Collected | format, length, country awareness |
| Mobile | Collected | format, length, symbols |
| Login Email | Input exists | normalization, blank/malformed behavior |
| Login Password | Input exists | blank behavior, masking |
| Search | Product search exists | matching rules, whitespace, case |
| Quantity | Editable on product detail | min/max/integer enforcement |
| Review Name | Input exists | required?, length |
| Review Email | Input exists | email validation |
| Review Text | Review field exists | required?, max length |
| Subscription Email | Input exists | email validation, duplicates |
| Contact Upload | Upload supported | size/type/security validation |
| Card Number | Payment field | numeric/length/format validation |
| CVC | Payment field | numeric/length |
| Expiry | Payment fields | valid month/year, expired date |

---

# 27. High-Risk Functionality

## Critical

### 1. Registration and account persistence
Failure blocks most authenticated flows.

### 2. Authentication/session handling
Wrong user/session state can corrupt checkout behavior.

### 3. Cart state and calculations
Incorrect quantity or total directly changes the purchase.

### 4. Cart preservation across login
This is a documented cross-state business requirement.

### 5. Checkout
Combines cart, authentication, address, and order state.

### 6. Payment submission
Final transaction action; duplicate-submit and validation issues are important.

### 7. Account deletion
Destructive operation requiring safe behavior.

## High

- product/price consistency;
- checkout address mapping;
- order confirmation;
- invoice generation/download;
- UI/API data consistency.

## Medium

- search;
- category;
- brand;
- contact form;
- product review;
- subscription.

## Low / Medium

- static content;
- scroll-to-top;
- secondary navigation;
- cosmetic layout issues unless they block core actions.

---

# 28. Risk Register

| ID | Risk | Impact | Likelihood | Priority | Suggested Mitigation |
|---|---|---:|---:|---|---|
| R-01 | Cart lost during login/registration | High | Medium | Critical | Automate anonymous-cart → login transition |
| R-02 | Incorrect quantity/total | High | Medium | Critical | Calculation assertions at cart/checkout |
| R-03 | Wrong account address at checkout | High | Medium | Critical | Compare registration data to checkout |
| R-04 | Duplicate order from double-submit | High | Medium | Critical | Repeated-click/idempotency tests |
| R-05 | Account deletion leaves valid session | High | Low-Med | High | Re-auth and direct-access checks |
| R-06 | UI/API account data inconsistent | High | Medium | High | Cross-layer validation |
| R-07 | Search returns irrelevant products | Medium | Medium | Medium | Keyword relevance assertions |
| R-08 | Brand/category tagging inconsistent | Medium | Medium | Medium | Product metadata cross-check |
| R-09 | File upload validation weak | High | Medium | High | Negative upload test matrix |
| R-10 | Invalid payment inputs accepted | High | Medium | High | Equivalence/boundary coverage |
| R-11 | Invoice does not reflect order | High | Low-Med | High | Compare downloaded invoice |
| R-12 | Shared subscription component inconsistent | Low | Medium | Medium | Data-driven page coverage |
| R-13 | Test accounts pollute shared environment | Medium | High | High | Unique data + teardown |
| R-14 | Dynamic catalog makes brittle UI tests | Medium | Medium | High | Use stable IDs/API setup where possible |
| R-15 | Browser state/cookies create test coupling | High | Medium | High | Independent test isolation |

---

# 29. Test Data Strategy

## User data

Generate unique email addresses per test run.

Example pattern:

```text
qa.automation.<timestamp>.<random>@example.test
```

Do not hard-code one shared user across the whole suite. Shared users create:

- test order dependency;
- deletion conflicts;
- parallel execution failures;
- polluted address/account data.

### Suggested personas

#### User A — standard valid user
All valid fields.

#### User B — minimal valid user
Only mandatory fields once discovered.

#### User C — boundary data user
Long names/addresses.

#### User D — special character user
Names/address with punctuation and Unicode where permitted.

#### Existing user
Stable reusable login account for non-destructive authentication tests.

#### Disposable user
Created and deleted within test lifecycle.

---

## Product data

Select products by stable identity where possible.

Maintain:

```text
product_id
product_name
price
category
brand
```

Do not blindly rely on “first product” for every automated test. It is easy but fragile and tells interviewers you optimized for demo speed rather than maintainability.

---

# 30. Test Environment and Isolation

Before automation, define:

- base URL;
- supported browsers;
- browser versions;
- desktop/mobile expectations;
- cookie/session behavior;
- test data ownership;
- cleanup strategy;
- whether API can create prerequisite users;
- whether tests can run in parallel;
- whether product catalog changes;
- download directory;
- upload test-file directory.

## Isolation rule

Each automated test should ideally:

1. create or locate its prerequisites;
2. execute independently;
3. assert only the behavior it owns;
4. clean up destructive test data where appropriate.

Tests should not depend on alphabetical execution order, TestNG priority, or a previous test “probably having run.”

---

# 31. UI/API Cross-Layer Test Ideas

## Account

```text
Create account via API
        ↓
Login through UI
        ↓
Verify checkout address
        ↓
Delete via API
```

Alternative:

```text
Register via UI
       ↓
GET account detail via API
       ↓
Compare persisted fields
```

## Products

```text
GET /api/productsList
        ↓
Select known product
        ↓
Open product in UI
        ↓
Compare name/price/brand/category
```

## Search

```text
POST /api/searchProduct
        ↓
Search same keyword in UI
        ↓
Compare result identities
```

These cross-layer patterns are stronger than duplicating the same assertion separately in UI and API suites.

---

# 32. Exploratory Testing Charters

## Charter 1 — Authentication and session

Explore login/logout/account deletion while using:

- Back;
- Forward;
- refresh;
- multiple tabs;
- cart state;
- direct URLs.

Goal: detect stale authentication and inconsistent session behavior.

---

## Charter 2 — Cart persistence

Add products through different entry points, then:

- navigate;
- refresh;
- login;
- logout;
- open second tab;
- change quantity;
- remove items.

Goal: discover cart-state inconsistencies.

---

## Charter 3 — Input robustness

For all text fields, try:

- blank;
- whitespace;
- long strings;
- punctuation;
- Unicode;
- HTML-like text;
- repeated submit.

Goal: identify validation inconsistency and UI breakage.

---

## Charter 4 — Checkout interruption

Interrupt checkout using:

- Back;
- refresh;
- navigation away;
- login change;
- multiple clicks;
- opening another cart tab.

Goal: identify duplicate orders, state loss, and inconsistent review totals.

---

## Charter 5 — Catalog integrity

Sample products across categories/brands and verify:

- name;
- price;
- category;
- brand;
- detail;
- cart;
- checkout.

Goal: find inconsistent catalog data.

---

# 33. Non-Functional Test Considerations

This practice application is not necessarily designed for full production-grade non-functional testing, but a senior QA should still identify the dimensions.

## Performance

Observe:

- Home load;
- Products load;
- search response;
- add-to-cart response;
- login response;
- checkout transition.

For later API testing, record:

- response time;
- payload size;
- failure rate.

Avoid inventing an SLA. Ask for one.

---

## Compatibility

Potential coverage:

- Chromium-based browser;
- Firefox;
- WebKit/Safari if expected by project;
- desktop viewport;
- selected mobile viewport.

Confirm supported browser matrix before calling a layout difference a defect.

---

## Accessibility

Basic checks:

- keyboard navigation;
- visible focus;
- form labels;
- error association;
- image alternative text;
- heading hierarchy;
- color-independent feedback;
- buttons/links reachable without mouse.

---

## Security-oriented QA

Within permitted scope:

- HTTPS;
- session termination;
- input handling;
- file upload controls;
- authorization around account deletion;
- direct-route behavior;
- sensitive data exposure;
- payment field handling.

This is not a substitute for penetration testing.

---

# 34. Usability Checks

- errors appear near relevant fields;
- error wording is understandable;
- required fields are identifiable;
- successful actions provide feedback;
- modal dialogs are dismissible;
- navigation labels are consistent;
- cart feedback clearly distinguishes Continue Shopping vs View Cart;
- checkout makes next action obvious;
- destructive Delete Account action should have appropriately clear behavior.

---

# 35. Regression Prioritization

## P0 / Smoke

Run on every meaningful build:

1. Home loads.
2. Products loads.
3. Register user.
4. Login.
5. Add product to cart.
6. Verify cart item and total.
7. Checkout with authenticated user.
8. Complete order.
9. Logout.

## P1 / Core regression

- duplicate email;
- invalid login;
- product details;
- search;
- quantity;
- remove cart item;
- cart preserved after login;
- address verification;
- invoice;
- account deletion.

## P2 / Extended regression

- all categories;
- all brands;
- reviews;
- subscription on multiple pages;
- contact upload;
- recommended products;
- scrolling behavior;
- broader boundary matrix.

---

# 36. Automation Candidate Assessment

## Strong UI automation candidates

- login/logout;
- registration happy path;
- duplicate registration;
- core product detail check;
- search happy path;
- cart add/remove;
- quantity preservation;
- cart persistence after login;
- checkout happy path;
- address comparison;
- order success;
- invoice download;
- subscription;
- product review.

## Better suited to API automation where possible

- create disposable user;
- delete user;
- verify login combinations;
- product-list schema/data;
- brand-list schema/data;
- search data permutations;
- user-detail verification;
- large negative-data matrices.

## Mostly exploratory/manual

- visual layout;
- confusing UX;
- unusual navigation sequences;
- accessibility exploration;
- multi-tab oddities;
- unexpected field behavior;
- file upload edge exploration before stable rules are known.

---

# 37. Automation Design Implications for Later Practices

This analysis should influence the later Selenium/Rest Assured framework.

Suggested future domain/page abstractions:

```text
pages/
 ├── HomePage
 ├── LoginPage
 ├── SignupPage
 ├── ProductsPage
 ├── ProductDetailsPage
 ├── CartPage
 ├── CheckoutPage
 ├── PaymentPage
 ├── OrderConfirmationPage
 └── ContactUsPage

api/
 ├── AccountClient
 ├── ProductClient
 ├── SearchClient
 └── BrandClient

models/
 ├── User
 ├── Address
 ├── Product
 ├── CartItem
 └── PaymentData

data/
 ├── UserFactory
 ├── ProductData
 └── TestDataGenerator
```

Do not create one massive `AutomationExercisePage` class. That is not a framework; that is a junk drawer with locators.

---

# 38. Defect Severity Heuristics

## Critical

- cannot complete purchase;
- wrong customer charged/order duplicated in a real payment context;
- another user's account/address exposed;
- destructive operation affects wrong account;
- major authorization/security issue.

## High

- login impossible;
- registration impossible;
- cart lost;
- wrong product/price/quantity;
- incorrect checkout address;
- order confirmation missing after apparent transaction;
- invoice incorrect.

## Medium

- search/filter incorrect but workaround exists;
- contact/review/subscription failure;
- one browser-specific functional issue with workaround.

## Low

- text typo;
- minor alignment;
- non-blocking cosmetic issue.

Severity is impact, not developer effort.

Priority is delivery urgency, not the same thing as severity.

---

# 39. Questions and Assumptions

## Account

1. Which registration fields are mandatory?
2. What are the exact min/max field lengths?
3. What password policy applies?
4. Are emails case-insensitive?
5. Are emails trimmed?
6. Can a deleted email address be registered again?
7. Is account deletion immediate?
8. Is there any account-lockout/rate-limit policy?
9. Are newsletter preferences persisted and retrievable?

## Product

10. Is catalog data static?
11. Can products become out of stock?
12. Is price expected to change during a session?
13. Are category/brand counts contractual?
14. Is quantity limited by inventory?
15. What is maximum purchasable quantity?

## Search/filter

16. Is search case-insensitive?
17. Is matching exact, substring, or token-based?
18. Should blank search return all products or validation?
19. Can brand and category filters be combined?
20. Should search preserve filters?

## Cart

21. Where is anonymous cart stored: cookie/session/server?
22. How long should cart persist?
23. What should happen to cart on logout?
24. If anonymous and account carts both contain items, should they merge?
25. How should duplicate product addition behave?
26. Is there a maximum number of cart lines?

## Checkout

27. Must billing and delivery always be identical?
28. Can user edit address at checkout?
29. Is checkout allowed with an incomplete profile?
30. What happens if price changes after item was added?
31. What happens if product becomes unavailable?
32. Is an order ID generated?

## Payment

33. Is payment simulated?
34. Which card-number formats are valid?
35. What CVC length is expected?
36. Are expired cards rejected?
37. Is double submission protected?
38. Should card data ever be retained/displayed?

## Order/invoice

39. What data must invoice contain?
40. Is invoice available after leaving confirmation page?
41. Should invoice be regenerated or remain immutable?

## Review

42. Are reviews persisted?
43. Are they publicly visible?
44. Is moderation expected?
45. Can anonymous users review?
46. Is duplicate review submission allowed?

## Contact

47. What upload types are supported?
48. What maximum upload size applies?
49. Are uploaded files stored?
50. Are potentially executable formats blocked?

## Non-functional

51. Supported browsers?
52. Supported device widths?
53. Accessibility target, if any?
54. Performance expectations?
55. API rate limits?
56. Test environment reset process?

---

# 40. Explicit Assumptions

Until confirmed:

- the website is a practice environment, not a real commerce production system;
- payment is treated as simulated unless evidence proves otherwise;
- product prices shown in the UI are the authoritative values for UI validation;
- no tax/shipping rules are assumed unless displayed;
- account email should behave as a unique identifier because duplicate registration is rejected;
- registration address is expected to populate both delivery and billing checkout sections;
- cart is expected to remain populated when an anonymous user logs in, based on the documented test scenario;
- published public APIs are suitable for test practice but are not assumed to be the exact backend calls used by every UI operation;
- validation rules not explicitly documented must be discovered empirically before automation hard-codes them.

---

# 41. Senior QA Exit Criteria for “Application Understanding”

Before moving to serious automation, the QA should be able to answer:

- What are the major modules?
- Which flows create revenue/business value?
- Which state is stored in the user account?
- Which state is stored in the cart?
- Which flows require authentication?
- What survives login/logout?
- Which fields are mandatory?
- What are the important input boundaries?
- How does product identity remain consistent across screens?
- How is total calculated?
- What information reaches checkout?
- What makes an order “successful”?
- Which APIs can create/clean test data?
- Which flows are destructive?
- Which tests require unique data?
- Which cases can safely run in parallel?
- Which failures should stop a release?

If those questions cannot be answered, automation is premature. Automating unknown behavior merely creates very fast confusion.

---

# 42. Recommended Practice Execution

For Practice 1, manually execute at least these sessions.

## Session A — Happy-path reconnaissance

1. Browse Home.
2. Open Products.
3. Open several Product Details pages.
4. Search.
5. Use category filter.
6. Use brand filter.
7. Add several products.
8. Modify quantity.
9. Remove a product.
10. Register.
11. Checkout.
12. Complete payment.
13. Download invoice.
14. Logout.
15. Login again.
16. Delete disposable account.

Capture observations rather than merely Pass/Fail.

---

## Session B — Validation reconnaissance

Probe:

- registration;
- login;
- search;
- quantity;
- subscription;
- review;
- contact;
- payment.

For every input, establish:

- required?
- allowed characters?
- min/max?
- trimming?
- error message?
- client-side or server-side?
- state after failure?

---

## Session C — Cross-module state

Focus only on:

- anonymous cart → login;
- anonymous cart → registration;
- registration address → checkout;
- product detail → cart → checkout;
- logout → cart;
- delete account → login attempt.

---

## Session D — Resilience

Use:

- Back;
- Forward;
- refresh;
- repeated clicks;
- second tab;
- direct URL.

Do this around:

- login;
- cart;
- checkout;
- payment;
- account deletion.

---

# 43. Suggested Evidence to Capture

For meaningful defects/observations, record:

- Test/Observation ID
- Date/time
- Environment
- Browser/version
- Preconditions
- Test data
- Steps
- Expected
- Actual
- Screenshot/video
- Console/network evidence where relevant
- Severity
- Reproducibility
- Workaround
- Related module
- Suspected regression scope

For API observations later:

- method;
- endpoint;
- headers;
- request body/parameters;
- status code;
- response body;
- response time;
- correlation/request ID if available.

---

# 44. Traceability to Published Practice Scenarios

The official site currently publishes practice coverage including:

- Register User
- Correct Login
- Incorrect Login
- Logout
- Existing Email Registration
- Contact Us
- Test Cases Page
- Product Detail
- Search
- Subscription
- Add Products to Cart
- Product Quantity
- Register While Checkout
- Register Before Checkout
- Login Before Checkout
- Remove Product
- Category Products
- Brand Products
- Search + Cart After Login
- Product Review
- Recommended Item
- Checkout Address
- Invoice Download
- Scroll Up / Down

This document deliberately expands beyond those scripted scenarios with boundaries, negative testing, state modeling, cross-module validation, test-data management, and risk-based prioritization.

---

# 45. References

Application:

- https://automationexercise.com/

Published test scenarios:

- https://automationexercise.com/test_cases

Published API practice list:

- https://automationexercise.com/api_list

Key live pages reviewed:

- https://automationexercise.com/login
- https://automationexercise.com/products
- https://automationexercise.com/product_details/1
- https://automationexercise.com/view_cart
- https://automationexercise.com/contact_us

---

# 46. Final QA Assessment

Automation Exercise is small enough to learn quickly but has enough cross-module behavior to practice real QA thinking.

The highest-value learning is not writing Selenium selectors for every page. It is understanding the business state transitions:

```text
Anonymous → Registered → Authenticated → Logged Out → Deleted

Product → Quantity → Cart → Checkout → Payment → Order → Invoice

Anonymous Cart → Login → Authenticated Cart

Registration Address → Checkout Delivery/Billing Address
```

Those transitions should drive future test cases, API tests, Selenium coverage, and Rest Assured integration.

A 6+ year QA is expected to identify these dependencies, determine unknown rules, prioritize critical paths, isolate test data, challenge assumptions, and prevent the automation suite from becoming a collection of brittle scripted clicks.
