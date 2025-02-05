+++
date = '2025-02-05T15:47:35+05:30'
draft = true
title = 'Forgejo Logo Change'
+++

# Customizing Logo in Forgejo
Customizing the logo and favicon in Forgejo is quite simple and we just need an `.svg` file that needs to replaced with the current Forgejo icons.
> [Read the docs for changing the logo](https://forgejo.org/docs/latest/contributor/customization/#changing-the-logo)

Let's use `fedora.svg` as an example for this:
1. Head inside the parent directory of **`forgejo`**.
2. Copy the `fedora.svg` into **`assets/`**.
3. Change the name of `fedora.svg` into **`favicon.svg`** & **`logo.svg`** (*making it two files*).
4. From the parent directory of **`forgejo`**, run this command:
```sh
make generate-images
```

The above command will generate the respective files into **`public/assets/img`**, that is:
- `public/assets/img/logo.svg` - Used for site icon, app icon
- `public/assets/img/logo.png` - Used for Open Graph
- `public/assets/img/avatar_default.png` - Used as the default avatar image
- `public/assets/img/apple-touch-icon.png` - Used on iOS devices for bookmarks
- `public/assets/img/favicon.svg` - Used for favicon
- `public/assets/img/favicon.png` - Used as fallback for browsers that don’t support SVG favicons

In simple terms, we don't have to manually add the images into `public/assets/img` and everything will be generated using that command.