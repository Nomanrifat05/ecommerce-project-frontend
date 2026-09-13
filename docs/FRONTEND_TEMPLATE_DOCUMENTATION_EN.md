# ShopMate Frontend Template Documentation

This document explains how the React e-commerce frontend template is structured and how its main parts work together. The goal is not only to run this project, but also to help you build a similar frontend template yourself in the future.

> Important: This repository contains the structure, imports, state shape, and UI plan for an e-commerce application, but several pages and components are still placeholders. Treat this project as a frontend skeleton/template, not as a fully production-ready application.

---

## 1. Technology Stack

| Technology             | Responsibility                                     |
| ---------------------- | -------------------------------------------------- |
| Vite                   | Development server and production build            |
| React                  | Component-based user interface                     |
| React DOM              | Mounts the React app into the HTML `#root` element |
| React Router DOM       | Page navigation based on the URL                   |
| Redux Toolkit          | Global application and business state              |
| React Redux            | Connects React components to Redux                 |
| Tailwind CSS           | Utility-first styling                              |
| PostCSS + Autoprefixer | CSS processing and browser compatibility           |
| Axios                  | Backend API requests                               |
| React Toastify         | Toast notifications                                |
| Lucide React           | Icon library                                       |
| Stripe React           | Payment UI integration                             |

The complete dependency list is available in `package.json`.

---

## 2. How the Application Starts

The application startup flow is:

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

`index.html` is the initial HTML shell loaded by the browser.

Important parts:

- `<div id="root"></div>` is the container where React renders the application.
- `<script type="module" src="/src/main.jsx">` loads the React entry file.
- The `viewport` meta tag supports responsive layouts on mobile devices.
- The current page title is `Vite + React`; it should be changed to the real product name, such as `ShopMate`.
- `/vite.svg` is the default Vite favicon and should be replaced with a project favicon.

React does not normally render the application directly inside this file. It renders the UI inside the `#root` element.

### 2.2 `src/main.jsx`

This is the React entry point:

```jsx
createRoot(document.getElementById("root")).render(
  <Provider store={store}>
    <App />
  </Provider>,
);
```

This code does four things:

1. Finds the `root` element from the HTML.
2. Provides the Redux store to the entire application.
3. Renders the `App` component.
4. Loads `index.css` as the global stylesheet.

Any component that uses `useSelector` or `useDispatch` must be rendered inside this Redux `Provider`.

---

## 3. `App.jsx`: The Main Application Composition

`src/App.jsx` combines three major responsibilities: theme management, routing, and the shared layout.

### 3.1 Theme provider

```jsx
<ThemeProvider>...</ThemeProvider>
```

The provider makes the current theme and the `toggleTheme()` function available throughout the application.

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

The route table decides which page should appear for each URL.

| URL            | Page                       |
| -------------- | -------------------------- |
| `/`            | Home                       |
| `/products`    | Product listing            |
| `/product/:id` | Product details            |
| `/cart`        | Cart                       |
| `/orders`      | Orders                     |
| `/payment`     | Payment                    |
| `/about`       | About                      |
| `/faq`         | Frequently asked questions |
| `/contact`     | Contact                    |
| Any other URL  | NotFound                   |

The `:id` in `/product/:id` is a dynamic route parameter. It can be read with `useParams()` inside the product detail page.

### 3.3 Global layout

The following components are placed outside `Routes`, so they are available on every page:

- `Navbar`
- `Sidebar`
- `SearchOverlay`
- `CartSidebar`
- `ProfilePanel`
- `LoginModal`
- `Footer`
- `ToastContainer`

This is an important layout pattern: shared UI is defined once instead of being copied into every page.

---

## 4. Folder Structure

```text
src/
├── App.jsx                 # Root composition and routes
├── main.jsx                # React mount point
├── index.css               # Global CSS, design tokens, custom utilities
├── App.css                 # Vite starter CSS; mostly unused
├── assets/                 # Local images, fonts, and other assets
├── components/             # Reusable UI components
│   ├── Home/               # Home page sections
│   ├── Layout/             # Navbar, footer, sidebar, and modals
│   └── Products/           # Product-related reusable UI
├── contexts/               # Context API based global behavior
├── data/                   # Temporary or static frontend data
├── lib/                    # Shared library configuration
├── pages/                  # Route-level screens
└── store/                  # Redux store and slices
```

### `pages/` versus `components/`

- `pages/` contains complete screens associated with routes, such as `Home`, `Cart`, and `Contact`.
- `components/` contains smaller reusable UI pieces, such as `ProductCard`, `Navbar`, and `Pagination`.
- If a page becomes large, divide its sections into reusable components.

A simple rule:

> Keep URL-level screens in `pages/` and reusable UI in `components/`.

---

## 5. How the Home Page Works

