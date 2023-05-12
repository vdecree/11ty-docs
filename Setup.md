For new builds we use Ryan's awesome scaffold that enables us to hit the ground running, it can be cloned [from here](https://github.com/bly-th/eleventy). We will be using 11ty with netlify CMS - VERY COOL.

1. In order to get netlify CMS installed, we can follow a very simple process which is to firstly add an `admin` directory within the `src` directory.
2. Inside our newly created `admin` dir, add the two following files:
	1. `config.yml`
	2. `index.njk`
3. Inside the `config.yml` paste the following:
```yml
local_backend: true

backend:
  name: git-gateway
  branch: master
media_folder: 'src/uploads'
public_folder: '/uploads'

navigation: &NAVIGATION
  label: 'Navigation'
  name: 'eleventyNavigation'
  widget: 'object'
  collasped: true
  fields:
    - { label: 'Key', name: 'key', widget: 'string', required: false }
    - { label: 'Parent', name: 'parent', widget: 'string', required: false }
    - { label: 'Order', name: 'order', widget: 'number', required: false }
    - { label: 'Excerpt', name: 'excerpt', widget: 'string', required: false }

meta: &META
  label: 'Meta'
  name: 'meta'
  widget: 'object'
  collapsed: true
  fields:
    - { label: 'Title', name: 'title', widget: 'string', required: false }
    - { label: 'Description', name: 'desc', widget: 'string', required: false }
    - { label: 'Social Image', name: 'socialImage', widget: 'image', required: false }

button: &BUTTON
  label: 'Button'
  name: 'button'
  widget: 'object'
  collapsed: true
  fields:
    - { label: 'Text', name: 'text', widget: 'string', required: false }
    - { label: 'Link', name: 'link', widget: 'string', required: false }

themeOptions: &THEMEOPTIONS
  - { label: 'Red', value: 'bg-primary' }
  - { label: 'Green', value: 'bg-secondary' }

theme: &THEME
  label: 'Theme'
  name: 'theme'
  widget: 'select'
  collapsed: true
  required: false
  options:
    - { label: 'Red', value: 'bg-primary color-light' }
    - { label: 'Green', value: 'bg-secondary color-light' }
    - { label: 'Bagle', value: 'bg-bagle color-dark' }
    - { label: 'Gold', value: 'bg-secondary color-gold' }
    - { label: 'Attention', value: 'bg-secondary color-attention' }

blocks: &BLOCKS
  label: 'Blocks'
  name: 'blocks'
  widget: 'list'
  types:
    - label: 'Text'
      name: 'text'
      widget: object
      fields:
        - { label: 'Content', name: 'content', widget: 'markdown' }
        - { label: 'Spacing Top', name: 'spacingtop', widget: 'string', required: false }
        - { label: 'Spacing Bottom', name: 'spacingbottom', widget: 'string', required: false }
    - label: 'Image'
      name: 'image'
      widget: 'object'
      fields:
        - { label: 'Image', name: 'image', widget: 'image' }
        - { label: 'Alt', name: 'alt', widget: 'string', required: false }

collections:
  - label: 'Pages'
    name: 'pages'
    folder: 'src'
    identifier_field: 'title'
    create: true
    filter: {field: "type", value: "page"} # I think i can use this to control what is seen in the CMS for the page listing
    meta: { path: { widget: string, label: 'Path', index_file: 'index' } }
    nested:
      depth: 100 # max depth to show in the collection tree
      summary: '{{title}}' # optional summary for a tree node, defaults to the inferred title field
    extension: 'md'
    fields:
      - { label: 'Layout', name: 'layout', widget: 'hidden', default: 'layouts/page.njk' }
      - { label: 'Page type', name: 'type', widget: 'hidden', default: 'page' } #set a custom field of type equal to page - we then use a filter above to only list the pages where the type is set to 'page'
      - { label: 'Title', name: 'title', widget: 'string' }
      - *BLOCKS
      - *META
      - *NAVIGATION
```
4. Next up let's add the following to the `index.njk`:
```njk
---
permalink: '/admin/index.html'
layout: null
sitemapIgnore: true
---
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Content Manager</title>
  <script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
</head>
<body>
  <!-- Include the script that builds the page and powers Netlify CMS -->
  <script src="https://unpkg.com/netlify-cms@^2.9.3/dist/netlify-cms.js"></script>
</body>
</html>
```
5. Add passthroughs for `/admin/` in `eleventy.js`. Paste the following into eleventy.js:
```js
eleventyConfig.addPassthroughCopy('./src/admin/');
eleventyConfig.addPassthroughCopy('./src/uploads/');
```
6. Add the admin NPM task in order to start the proxy server for Netlify CMS. Add the following to the package.json
```json
"admin": "npx netlify-cms-proxy-server",
```

```
## Windows users

By default if you run `npm run dev` it will error due to Windows needing another package added to help with the `NODE_ENV` bits. Run the following `npm install --save-dev cross-env` and then update the following line: `"dev:css": "cross-env NODE_ENV=production npx tailwindcss -o src/_includes/css/utility/tokens.css --watch",`. Now you're good to go.

## Run 11ty & Netlify CMS

SO to run 11ty and netlify CMS we can run `npm run admin & npm run dev`. This builds our project and gives us live reload - Boom!

### A few basic things to remember

Take full advantage of Tailwind for tokens and instant token generation without the need to stop the NPM process. Leverage the power of Blyth and bath in glory.