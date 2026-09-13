# ShopMate Frontend Template Documentation

এই ডকুমেন্টটি এই React e-commerce frontend template কীভাবে কাজ করে তা বোঝানোর জন্য লেখা। লক্ষ্য হলো শুধু বর্তমান project চালানো নয়, একই architecture ব্যবহার করে ভবিষ্যতে নিজে frontend template তৈরি করতে শেখা।

> গুরুত্বপূর্ণ: এই repository-তে অনেক page এবং component-এর import, state shape ও UI structure প্রস্তুত আছে, কিন্তু বেশ কিছু component এখনো placeholder অবস্থায় আছে (`return <></>` বা empty reducer)। তাই এটিকে একটি frontend skeleton/template হিসেবে দেখুন, সম্পূর্ণ production-ready application হিসেবে নয়।

---

## 1. Template-এর Technology Stack

| Technology             | কাজ                                                      |
| ---------------------- | -------------------------------------------------------- |
| Vite                   | Development server এবং production build                  |
| React                  | Component-based UI তৈরি                                  |
| React DOM              | React application-কে HTML-এর `#root` element-এ mount করে |
| React Router DOM       | URL অনুযায়ী page পরিবর্তন                                |
| Redux Toolkit          | Global application state পরিচালনা                        |
| React Redux            | React component থেকে Redux state/action ব্যবহার          |
| Tailwind CSS           | Utility class দিয়ে styling                               |
| PostCSS + Autoprefixer | CSS process এবং browser compatibility                    |
| Axios                  | Backend API request                                      |
| React Toastify         | Toast notification                                       |
| Lucide React           | Icons                                                    |
| Stripe React           | Payment UI-এর প্রস্তুত integration                       |

Dependency-গুলোর মূল তালিকা দেখা যাবে `package.json`-এ।

---

## 2. Application কীভাবে শুরু হয়

পুরো application-এর startup flow:

```text
index.html
   |
   v
src/main.jsx
   |
   v
Redux Provider + App
   |
   v
src/App.jsx
   |
   +--> ThemeProvider
   +--> BrowserRouter
   +--> Global Layout
   +--> Routes / Pages
   +--> Footer + ToastContainer
```

### 2.1 `index.html`

`index.html` হলো browser-এর initial HTML shell। এখানে:

- `<div id="root"></div>` React application বসানোর জায়গা।
- `<script type="module" src="/src/main.jsx">` React entry file load করে।
- `viewport` meta tag mobile responsive layout-এর জন্য দরকার।
- বর্তমানে title `Vite + React`; project branding অনুযায়ী এটি `ShopMate` করা উচিত।
- `/vite.svg` favicon-ও template starter asset; নিজের favicon ব্যবহার করা উচিত।

React সাধারণত এই file-এ সরাসরি UI লেখে না। React UI `#root`-এর ভিতরে render করে।

### 2.2 `src/main.jsx`

এই file application-এর entry point। এখানে:

```jsx
createRoot(document.getElementById("root")).render(
  <Provider store={store}>
    <App />
  </Provider>,
);
```

এর অর্থ:

1. HTML থেকে `root` element নেওয়া হচ্ছে।
2. Redux `Provider` দিয়ে পুরো app-কে `store`-এর access দেওয়া হচ্ছে।
3. `App` component render করা হচ্ছে।
4. `index.css` global stylesheet হিসেবে load হচ্ছে।

কোনো component-এ `useSelector` বা `useDispatch` ব্যবহার করতে হলে সেটিকে এই Redux `Provider`-এর ভিতরে থাকতে হবে।

---

## 3. `App.jsx`: Application-এর মূল composition

`src/App.jsx`-এ তিন ধরনের জিনিস একসাথে জোড়া হয়েছে:

### 3.1 Theme Provider

```jsx
<ThemeProvider>...</ThemeProvider>
```

এটি theme state এবং `toggleTheme()` function পুরো application-এ available করে।

### 3.2 Router

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Index />} />
    <Route path="/products" element={<Products />} />
    ...
  </Routes>