The structure of `src/pages/Home.jsx` is:

```text
Home
├── HeroSlider
├── CategoryGrid
├── ProductSlider: New Arrivals
├── ProductSlider: Top Rated Products
├── FeatureSection
└── NewsletterSection
```

The Home page reads product data from Redux:

```jsx
const { topRatedProducts, newProducts } = useSelector((state) => state.product);
```

Product sections are rendered only when data exists:

```jsx
{
  newProducts.length > 0 && <ProductSlider products={newProducts} />;
}
```

This is called conditional rendering. It prevents an empty product section from appearing when there is no data.

### Home components

#### `HeroSlider.jsx`

- Stores the active slide in local `currentSlide` state.
- Automatically changes slides every eight seconds with `setInterval`.
- Provides next and previous slide controls.
- Clears the interval in the `useEffect` cleanup function.
- Uses React Router `Link` for category-specific call-to-action buttons.

#### `CategoryGrid.jsx`

- Reads category data from `data/products.js`.
- Uses `.map()` to render a card for every category.
- Sends users to a URL such as `/products?category=Electronics` when a category is selected.

#### `ProductSlider.jsx`

This should be a reusable product-list presentation component. Its responsibilities normally include:

1. Displaying a section title.
2. Looping through the product list.
3. Showing product image, name, price, and rating.
4. Dispatching cart actions.
5. Linking to the product detail page.

The current component is still a placeholder, so this behavior must be implemented.

#### `FeatureSection.jsx`

This component renders icons, titles, and descriptions from a `features` array. This is a data-driven UI pattern: to add a new feature, update the data instead of duplicating the markup.

#### `NewsletterSection.jsx`

- Stores the email input in local state.
- Uses a controlled input with both `value` and `onChange`.
- Does not currently submit data to a backend; a submit handler and API request must be added for production use.

---

## 6. Layout Components

The components in `src/components/Layout/` form the shared application shell.

| Component           | Responsibility                                                  |
| ------------------- | --------------------------------------------------------------- |
| `Navbar.jsx`        | Brand, navigation, theme, search, account, and cart controls    |
| `Sidebar.jsx`       | Mobile or off-canvas navigation                                 |
| `SearchOverlay.jsx` | Search input and search-result interaction                      |
| `CartSidebar.jsx`   | Quick cart preview and quantity controls                        |
| `ProfilePanel.jsx`  | Profile information, profile update, and logout UI              |
| `LoginModal.jsx`    | Login and signup popup                                          |
| `Footer.jsx`        | Footer links, contact information, newsletter, and social links |

`Footer.jsx` is one of the most complete components in the current template. It demonstrates:

- Rendering navigation links from data objects.
- Using `Link` for internal routes.
- Rendering icons from an array.
- Reusing custom classes such as `glass`, `glass-panel`, `glass-card`, and `gradient-primary`.

### Common modal/sidebar pattern

A global modal or sidebar normally follows this flow:

```text
Redux popup state
   |
   v
isCartOpen / isAuthPopupOpen / isSidebarOpen
   |
   v
Component reads state with useSelector
   |
   v
Component renders when the value is true
   |
   v
Button click -> dispatch(toggleCart())
```

This template has a dedicated `popupSlice.js` for this purpose. However, its reducer bodies are currently empty, so the actions still need to be implemented.

---

## 7. Page Components

### `About.jsx`

- Defines an array of company values.
- Each value contains an icon component, title, and description.
- Uses `.map()` to render value cards.
- Uses `value.icon` to render a dynamic icon component.

### `FAQ.jsx`

- Stores open/closed FAQ state in the `openItems` object.
- Toggles a boolean using each FAQ item's index.
- Allows multiple FAQ items to remain open at the same time.
- Uses `ChevronDown` and `ChevronUp` to communicate the current state.

### `Contact.jsx`

- Stores all form values inside the `formData` object.
- Uses controlled inputs.
- Prevents the browser's default form submission in `handleSubmit`.
- Currently displays an alert and resets the form; production code should send the data to an API.

### `NotFound.jsx`

- Provides the fallback screen for unknown routes.
- Uses `Link` to return to the home page.
- Uses `window.history.back()` to return to the previous page.

### Incomplete route pages

The following files have their imports and planned dependencies prepared, but their JSX is still a placeholder:

- `pages/Products.jsx`
- `pages/ProductDetail.jsx`
- `pages/Cart.jsx`
- `pages/Orders.jsx`
- `pages/Payment.jsx`

Implement the UI structure first, then connect Redux and API behavior.

---

## 8. Redux Architecture

### 8.1 Store

`src/store/store.js` combines all slices into one store:

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

The corresponding state paths are:

```jsx
state.auth;
state.popup;
state.cart;
state.product;
state.order;
```

### 8.2 Slice responsibilities

#### `authSlice.js`

