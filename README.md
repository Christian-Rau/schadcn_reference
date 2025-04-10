⚠️ **Warning**: This setup is not intended for development. It is an original setup from [shadcn-svelte installation guide](https://next.shadcn-svelte.com/docs/installation/sveltekit) that has been edited for test purposes and is therefore broken. Proceed with caution! ⚠️

# Integrating Tailwind CSS 4.0 with shadcn-svelte Forms in an Existing Svelte 5 Project

This guide explains how we adapted our ongoing Svelte 5/SvelteKit 2.0 project to use Tailwind CSS 4.0 in order to take advantage of shadcn-svelte form components. This approach differs significantly from the standard Tailwind CSS 3 setup and was implemented mid-development rather than at project inception.

## Motivation

Several days into development, we needed to integrate shadcn-svelte form components for their powerful validation capabilities and consistent styling. This required adjusting our existing Tailwind setup to properly support these components while maintaining our established design system.

## Key Differences from Tailwind CSS 3

Tailwind CSS 4.0 introduces a "CSS-first" approach where more configuration happens directly in CSS files rather than in a JavaScript config file. Our setup embraces this new philosophy for a cleaner implementation that supports shadcn-svelte components.

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

## Using shadcn-svelte Form Components

The primary goal of this implementation was to integrate shadcn-svelte form components, which offer robust form handling with Formsnap and Zod validation. These were installed using:

```bash
pnpm dlx shadcn-svelte@next add form
```

### Benefits of shadcn-svelte Forms with Our Setup

The form components from shadcn-svelte (https://next.shadcn-svelte.com/docs/components/form) work seamlessly with our Tailwind 4.0 setup:

1. **Form Validation**: Built-in support for Zod schemas for type-safe form validation
2. **Form State Management**: Utilizes Formsnap for efficient form state handling
3. **Themed Components**: The form elements use our theme variables automatically
4. **Dark Mode Support**: Forms adapt to light/dark modes through our CSS variables

### Example Usage

```svelte
<script lang="ts">
	import { superForm } from 'sveltekit-superforms';
	import { formSchema } from './schema';
	import * as Form from '$lib/components/ui/form';

	const form = superForm(data.form);
</script>

<Form.Root {form} schema={formSchema}>
	<Form.Field {form} name="username">
		<Form.Label>Username</Form.Label>
		<Form.Control>
			<Form.Input placeholder="Enter username" />
		</Form.Control>
		<Form.HelperText>Enter your username</Form.HelperText>
		<Form.ErrorMessage />
	</Form.Field>

	<Button type="submit">Submit</Button>
</Form.Root>
```

## Simplified PostCSS Configuration

For simplicity, we chose not to create a `postcss.config.js` file. The `@tailwindcss/vite` plugin handles the necessary PostCSS processing directly, which reduces configuration complexity.

This approach works well with Tailwind 4.0's CSS-first philosophy and helps maintain a cleaner project structure with fewer configuration files.

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

⚠️ **Warning**: This setup is not intended for development. It is an original setup from [shadcn-svelte installation guide](https://next.shadcn-svelte.com/docs/installation/sveltekit) that has been edited for test purposes and is therefore broken. Proceed with caution! ⚠️