</BrowserRouter>
```

কোন URL-এ কোন page দেখাবে, তা এখানে নির্ধারিত হয়।

| URL             | Page             |
| --------------- | ---------------- |
| `/`             | Home             |
| `/products`     | Products listing |
| `/product/:id`  | Product detail   |
| `/cart`         | Cart             |
| `/orders`       | Orders           |
| `/payment`      | Payment          |
| `/about`        | About            |
| `/faq`          | FAQ              |
| `/contact`      | Contact          |
| অন্য যেকোনো URL | NotFound         |

`/product/:id`-এর `:id` হলো dynamic route parameter। Product detail page-এ `useParams()` দিয়ে এটি পড়া যায়।

### 3.3 Global layout

`Routes`-এর বাইরে রাখা component সব page-এ দেখা যায়:

- `Navbar`
- `Sidebar`
- `SearchOverlay`
- `CartSidebar`
- `ProfilePanel`
- `LoginModal`
- `Footer`
- `ToastContainer`

এটি খুব গুরুত্বপূর্ণ pattern। Repeated UI page-এর ভিতরে copy না করে `App.jsx`-এ একবার রাখা হয়েছে।

---

## 4. Folder structure বোঝা

```text
src/
├── App.jsx                 # Root composition এবং routes
├── main.jsx                # React mount point
├── index.css               # Global CSS, design tokens, custom utilities
├── App.css                 # Vite starter CSS; বেশিরভাগই unused
├── assets/                 # Local images/fonts/other assets
├── components/             # Reusable UI components
│   ├── Home/               # Home page-এর section
│   ├── Layout/             # Navbar, footer, sidebar, modal
│   └── Products/           # Product-related reusable UI
├── contexts/               # Context API based global behavior
├── data/                   # Temporary/static frontend data
├── lib/                    # Shared libraries/configuration
├── pages/                  # Route-level screen
└── store/                  # Redux store এবং slices
```

### `pages/` বনাম `components/`

- `pages/` একটি সম্পূর্ণ route/screen-এর দায়িত্ব নেয়। যেমন `Home`, `Cart`, `Contact`।
- `components/` ছোট reusable অংশ। যেমন `ProductCard`, `Navbar`, `Pagination`।
- একটি page বড় হলে সেটিকে section/component-এ ভাগ করা উচিত।

সহজ নিয়ম:

> URL-level screen রাখুন `pages/`-এ, পুনরায় ব্যবহারযোগ্য UI রাখুন `components/`-এ।

---

## 5. Home page কীভাবে তৈরি হয়েছে

`src/pages/Home.jsx`-এর layout:

```text
Home
├── HeroSlider
├── CategoryGrid
├── ProductSlider: New Arrivals
├── ProductSlider: Top Rated Products
├── FeatureSection
└── NewsletterSection
```

Home page Redux থেকে product state নেয়:

```jsx
const { topRatedProducts, newProducts } = useSelector((state) => state.product);
```

তারপর data থাকলে product section দেখায়:

```jsx
{
  newProducts.length > 0 && <ProductSlider products={newProducts} />;
}
```

এখানে conditional rendering ব্যবহার হয়েছে। অর্থাৎ data না থাকলে empty product section দেখাবে না।

### Home components

#### `HeroSlider.jsx`

- local `currentSlide` state রাখে।
- `setInterval` দিয়ে প্রতি ৮ সেকেন্ডে slide বদলায়।
- `nextSlide` এবং `prevSlide` manual navigation করে।
- `useEffect` cleanup-এ interval clear করে।
- CTA button `Link` ব্যবহার করে product category page-এ যায়।

#### `CategoryGrid.jsx`

- `data/products.js` থেকে categories নেয়।
- `.map()` দিয়ে প্রতিটি category card বানায়।
- category click করলে query string সহ `/products?category=...` URL-এ যায়।

#### `ProductSlider.jsx`

এটি product list-এর reusable presentation component হওয়া উচিত। সাধারণত এর দায়িত্ব:

1. title দেখানো।
2. product list loop করা।
3. product image, name, price, rating দেখানো।
4. cart action dispatch করা।
5. product detail link দেওয়া।

বর্তমানে componentটির UI placeholder অবস্থায় আছে, তাই এই behavior সম্পূর্ণ করতে হবে।

#### `FeatureSection.jsx`

`features` array থেকে icon, title এবং description render করে। এই pattern-কে data-driven UI বলা যায়। একই markup বারবার না লিখে data array পরিবর্তন করলেই নতুন feature যোগ করা যায়।

#### `NewsletterSection.jsx`

- `email` input-এর জন্য local state ব্যবহার করে।
- controlled input: `value={email}` এবং `onChange` দুটোই আছে।
- বর্তমানে form submit backend-এ পাঠানো হয়নি; submit handler যোগ করতে হবে।

---

## 6. Layout components

`src/components/Layout/`-এর component-গুলো পুরো application-এর shared shell।

| Component           | উদ্দেশ্য                                                 |
| ------------------- | -------------------------------------------------------- |
| `Navbar.jsx`        | Brand, navigation, theme, search, account, cart controls |
| `Sidebar.jsx`       | Mobile বা off-canvas navigation                          |
| `SearchOverlay.jsx` | Search input এবং search result interaction               |
| `CartSidebar.jsx`   | Quick cart preview এবং quantity controls                 |
| `ProfilePanel.jsx`  | Profile information/update/logout UI                     |
| `LoginModal.jsx`    | Login/signup popup                                       |
| `Footer.jsx`        | Footer links, contact, newsletter, social links          |

`Footer.jsx` বর্তমানে সবচেয়ে পূর্ণ componentগুলোর একটি। এখানে:

- link data object থেকে navigation list render হয়।
- `Link` internal route-এর জন্য ব্যবহার হয়েছে।
- social icon array থেকে icon render হয়।
- `glass`, `glass-panel`, `glass-card`, `gradient-primary` custom class ব্যবহার হয়েছে।

### Modal/sidebar-এর সাধারণ pattern

একটি global modal বা sidebar সাধারণত এভাবে কাজ করে:

```text
Redux popup state
   |
   v
