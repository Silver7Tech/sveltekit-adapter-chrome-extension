# sveltekit-adapter-chrome-extension

[Adapter](https://kit.svelte.dev/docs#adapters) for SvelteKit apps that prerenders your site as a collection of static files and removes inline scripts to comply with content source policies of Chrome extensions using manifest v3.

> Based on [@sveltekit/adapter-static](https://github.com/sveltejs/kit/blob/master/packages/adapter-static). Credit goes to [these people](https://github.com/sveltejs/kit/graphs/contributors) for their hard work to make Svelte so great

## Usage

Install with `npm i -D sveltekit-adapter-chrome-extension`, then add the adapter to your `svelte.config.js`:

```js
// svelte.config.js
import adapter from "sveltekit-adapter-chrome-extension";

export default {
  kit: {
    adapter: adapter({
      // default options are shown
      pages: "build",
      assets: "build",
      fallback: null,
      precompress: false,
      manifest: "manifest.json"
    }),
    appDir: "app",
  },
};
```

## Options

### pages

The directory to write prerendered pages to. It defaults to `build`.

### assets

The directory to write static assets (the contents of `static`, plus client-side JS and CSS generated by SvelteKit) to. Ordinarily this should be the same as `pages`, and it will default to whatever the value of `pages` is, but in rare circumstances you might need to output pages and assets to separate locations.

### fallback

Specify a fallback page for SPA mode, e.g. `index.html` or `200.html` or `404.html`.

### precompress

If `true`, precompresses files with brotli and gzip. This will generate `.br` and `.gz` files.

### manifest

Specify manifest file name if you want different manifests for different target platforms, e.g. `chrome_manifest.json`, `firefox_manifest.json`.
This file name must match one that is present in the `static` directory (the dir containing your static files). The selected target file will be renamed to `manifest.json` in the build directory, and all other `.json` files with `manifest` in the name won't be copied.

## SPA mode

You can use `adapter-static` to create a single-page app or SPA by specifying a **fallback page**.

> In most situations this is not recommended: it harms SEO, tends to slow down perceived performance, and makes your app inaccessible to users if JavaScript fails or is disabled (which happens [more often than you probably think](https://kryogenix.org/code/browser/everyonehasjs.html)).

The fallback page is a blank HTML page that loads your SvelteKit app and navigates to the correct route. For example [Surge](https://surge.sh/help/adding-a-200-page-for-client-side-routing), a static web host, lets you add a `200.html` file that will handle any requests that don't otherwise match. We can create that file like so:

```js
// svelte.config.js
import adapter from "@sveltejs/adapter-static";

export default {
  kit: {
    adapter: adapter({
      fallback: "200.html",
    }),
  },
};
```

When operating in SPA mode, only pages that have the [`prerender`](https://kit.svelte.dev/docs#page-options-prerender) option set will be prerendered.

## GitHub Pages

When building for GitHub Pages, make sure to update [`paths.base`](https://kit.svelte.dev/docs#configuration-paths) to match your repo name, since the site will be served from https://your-username.github.io/your-repo-name rather than from the root.

You will have to prevent GitHub's provided Jekyll from managing your site by putting an empty `.nojekyll` file in your static folder. If you do not want to disable Jekyll, change the kit's `appDir` configuration option to `'app_'` or anything not starting with an underscore. For more information, see GitHub's [Jekyll documentation](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll#configuring-jekyll-in-your-github-pages-site).

A config for GitHub Pages might look like the following:

```js
const dev = process.env.NODE_ENV === 'development';

/** @type {import('@sveltejs/kit').Config} */
const config = {
	...
	kit: {
		...
		paths: {
			base: dev ? '' : '/your-repo-name',
		},
		// If you are not using a .nojekyll file, change your appDir to something not starting with an underscore.
		// For example, instead of '_app', use 'app_', 'internal', etc.
		appDir: 'internal',
	}
};
```

## License

[MIT](LICENSE)
