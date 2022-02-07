# Style Library Interoperability

<p class="description">While you can use the emotion based styling solution provided by MUI to style your application, you can also use the one you already know and love (from plain CSS to styled-components).</p>

This guide aims to document the most popular alternatives,
but you should find that the principles applied here can be adapted to other libraries.
There are examples for the following styling solutions:

- [Plain CSS](#plain-css)
- [Global CSS](#global-css)
- [Styled Components](#styled-components)
- [CSS Modules](#css-modules)
- [Emotion](#emotion)
- [Tailwind CSS](#tailwind-css)

## Plain CSS

Nothing fancy, just plain CSS.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Edit Button](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/plain-css-fdue7)

**PlainCssSlider.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}
```

**PlainCssSlider.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import './PlainCssSlider.css';

export default function PlainCssSlider() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className="slider" />
    </div>
  );
}
```

### CSS injection order ⚠️

**Note:** Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over your custom styles. To remove the need for **!important**, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@mui/material/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function PlainCssPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

### Deeper elements

If you attempt to style the Slider,
you will likely need to affect some of the Slider's child elements, for example the thumb.
In MUI, all child elements have an increased specificity of 2: `.parent .child {}`. When writing overrides, you need to do the same.

The following examples override the slider's `thumb` style in addition to the custom styles on the slider itself.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**PlainCssSliderDeep1.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .MuiSlider-thumb {
  border-radius: 1px;
}
```

**PlainCssSliderDeep1.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import './PlainCssSliderDeep1.css';

export default function PlainCssSliderDeep1() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className="slider" />
    </div>
  );
}
```

The above demo relies on the [default `className` values](/styles/advanced/#with-material-ui-core), but you can provide your own class name with the `componentsProps` API.

**PlainCssSliderDeep2.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .thumb {
  border-radius: 1px;
}
```

**PlainCssSliderDeep2.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import './PlainCssSliderDeep2.css';

export default function PlainCssSliderDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider
        defaultValue={30}
        className="slider"
        componentsProps={{ thumb: { className: 'thumb' } }}
      />
    </div>
  );
}
```

## Global CSS

Explicitly providing the class names to the component is too much effort?
[You can target the class names generated by MUI](/styles/advanced/#with-material-ui-core).

[![Edit Button](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/global-classnames-dho8k)

**GlobalCssSlider.css**

```css
.MuiSlider-root {
  color: #20b2aa;
}

.MuiSlider-root:hover {
  color: #2e8b57;
}
```

**GlobalCssSlider.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import './GlobalCssSlider.css';

export default function GlobalCssSlider() {
  return <Slider defaultValue={30} />;
}
```

### CSS injection order ⚠️

**Note:** Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over your custom styles. To remove the need for **!important**, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@mui/material/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function GlobalCssPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

### Deeper elements

If you attempt to style the Slider,
you will likely need to affect some of the Slider's child elements, for example the thumb.
In MUI, all child elements have an increased specificity of 2: `.parent .child {}`. When writing overrides, you need to do the same.

The following example overrides the slider's `thumb` style in addition to the custom styles on the slider itself.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**GlobalCssSliderDeep.css**

```css
.MuiSlider-root {
  color: #20b2aa;
}

.MuiSlider-root:hover {
  color: #2e8b57;
}

.MuiSlider-root .MuiSlider-thumb {
  border-radius: 1px;
}
```

**GlobalCssSliderDeep.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import './GlobalCssSliderDeep.css';

export default function GlobalCssSliderDeep() {
  return <Slider defaultValue={30} />;
}
```

## Styled Components

![stars](https://img.shields.io/github/stars/styled-components/styled-components.svg?style=social&label=Star)
![npm](https://img.shields.io/npm/dm/styled-components.svg)

### Change the default styled engine

By default, MUI components come with emotion as their style engine. If,
however, you would like to use `styled-components`, you can configure your app by following the [styled engine guide](/guides/styled-engine/#how-to-switch-to-styled-components) or starting with one of the example projects:

<!-- #default-branch-switch -->

- [Create React App with styled-components](https://github.com/mui/material-ui/tree/master/examples/create-react-app-with-styled-components)
- [Create React App with styled-components and typescript](https://github.com/mui/material-ui/tree/master/examples/create-react-app-with-styled-components-typescript)

Following this approach reduces the bundle size, and removes the need to configure the CSS injection order.

After the style engine is configured properly, you can use the [`styled()`](/system/styled/) utility
from `@mui/material/styles` and have direct access to the theme.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Edit Button](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/styled-components-interoperability-w9z9d)

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
import { styled } from '@mui/material/styles';

const CustomizedSlider = styled(Slider)`
  color: #20b2aa;

  :hover {
    color: #2e8b57;
  }
`;

export default function StyledComponents() {
  return <CustomizedSlider defaultValue={30} />;
}
```

### Deeper elements

If you attempt to style the Slider,
you will likely need to affect some of the Slider's child elements, for example the thumb.
In MUI, all child elements have an increased specificity of 2: `.parent .child {}`. When writing overrides, you need to do the same.

The following examples override the slider's `thumb` style in addition to the custom styles on the slider itself.

{{"demo": "StyledComponentsDeep.js", "defaultCodeOpen": true}}

The above demo relies on the [default `className` values](/styles/advanced/#with-material-ui-core), but you can provide your own class name with the `componentsProps` API.

```jsx
import * as React from 'react';
import { styled } from '@mui/material/styles';
import Slider from '@mui/material/Slider';

const CustomizedSlider = styled((props) => (
  <Slider componentsProps={{ thumb: { className: 'thumb' } }} {...props} />
))`
  color: #20b2aa;

  :hover {
    color: #2e8b57;
  }

  & .thumb {
    border-radius: 1px;
  }
`;

export default function StyledComponentsDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <CustomizedSlider defaultValue={30} />
    </div>
  );
}
```

### Theme

By using the MUI theme provider, the theme will be available in the theme context
of the styled engine too (emotion or styled-components, depending on your configuration).

> ⚠️ If you are **already** using a custom theme with styled-components or emotion,
> it might not be compatible with MUI's theme specification. If it's not
> compatible, you need to render MUI's ThemeProvider <b>first</b>. This will
> ensure the theme structures are isolated. This is ideal for the progressive adoption
> of MUI's components in the codebase.

You are encouraged to share the same theme object between MUI and the rest of your project.

```jsx
const CustomizedSlider = styled(Slider)(
  ({ theme }) => `
  color: ${theme.palette.primary.main};

  :hover {
    color: ${darken(theme.palette.primary.main, 0.2)};
  }
`,
);
```

{{"demo": "StyledComponentsTheme.js"}}

### Portals

The [Portal](/components/portal/) provides a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component.
Because of the way styled-components scopes its CSS, you may run into issues where styling is not applied.

For example, if you attempt to style the `tooltip` generated by the [Tooltip](/components/tooltips/) component,
you will need to pass along the `className` property to the element being rendered outside of it's DOM hierarchy.
The following example shows a workaround:

```jsx
import * as React from 'react';
import { styled } from '@mui/material/styles';
import Button from '@mui/material/Button';
import Tooltip from '@mui/material/Tooltip';

const StyledTooltip = styled(({ className, ...props }) => (
  <Tooltip {...props} classes={{ popper: className }} />
))`
  & .MuiTooltip-tooltip {
    background: navy;
  }
`;
```

{{"demo": "StyledComponentsPortal.js"}}

## CSS Modules

![stars](https://img.shields.io/github/stars/css-modules/css-modules.svg?style=social&label=Star)

It's hard to know the market share of [this styling solution](https://github.com/css-modules/css-modules) as it's dependent on the
bundling solution people are using.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Edit Button](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/css-modules-nuyg8)

**CssModulesSlider.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}
```

**CssModulesSlider.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSlider.module.css';

export default function CssModulesSlider() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className={styles.slider} />
    </div>
  );
}
```

### CSS injection order ⚠️

**Note:** Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over your custom styles. To remove the need for **!important**, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@mui/material/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function CssModulesPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

### Deeper elements

If you attempt to style the Slider,
you will likely need to affect some of the Slider's child elements, for example the thumb.
In MUI, all child elements have an increased specificity of 2: `.parent .child {}`. When writing overrides, you need to do the same.

The following examples override the slider's `thumb` style in addition to the custom styles on the slider itself.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**CssModulesSliderDeep1.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .MuiSlider-thumb {
  border-radius: 1px;
}
```

**CssModulesSliderDeep1.js**

```jsx
import * as React from 'react';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSliderDeep1.module.css';
import Slider from '@mui/material/Slider';

export default function CssModulesSliderDeep1() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className={styles.slider} />
    </div>
  );
}
```

The above demo relies on the [default `className` values](/styles/advanced/#with-material-ui-core), but you can provide your own class name with the `componentsProps` API.

**CssModulesSliderDeep2.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .thumb {
  border-radius: 1px;
}
```

**CssModulesSliderDeep2.js**

```jsx
import * as React from 'react';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSliderDeep2.module.css';
import Slider from '@mui/material/Slider';

export default function CssModulesSliderDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider
        defaultValue={30}
        className={styles.slider}
        componentsProps={{ thumb: { className: styles.thumb } }}
      />
    </div>
  );
}
```

## Emotion

![stars](https://img.shields.io/github/stars/emotion-js/emotion.svg?style=social&label=Star)
![npm](https://img.shields.io/npm/dm/@emotion/react.svg)

### The `css` prop

Emotion's **css()** method works seamlessly with MUI.

{{"demo": "EmotionCSS.js", "defaultCodeOpen": true}}

### Theme

It works exactly like styled components. You can [use the same guide](/guides/interoperability/#styled-components).

### The `styled()` API

It works exactly like styled components. You can [use the same guide](/guides/interoperability/#styled-components).

## Tailwind CSS

![stars](https://img.shields.io/github/stars/tailwindlabs/tailwindcss.svg?style=social&label=Star)
![npm](https://img.shields.io/npm/dm/tailwindcss)

If you are used to Tailwind CSS and want to use it together with the MUI components, you can start by cloning the [Tailwind CSS](https://github.com/mui/material-ui/tree/master/examples/tailwind) example project.
If you use a different framework, or already have set up your project, follow these steps:

1. Add Tailwind CSS to your project, following the instructions in https://tailwindcss.com/docs/installation.
2. Remove Tailwind's `base` directive in favor of the `CSSBaseline` component provided by `@mui/material`, as it plays nicer with the MUI components.

**index.css**

```diff
-@tailwind base;
 @tailwind components;
 @tailwind utilities;
```

3. Add the `important` option, using the id of your app wrapper, for example `"#root"`.

**tailwind.config.js**

```diff
 module.exports = {
   content: [
     "./src/**/*.{js,jsx,ts,tsx}",
   ],
+  important: '#root',
   theme: {
     extend: {},
   },
   plugins: [],
 }

```

This is necessary for ensuring that the [deeper elements](#deeper-elements-5), can be customized using Tailwind's utility classes.
More details on this option can be found here https://tailwindcss.com/docs/configuration#selector-strategy

4. Fix the CSS injection order

**Note:** Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over your custom styles. To remove the need for **!important**, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@mui/material/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using emotion and have a custom cache in your app, it will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function PlainCssPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look at the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

Now it's all set up and you can start using Tailwind CSS on the MUI components!

**App.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function App() {
  return (
    <div>
      <Slider defaultValue={30} className="color-indigo-500" />
    </div>
  );
}
```

### Deeper elements

If you attempt to style the Slider, for example, you'll likely want to customize its child elements.

This example showcases how to override the Slider's `thumb`style.

**SliderThumbOverrides.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function SliderThumbOverrides() {
  return (
    <Slider
      className="p-4"
      componentsProps={{ thumb: { className: 'hover:shadow-none' } }}
    />
  );
}
```

### Styling pseudo states

If you want to style a component's pseudo-state, you can use the appropriate key in the `classes` prop.
Here is an example of how you can style the Slider's active state:

**SliderPseudoStateOverrides.js**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function SliderThumbOverrides() {
  return <Slider className="p-4" classes={{ active: 'shadow-none' }} />;
}
```