isCartOpen / isAuthPopupOpen / isSidebarOpen
   |
   v
Component useSelector দিয়ে state নেয়
   |
   v
true হলে UI render করে
   |
   v
button click -> dispatch(toggleCart())
```

এই template-এ popup state-এর জন্য `popupSlice.js` রাখা হয়েছে। তবে slice-এ reducer body এখনো লেখা হয়নি, তাই action implementation সম্পূর্ণ করতে হবে।

---

## 7. Page components

### `About.jsx`

- company values-এর array বানায়।
- প্রতিটি value-তে icon component, title, description আছে।
- `.map()` দিয়ে value cards render করে।
- `value.icon` দিয়ে dynamic component render করা হয়েছে।

### `FAQ.jsx`

- `openItems` object-এ কোন FAQ খোলা আছে তা রাখে।
- একটি FAQ খুললে index অনুযায়ী boolean toggle হয়।
- একই সময়ে একাধিক FAQ open থাকতে পারে।
- `ChevronDown` এবং `ChevronUp` icon দিয়ে state বোঝানো হয়েছে।

### `Contact.jsx`

- `formData` object-এ সব input-এর value রাখে।
- controlled inputs ব্যবহার করা হয়েছে।
- `handleSubmit` default browser submit বন্ধ করে।
- বর্তমানে শুধু alert দেখায় এবং form reset করে; production-এ API request লাগবে।

### `NotFound.jsx`

- unknown route-এর fallback screen।
- `Link` দিয়ে home-এ ফেরে।
- `window.history.back()` দিয়ে আগের page-এ ফেরে।

### অসম্পূর্ণ route pages

নিচের file-গুলোতে import প্রস্তুত, কিন্তু JSX UI এখনো placeholder:

- `pages/Products.jsx`
- `pages/ProductDetail.jsx`
- `pages/Cart.jsx`
- `pages/Orders.jsx`
- `pages/Payment.jsx`

এগুলোতে প্রথমে UI structure, তারপর Redux/API behavior বসাতে হবে।

---

## 8. Redux architecture

### 8.1 Store

`src/store/store.js`-এ সব slice একত্র করা হয়েছে:

```jsx
export const store = configureStore({
  reducer: {
    auth: authReducer,
    popup: popupReducer,
    cart: cartReducer,
    product: productReducer,
    order: orderReducer,
  },
});
```

তাই component-এ state path হবে:

```jsx
state.auth;
state.popup;
state.cart;
state.product;
state.order;
```

### 8.2 Slice-এর দায়িত্ব

#### `authSlice.js`

Authentication-related state:

- `authUser`
- login/signup loading
- profile update loading
- password reset loading
- auth checking state

API call-এর জন্য `axiosInstance` import করা হয়েছে, কিন্তু async thunk এবং reducers এখনো সম্পূর্ণ নয়।

#### `productSlice.js`

Product state:

- সব products
- selected product details
- total product count
- top-rated products
- new products
- reviews
- loading flags
- AI search status

#### `cartSlice.js`

Cart-এর item list `cart` array-তে থাকবে। সাধারণ reducer হওয়া উচিত:

- `addToCart`
- `removeFromCart`
- `increaseQuantity`
- `decreaseQuantity`
- `clearCart`

বর্তমানে reducers empty।

#### `orderSlice.js`

Order এবং payment flow-এর state:

- `myOrders`
- order loading
- placing order loading
- `finalPrice`
- `orderStep`
- `paymentIntent`

#### `popupSlice.js`

Global UI visibility state:

- auth popup
- sidebar
- search bar
- cart sidebar
- AI modal

এখানে action নাম export করা হয়েছে, কিন্তু reducer implementation যোগ করতে হবে।

### 8.3 Redux component-এ ব্যবহার

```jsx
import { useDispatch, useSelector } from "react-redux";