Authentication-related state includes:

- `authUser`
- Login and signup loading states
- Profile update loading state
- Password reset loading state
- Authentication checking state

`axiosInstance` is already imported, but the async thunks and reducers are not complete.

#### `productSlice.js`

Product-related state includes:

- All products
- Selected product details
- Total product count
- Top-rated products
- New products
- Reviews
- Loading flags
- AI search status

#### `cartSlice.js`

The cart item list is stored in the `cart` array. Typical reducers should include:

- `addToCart`
- `removeFromCart`
- `increaseQuantity`
- `decreaseQuantity`
- `clearCart`

The current reducer list is empty.

#### `orderSlice.js`

Order and payment state includes:

- `myOrders`
- Order loading state
- Order placement loading state
- `finalPrice`
- `orderStep`
- `paymentIntent`

#### `popupSlice.js`

Global UI visibility state includes:

- Authentication popup
- Sidebar
- Search bar
- Cart sidebar
- AI modal

### 8.3 Using Redux inside a component

```jsx
import { useDispatch, useSelector } from "react-redux";

const cartItems = useSelector((state) => state.cart.cart);
const dispatch = useDispatch();

dispatch(addToCart(product));
```

Use:

- `useSelector` to read data from the store.
- `useDispatch` to run reducer actions.
- Redux for shared business data used by multiple pages.
- `useState` for state that belongs only to one component.

---

## 9. Context API and Theme System

`src/contexts/ThemeContext.jsx` uses the Context API for theme management.

### Theme flow

```text
Read theme from localStorage
        |
        v
ThemeProvider state
        |
        v
toggleTheme()
        |
        v
Add light or dark class to <html>
        |
        v
CSS variables change the colors
```

A component can access the theme through the custom hook:

```jsx
const { theme, toggleTheme } = useTheme();
```

### Why use Context instead of Redux?

- Theme is a cross-cutting UI preference.
- It is not complex business data.
- Context is sufficient for a small global behavior.
- Cart, authentication, products, and orders are domain states, so they belong in Redux.

Keeping this boundary clear prevents every piece of state from being placed in the same system.

---

## 10. Styling System

### 10.1 Tailwind CSS

Components use Tailwind utility classes:

```jsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8">
```

This means:

- One column on mobile.
- Two columns on medium screens.
- Four columns on large screens.
- A gap of eight between grid items.

Common responsive prefixes:

| Prefix    | Meaning                       |
| --------- | ----------------------------- |
| No prefix | Mobile/default styles         |
| `sm:`     | Small screens and above       |
| `md:`     | Medium screens and above      |
| `lg:`     | Large screens and above       |
| `xl:`     | Extra-large screens and above |

### 10.2 CSS variables

`src/index.css` defines design tokens such as:

- `--background`
- `--foreground`
- `--primary`
- `--secondary`
- `--border`
- `--radius`
- Glass and shadow variables

The Tailwind configuration maps these variables to semantic utility classes:

```js
background: "hsl(var(--background))";
primary: "hsl(var(--primary))";
```

This allows components to use `bg-background`, `text-foreground`, and `bg-primary` instead of hard-coded colors. It also makes theme changes easier.

### 10.3 Custom utility classes

`index.css` defines reusable classes such as:

- `.glass`
- `.glass-panel`
- `.glass-card`
- `.gradient-primary`
- `.gradient-glass`
- `.animate-smooth`
- `.glow-primary`
- `.glow-on-hover`

Use a custom class when the same visual style is needed in multiple places.

### 10.4 `App.css`

`App.css` still contains Vite starter styles such as `.logo`, `.read-the-docs`, and the logo animation. These are not important for the current e-commerce interface and can be removed later.

Be careful with the starter `#root` styles. A global `max-width`, padding, and centered text can unintentionally constrain the layout of the entire e-commerce application.

---

## 11. Data and API Layer

### Static data: `src/data/products.js`

This file contains a hard-coded category list. Static data is useful during UI development. When the backend is ready, API data should be stored in Redux instead of being embedded directly inside components.

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

This configuration provides:

- A shared base URL for all API requests.
- A local backend URL during development.
- A relative URL in production.
- Cookie credentials for cookie-based authentication.

A typical request looks like this:

```js
const response = await axiosInstance.get("/products");
const products = response.data;
```

For asynchronous operations, use `createAsyncThunk` and handle four stages:

1. Set loading to `true` when the request starts.
2. Store the response data on success.
3. Store an error or show a toast on failure.
4. Set loading to `false` when the request finishes.

---

## 12. Payment Preparation

`Payment.jsx` and `PaymentForm.jsx` already contain imports for Stripe:

- `loadStripe`
- `Elements`
- `CardElement`
- `useStripe`
- `useElements`

The normal payment flow is:

