# Setting Up Tailwind CSS 4.0 with Svelte 5 and SvelteKit 2.0

This guide explains how our project implements Tailwind CSS 4.0 with Svelte 5 and SvelteKit 2.0, which differs significantly from the standard Tailwind CSS 3 approach.

## Key Differences from Tailwind CSS 3

Tailwind CSS 4.0 introduces a "CSS-first" approach where more configuration happens directly in CSS files rather than in a JavaScript config file. Our setup embraces this new philosophy for a cleaner implementation.

### Package Installation

```bash
pnpm install tailwindcss@latest @tailwindcss/vite@latest
```

## Core Files

### 1. Minimal `tailwind.config.js`

Unlike Tailwind CSS 3 which requires extensive theme configuration in the JavaScript config file, our approach uses a minimal configuration that only handles content paths and dark mode:

```js
/** @type {import('tailwindcss').Config} */
export default {
	content: ['./src/**/*.{html,js,svelte,ts}'],
	darkMode: ['class', '[data-mode="dark"]']
};
```

### 2. Theme-Driven `app.css`

The heart of our setup is the `app.css` file where we:

- Import Tailwind using the new import syntax
- Define custom dark mode variant
- Define all theme variables directly in CSS
- Create utility classes that map to our theme variables

```css
@import 'tailwindcss';

/* Dark mode variant */
@custom-variant dark (&:where(.dark, .dark *, [data-mode="dark"], [data-mode="dark"] *));

/* Define theme variables */
@theme {
	/* Light mode variables */
	--background: #ffffff;
	--foreground: #09090b;
	/* ...other variables... */
}

/* Define utility classes that map to theme variables */
@layer base {
	.bg-background {
		background-color: var(--background);
	}
	.bg-foreground {
		background-color: var(--foreground);
	}
	/* ...other utility classes... */
}

/* Dark mode overrides */
@dark {
	--background: #09090b;
	--foreground: #fafafa;
	/* ...other dark mode variables... */
}
```

### 3. Vite Plugin Setup

In `vite.config.ts`, we use the new Tailwind Vite plugin:

```ts
import { sveltekit } from '@sveltejs/kit/vite';
import { defineConfig } from 'vite';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
	plugins: [tailwindcss(), sveltekit()]
});
```

## How Components Use Themed Classes

Our components use standard Tailwind class names that map to our theme variables:

```svelte
<div class="bg-primary text-primary-foreground rounded-md p-4">Themed content</div>
```

The CSS classes like `bg-primary` are connected to our theme variables through the mappings in `app.css`.

## Benefits of This Approach

1. **Cleaner Configuration**: Most of the styling logic is in CSS where it belongs
2. **Better Theme Control**: Direct access to CSS variables in your CSS file
3. **No HSL Variable Format Required**: Unlike shadcn-ui with Tailwind 3, we don't need to use the HSL variable format
4. **Easier Dark Mode**: Dark mode works seamlessly with the `@dark` directive
5. **Future-Proof**: Aligned with Tailwind 4's philosophy of "CSS in CSS"

## Troubleshooting Theme Issues

If theme colors are not being applied:

1. **Check CSS Variable Definitions**: Make sure they're defined in the `@theme` block
2. **Ensure Class Mappings**: Verify the @layer base section has mappings for all utility classes
3. **Restart Dev Server**: Sometimes a server restart is needed after CSS changes

## Potential Issues with shadcn-svelte Components

If using shadcn-svelte components, note that they were designed for Tailwind CSS 3's theme approach using HSL variables. Our direct mapping approach with `@layer base` resolves compatibility issues.

## Additional Notes

- When adding new theme colors, add them to both the `@theme` block and create corresponding utility classes in `@layer base`
- This setup works for both light and dark modes through the `@dark` directive
- Arbitrary value syntax (`text-[--primary]`) can still be used for direct CSS variable access if needed

By following this approach, we've successfully implemented a Tailwind CSS 4.0 theming system with Svelte 5 and SvelteKit 2.0 that is both maintainable and performant.