const cartItems = useSelector((state) => state.cart.cart);
const dispatch = useDispatch();

dispatch(addToCart(product));
```

- `useSelector`: store থেকে data পড়তে।
- `useDispatch`: store-এর reducer action চালাতে।
- component-এর ভিতরে server data রাখার বদলে shared data Redux-এ রাখুন।
- শুধু component-এর নিজের UI state হলে `useState` ব্যবহার করুন।

---

## 9. Context API এবং Theme system

`src/contexts/ThemeContext.jsx` theme-এর জন্য Context API ব্যবহার করেছে।

### Theme flow

```text
localStorage থেকে theme পড়া
        |
        v
ThemeProvider state
        |
        v
toggleTheme()
        |
        v
<html>-এ light অথবা dark class
        |
        v
CSS variables-এর রং পরিবর্তন
```

`useTheme()` custom hook ব্যবহার করে যেকোনো child component theme পেতে পারে:

```jsx
const { theme, toggleTheme } = useTheme();
```

### Redux-এর বদলে Context কেন?

- Theme একটি cross-cutting UI preference।
- এটি complex business state নয়।
- ছোট global behavior-এর জন্য Context যথেষ্ট।
- Cart, auth, product, order-এর মতো domain state Redux-এ রাখা হয়েছে।

এই boundary পরিষ্কার রাখা গুরুত্বপূর্ণ: সবকিছু Redux-এ বা সবকিছু Context-এ রাখবেন না।

---

## 10. Styling system

### 10.1 Tailwind

Component-গুলোতে utility class ব্যবহার হয়েছে:

```jsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8">
```

অর্থ:

- mobile-এ ১ column
- medium screen-এ ২ column
- large screen-এ ৪ column
- grid item-এর মধ্যে gap ৮

Responsive prefix:

| Prefix          | সাধারণ অর্থ             |
| --------------- | ----------------------- |
| কোনো prefix নেই | mobile/default          |
| `sm:`           | small screen থেকে       |
| `md:`           | medium screen থেকে      |
| `lg:`           | large screen থেকে       |
| `xl:`           | extra large screen থেকে |

### 10.2 CSS variables

`src/index.css`-এ design token রাখা হয়েছে:

- `--background`
- `--foreground`
- `--primary`
- `--secondary`
- `--border`
- `--radius`
- glass এবং shadow variables

Tailwind config-এ এগুলোকে semantic color হিসেবে map করা হয়েছে:

```js
background: "hsl(var(--background))";
primary: "hsl(var(--primary))";
```

ফলে component-এ সরাসরি color value না লিখে `bg-background`, `text-foreground`, `bg-primary` ব্যবহার করা যায়। Theme পরিবর্তন সহজ হয়।

### 10.3 Custom component utilities

`index.css`-এ reusable class আছে:

- `.glass`
- `.glass-panel`
- `.glass-card`
- `.gradient-primary`
- `.gradient-glass`
- `.animate-smooth`
- `.glow-primary`
- `.glow-on-hover`

একই visual style বহু জায়গায় দরকার হলে custom class ব্যবহার করা ভালো।

### 10.4 `App.css`

`App.css`-এ Vite starter styles রয়ে গেছে, যেমন `.logo`, `.read-the-docs`, logo animation। বর্তমান e-commerce UI-র জন্য এগুলো প্রয়োজনীয় নয়। পরে পরিষ্কার করা যেতে পারে। বিশেষ করে `#root`-এ `max-width`, `padding`, `text-align: center` রাখলে পুরো ecommerce layout অপ্রত্যাশিতভাবে সীমাবদ্ধ হতে পারে।