```text
Cart
  |
  v
Payment page
  |
  v
Backend creates a payment intent
  |
  v
Stripe Elements displays card input
  |
  v
Stripe confirms the payment
  |
  v
Backend creates the order
  |
  v
Order success
```

Never place a card secret or private key in frontend code. A Stripe publishable key may be used by the frontend, but the secret key must remain on the backend.

---

## 13. How to Build a New Feature

Suppose you want to add a `Wishlist` feature.

### Step 1: Define the requirement

Decide:

- Can users add a product to a wishlist?
- Can they remove it?
- Is there a wishlist page?
- What happens when a logged-out user clicks the button?

### Step 2: Decide where the state belongs

- Button-only loading state: local `useState`.
- Wishlist used across multiple pages: a Redux slice.
- Modal visibility: popup slice or local state.

### Step 3: Create the slice

Create `wishlistSlice.js` with an initial state and reducers. Register the reducer inside `store.js`.

### Step 4: Create a reusable component

For example, `WishlistButton.jsx` should:

- Receive a product through props.
- Read the active state from Redux.
- Dispatch add/remove actions.
- Show loading and error states.

### Step 5: Reuse it in pages

Use the same `WishlistButton` in both `ProductCard` and `ProductDetail`.

### Step 6: Add a route

Add a `/wishlist` route in `App.jsx` if a separate wishlist page is required.

### Step 7: Connect the API

Start with mock Redux data to verify the UI. Then connect `axiosInstance` and async thunks to the backend.

### Step 8: Test every state

Check:

- Empty state
- Loading state
- Error state
- Success state
- Logged-out state
- Mobile layout

---

## 14. Recommended Implementation Order

Follow this order to keep dependencies manageable:

1. Change the title and favicon in `index.html`.
2. Remove or limit the Vite starter CSS in `App.css`.
3. Implement the reducers and actions in `popupSlice`.
4. Add cart CRUD reducers in `cartSlice`.
5. Complete the UI for `ProductCard` and `ProductSlider`.
6. Build the product listing page with filtering, query strings, and pagination.
7. Build product details with fetching, quantity controls, cart actions, and reviews.
8. Build the cart page with totals and a checkout link.
9. Complete the authentication flow.
10. Complete the order and Stripe payment flow according to the backend contract.
11. Test loading, error, empty, and mobile-responsive states.

---

## 15. Run, Build, and Lint

From the project folder:

```bash
npm install
npm run dev
```

Create a production build:

```bash
npm run build
```

Run ESLint:

```bash
npm run lint
```

Vite will display a development URL, usually similar to `http://localhost:5173`.

---

## 16. Debugging Checklist

### A page is not appearing

1. Check that `App` is rendered in `main.jsx`.
2. Check the route path in `App.jsx`.
3. Make sure the browser URL matches the route.
4. Read the browser console error.

### Redux data is undefined

1. Check that `Provider` exists.
2. Check the reducer names in `store.js`.
3. Verify the selector path, such as `state.product` or `state.cart`.
4. Check that the requested property exists in the initial state.

### The theme is not working

1. Check that `ThemeProvider` wraps the component tree.
2. Inspect the `<html>` element for a `light` or `dark` class.
3. Confirm that CSS variable names match the Tailwind configuration.

### API requests are failing

1. Confirm that the backend is running.
2. Check the base URL and endpoint.
3. Inspect the request URL and status in the browser Network tab.
4. Check CORS and credentials configuration.

### The layout looks wrong

1. Check whether the default `#root` styles in `App.css` are restricting the layout.
2. Confirm that the Tailwind content paths include all source files.
3. Check that light and dark CSS variables are defined correctly.

---

## 17. Mental Model for Building Your Own React Template

Think of a frontend template as six layers:

```text
1. Entry layer       -> main.jsx
2. Composition layer -> App.jsx and shared layout
3. Route layer       -> pages and router
4. UI layer          -> reusable components
5. State layer       -> Redux, Context, and local state
6. Service layer     -> Axios, APIs, payments, and utilities
```

For every new feature, ask:

- Which page owns this UI?
- Which parts should be reusable?
- Who owns the state?
- Is the data local, static, or from the backend?
- What are the loading, error, and empty states?
- Does this feature need a route?
- How should it work on mobile?

Answering these questions before coding keeps the codebase easier to maintain as it grows.

---

## Summary

The main idea of this template is:

- `main.jsx` starts the application.
- `App.jsx` combines the layout and routes.
- `pages` represent complete screens.
- `components` represent reusable UI.
- Redux stores shared business state.
- Context handles smaller global behavior such as themes.
- Axios provides a shared API entry point.
- Tailwind and CSS variables control the visual system.

When adding a new feature, define its route, UI component, state owner, and API contract first. Then build the smallest reusable component and integrate it into the page. This is the most reusable lesson from this template.