---

## 11. Data এবং API layer

### Static data: `src/data/products.js`

এখানে category list hard-code করা আছে। এটি development/mock data হিসেবে ভালো। Backend থেকে data এলে এই data সরাসরি component-এ না রেখে API response Redux-এ রাখা উচিত।

### Axios: `src/lib/axios.js`

```js
export const axiosInstance = axios.create({
  baseURL:
    import.meta.env.MODE === "development"
      ? "http://localhost:4000/api/v1"
      : "/",
  withCredentials: true,
});
```

এর সুবিধা:

- প্রতিটি request-এ full base URL লিখতে হয় না।
- development-এ local backend ব্যবহার হয়।
- production-এ relative URL ব্যবহার হয়।
- `withCredentials: true` cookie-based authentication-এর জন্য ব্যবহৃত হয়।

সাধারণ API pattern:

```js
const response = await axiosInstance.get("/products");
const products = response.data;
```

Async operation-এর জন্য সাধারণত `createAsyncThunk` ব্যবহার করে:

1. request শুরুতে loading true।
2. success হলে data store-এ।
3. failure হলে error/toast।
4. শেষে loading false।

---

## 12. Payment preparation

`Payment.jsx` এবং `PaymentForm.jsx`-এ Stripe-এর imports প্রস্তুত আছে:

- `loadStripe`
- `Elements`
- `CardElement`
- `useStripe`
- `useElements`

সঠিক flow সাধারণত:

```text
Cart
  |
  v
Payment page
  |
  v
Backend payment intent তৈরি
  |
  v
Stripe Elements-এ card input
  |
  v
Stripe confirm payment
  |
  v
Backend-এ order create
  |
  v
Order success
```

কার্ডের secret বা private key কখনো frontend-এ রাখা যাবে না। Stripe publishable key frontend-এ থাকতে পারে, secret key শুধু backend-এ থাকবে।

---

## 13. একটি নতুন feature নিজে বানানোর নিয়ম

ধরা যাক, `Wishlist` feature বানাবেন। এই ধারাটি অনুসরণ করুন:

### Step 1: Requirement লিখুন

- user product wishlist-এ যোগ করবে
- wishlist থেকে remove করবে
- wishlist page দেখবে
- login না করলে কী হবে নির্ধারণ করুন

### Step 2: State ownership নির্ধারণ করুন

- শুধু button-এর loading: local `useState`
- wishlist সব page-এ দরকার: Redux slice
- modal খোলা/বন্ধ: popup slice বা local state

### Step 3: Slice লিখুন

`wishlistSlice.js`-এ initial state ও reducers লিখুন। তারপর `store.js`-এ reducer যোগ করুন।

### Step 4: Reusable component বানান

যেমন `WishlistButton.jsx`:

- product prop নেবে
- Redux state থেকে active status জানবে
- dispatch করে add/remove করবে
- loading/error state দেখাবে

### Step 5: Page-এ বসান

`ProductCard` এবং `ProductDetail`-এ একই `WishlistButton` reuse করুন।

### Step 6: Route যোগ করুন

`App.jsx`-এ `/wishlist` route যোগ করুন।

### Step 7: API integration করুন

প্রথমে mock state দিয়ে UI verify করুন, পরে `axiosInstance` এবং async thunk দিয়ে backend যুক্ত করুন।

### Step 8: সব state test করুন

- empty state
- loading state
- error state
- success state
- logged-out state
- mobile layout

---

## 14. এই template-এ বর্তমান কাজের priority

এই order-এ কাজ করলে dependency কম থাকবে:

1. `index.html`-এর title/favicon ঠিক করা।
2. `App.css`-এর Vite starter CSS সরানো বা সীমিত করা।
3. popup slice-এর reducer/action সম্পূর্ণ করা।
4. `cartSlice`-এর CRUD reducers লেখা।
5. `ProductCard` এবং `ProductSlider` UI সম্পূর্ণ করা।
6. `Products.jsx`-এ listing, filter, query string ও pagination যোগ করা।
7. `ProductDetail.jsx`-এ product fetch, quantity, cart, review যোগ করা।
8. `Cart.jsx`-এ totals এবং checkout link যোগ করা।
9. auth flow সম্পূর্ণ করা।
10. order এবং Stripe payment flow backend contract অনুযায়ী সম্পূর্ণ করা।
11. loading, error, empty state এবং mobile responsiveness পরীক্ষা করা।

---

## 15. Run, build এবং lint

Project folder থেকে:

```bash
npm install
npm run dev
```

Production build check:

```bash
npm run build
```

Lint check:

```bash
npm run lint
```

Vite সাধারণত development server-এর URL দেখাবে, যেমন `http://localhost:5173`।

---

## 16. Debugging checklist

### Page দেখা যাচ্ছে না

1. `main.jsx`-এ `App` render হচ্ছে কি না দেখুন।
2. `App.jsx`-এ route path ঠিক আছে কি না দেখুন।
3. Browser URL route-এর সঙ্গে মিলে কি না দেখুন।
4. Console error পড়ুন।

### Redux data undefined

1. `Provider` আছে কি না দেখুন।
2. `store.js`-এ reducer-এর নাম দেখুন।
3. `useSelector` path ঠিক কি না দেখুন: `state.product`, `state.cart` ইত্যাদি।
4. initial state-এ property আছে কি না দেখুন।

### Theme কাজ করছে না

1. `ThemeProvider` component tree-এর উপরে আছে কি না দেখুন।
2. `<html>` element-এ `light` বা `dark` class যোগ হচ্ছে কি না দেখুন।
3. CSS variable-এর নাম Tailwind config-এর সঙ্গে মিলে কি না দেখুন।

### API request fail করছে

1. backend চলছে কি না দেখুন।
2. base URL এবং endpoint ঠিক কি না দেখুন।
3. browser Network tab-এ request URL/status দেখুন।
4. CORS এবং credentials configuration দেখুন।

### UI-এর style অদ্ভুত

1. `App.css`-এর Vite default `#root` style layout আটকে দিচ্ছে কি না দেখুন।
2. Tailwind content path source file cover করছে কি না দেখুন।
3. CSS variable light/dark theme-এ define আছে কি না দেখুন।

---

## 17. নিজে নতুন React template বানানোর mental model

একটি ভালো frontend template সাধারণত এই ৬টি layer-এ ভাবুন:

```text
1. Entry layer       -> main.jsx
2. Composition layer -> App.jsx এবং layout
3. Route layer       -> pages এবং router
4. UI layer          -> reusable components
5. State layer       -> Redux / Context / local state
6. Service layer     -> axios, API, payment, utilities
```

প্রতিটি feature-এ নিজেকে জিজ্ঞাসা করুন:

- এই UI কোন page-এ থাকবে?
- কোন অংশ reusable?
- state-এর owner কে?
- data local, static, না backend থেকে?
- loading/error/empty state কী হবে?
- route দরকার কি?
- mobile layout কী হবে?

এই প্রশ্নগুলোর উত্তর আগে ঠিক করলে codebase দ্রুত বড় হলেও structure পরিষ্কার থাকে।

---

## সংক্ষিপ্ত সারাংশ

এই template-এর মূল idea হলো:

- `main.jsx` app চালু করে।
- `App.jsx` layout এবং route একত্র করে।
- `pages` screen তৈরি করে।
- `components` reusable UI তৈরি করে।
- Redux shared business state রাখে।
- Context theme-এর মতো ছোট global behavior রাখে।
- Axios backend-এর সঙ্গে যোগাযোগের common entry point।
- Tailwind এবং CSS variables পুরো design system নিয়ন্ত্রণ করে।

একটি নতুন feature যোগ করার সময় প্রথমে তার route, UI component, state owner এবং API contract ঠিক করুন; তারপর ছোট component থেকে page-level integration করুন। এটাই এই template-এর সবচেয়ে গুরুত্বপূর্ণ reusable lesson।
