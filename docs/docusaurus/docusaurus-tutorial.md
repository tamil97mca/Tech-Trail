# Advanced Docusaurus Tutorial: Real-World Project Guide

## Table of Contents
- [Advanced Docusaurus Tutorial: Real-World Project Guide](#advanced-docusaurus-tutorial-real-world-project-guide)
  - [Table of Contents](#table-of-contents)
  - [1. Advanced Project Structure](#1-advanced-project-structure)
  - [2. Custom Plugins Development](#2-custom-plugins-development)
  - [3. Advanced Theme Customization](#3-advanced-theme-customization)
  - [4. Complex Documentation Scenarios](#4-complex-documentation-scenarios)
    - [Versioned and Multi-Product Documentation](#versioned-and-multi-product-documentation)
  - [5. Performance Optimization](#5-performance-optimization)
    - [Code Splitting and Lazy Loading](#code-splitting-and-lazy-loading)
    - [Image Optimization](#image-optimization)
  - [6. Advanced Search Implementations](#6-advanced-search-implementations)
    - [Algolia DocSearch Configuration](#algolia-docsearch-configuration)
    - [Custom Search Implementation](#custom-search-implementation)
  - [7. Internationalization at Scale](#7-internationalization-at-scale)
  - [8. Continuous Integration and Deployment](#8-continuous-integration-and-deployment)
  - [9. Headless CMS Integration](#9-headless-cms-integration)
  - [10. API Documentation Generation](#10-api-documentation-generation)

## 1. Advanced Project Structure

For large-scale projects, consider a monorepo structure:

```
my-docusaurus-project/
├── packages/
│   ├── docs-site/
│   │   ├── docs/
│   │   ├── src/
│   │   ├── docusaurus.config.js
│   │   └── package.json
│   ├── shared-components/
│   │   ├── src/
│   │   └── package.json
│   └── custom-plugin/
│       ├── src/
│       └── package.json
├── lerna.json
└── package.json
```

Use Lerna or Yarn Workspaces for managing this structure. Here's an example `lerna.json`:

```json
{
  "packages": ["packages/*"],
  "version": "independent",
  "npmClient": "yarn",
  "useWorkspaces": true
}
```

## 2. Custom Plugins Development

Create a custom plugin for advanced functionality. Here's an example of a plugin that adds a custom webpack configuration:

```javascript
// packages/custom-plugin/src/index.js
module.exports = function (context, options) {
  return {
    name: 'custom-webpack-plugin',
    configureWebpack(config, isServer, utils) {
      return {
        module: {
          rules: [
            {
              test: /\.csv$/,
              use: 'csv-loader',
            },
          ],
        },
      };
    },
  };
};
```

Add this plugin to your `docusaurus.config.js`:

```javascript
module.exports = {
  // ...
  plugins: [
    [
      path.resolve(__dirname, '../custom-plugin/src'),
      {
        // plugin options
      },
    ],
  ],
};
```

## 3. Advanced Theme Customization

Create a fully custom theme by overriding all default components:

```
packages/docs-site/src/theme/
├── Footer/
│   └── index.js
├── Navbar/
│   └── index.js
├── Layout/
│   └── index.js
├── MDXComponents/
│   └── index.js
└── Root/
    └── index.js
```

Example of a custom `Layout` component with advanced features:

```jsx
// packages/docs-site/src/theme/Layout/index.js
import React from 'react';
import clsx from 'clsx';
import { useThemeConfig } from '@docusaurus/theme-common';
import {
  useScrollPosition,
  useLocationChange,
} from '@docusaurus/theme-common/internal';
import SkipToContent from '@theme/SkipToContent';
import AnnouncementBar from '@theme/AnnouncementBar';
import Navbar from '@theme/Navbar';
import Footer from '@theme/Footer';
import LayoutProviders from '@theme/LayoutProviders';
import ErrorBoundary from '@docusaurus/ErrorBoundary';

export default function Layout(props) {
  const {
    children,
    noFooter,
    wrapperClassName,
    // Not really layout-related, but kept for convenience/retro-compatibility
    title,
    description,
  } = props;
  const { navbar, footer } = useThemeConfig();
  const [isNavbarVisible, setIsNavbarVisible] = React.useState(true);

  useScrollPosition(
    ({ scrollY }) => {
      const newNavbarVisible = scrollY < 200;
      if (newNavbarVisible !== isNavbarVisible) {
        setIsNavbarVisible(newNavbarVisible);
      }
    },
    [isNavbarVisible]
  );

  useLocationChange((locationChangeEvent) => {
    if (locationChangeEvent.location.hash) {
      const id = locationChangeEvent.location.hash.substring(1);
      const element = document.getElementById(id);
      if (element) {
        element.scrollIntoView();
      }
    }
  });

  return (
    <LayoutProviders>
      <ErrorBoundary fallback={({ error, tryAgain }) => <ErrorDisplay error={error} tryAgain={tryAgain} />}>
        <SkipToContent />
        <AnnouncementBar />
        <Navbar />
        <div className={clsx('main-wrapper', wrapperClassName)}>
          {children}
        </div>
        {!noFooter && <Footer />}
      </ErrorBoundary>
    </LayoutProviders>
  );
}
```

## 4. Complex Documentation Scenarios

### Versioned and Multi-Product Documentation

For projects with multiple products and versions, use this folder structure:

```
docs/
├── product-a/
│   ├── current/
│   │   ├── guide.md
│   │   └── api.md
│   ├── version-2.0.0/
│   │   ├── guide.md
│   │   └── api.md
│   └── version-1.0.0/
│       ├── guide.md
│       └── api.md
└── product-b/
    ├── current/
    │   ├── guide.md
    │   └── api.md
    └── version-1.0.0/
        ├── guide.md
        └── api.md
```

Configure `docusaurus.config.js` to handle multiple products:

```javascript
module.exports = {
  // ...
  presets: [
    [
      '@docusaurus/preset-classic',
      {
        docs: {
          path: 'docs/product-a',
          routeBasePath: 'docs/product-a',
          sidebarPath: require.resolve('./sidebars-product-a.js'),
          versions: {
            current: {
              label: 'Next',
              path: 'next',
            },
            '2.0.0': {
              label: '2.0.0',
              path: '2.0.0',
            },
            '1.0.0': {
              label: '1.0.0',
              path: '1.0.0',
            },
          },
        },
      },
    ],
  ],
  plugins: [
    [
      '@docusaurus/plugin-content-docs',
      {
        id: 'product-b',
        path: 'docs/product-b',
        routeBasePath: 'docs/product-b',
        sidebarPath: require.resolve('./sidebars-product-b.js'),
        versions: {
          current: {
            label: 'Next',
            path: 'next',
          },
          '1.0.0': {
            label: '1.0.0',
            path: '1.0.0',
          },
        },
      },
    ],
  ],
};
```

## 5. Performance Optimization

### Code Splitting and Lazy Loading

Use dynamic imports for large components:

```jsx
import React, { Suspense, lazy } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function MyComponent() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### Image Optimization

Use the `@docusaurus/plugin-ideal-image` for automatic image optimization:

```javascript
module.exports = {
  // ...
  plugins: [
    [
      '@docusaurus/plugin-ideal-image',
      {
        quality: 70,
        max: 1030,
        min: 640,
        steps: 2,
        disableInDev: false,
      },
    ],
  ],
};
```

Then use it in your components:

```jsx
import Image from '@theme/IdealImage';

<Image img={require('./image.png')} />
```

## 6. Advanced Search Implementations

### Algolia DocSearch Configuration

For a more customized Algolia DocSearch setup:

```javascript
module.exports = {
  // ...
  themeConfig: {
    algolia: {
      appId: 'YOUR_APP_ID',
      apiKey: 'YOUR_SEARCH_API_KEY',
      indexName: 'YOUR_INDEX_NAME',
      contextualSearch: true,
      searchParameters: {
        facetFilters: ['language:en', 'version:current'],
      },
      searchPagePath: 'search',
      externalUrlRegex: 'external\\.com|domain\\.com',
    },
  },
};
```

### Custom Search Implementation

For a fully custom search solution, create a new theme component:

```jsx
// src/theme/SearchBar/index.js
import React, { useState, useCallback } from 'react';
import { useHistory } from '@docusaurus/router';
import useDocusaurusContext from '@docusaurus/useDocusaurusContext';

export default function SearchBar() {
  const [searchQuery, setSearchQuery] = useState('');
  const history = useHistory();
  const { siteConfig } = useDocusaurusContext();

  const handleSubmit = useCallback((e) => {
    e.preventDefault();
    history.push(`${siteConfig.baseUrl}search?q=${encodeURIComponent(searchQuery)}`);
  }, [searchQuery, history, siteConfig.baseUrl]);

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Search..."
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
      />
      <button type="submit">Search</button>
    </form>
  );
}
```

## 7. Internationalization at Scale

For large-scale i18n, use a translation management system like Crowdin. Here's an example workflow:

1. Set up your Crowdin project and configure your `crowdin.yml`:

```yaml
project_id: "your-project-id"
api_token: "your-api-token"
base_path: "."
base_url: "https://api.crowdin.com"

files:
  - source: /docs/**/*.md
    translation: /i18n/%two_letters_code%/docusaurus-plugin-content-docs/current/**/%original_file_name%
  - source: /blog/**/*.md
    translation: /i18n/%two_letters_code%/docusaurus-plugin-content-blog/**/%original_file_name%
```

2. Use the Crowdin CLI to upload source files and download translations:

```bash
crowdin upload sources
crowdin download
```

3. Implement a CI/CD pipeline to automate this process.

## 8. Continuous Integration and Deployment

Set up a GitHub Actions workflow for CI/CD:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - name: Install dependencies
        run: yarn install --frozen-lockfile
      - name: Build
        run: yarn build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

## 9. Headless CMS Integration

Integrate a headless CMS like Contentful for dynamic content:

1. Install the Contentful SDK:

```bash
yarn add contentful
```

2. Create a custom plugin to fetch data from Contentful:

```javascript
// plugins/contentful-plugin/index.js
const contentful = require('contentful');

module.exports = function (context, options) {
  return {
    name: 'contentful-plugin',
    async loadContent() {
      const client = contentful.createClient({
        space: options.spaceId,
        accessToken: options.accessToken,
      });
      const entries = await client.getEntries({
        content_type: 'blogPost',
      });
      return entries.items;
    },
    async contentLoaded({ content, actions }) {
      const { createData, addRoute } = actions;
      const blogPosts = content.map((post) => ({
        id: post.sys.id,
        title: post.fields.title,
        content: post.fields.content,
      }));
      await createData('contentful-blog-posts.json', JSON.stringify(blogPosts));
      addRoute({
        path: '/blog',
        component: '@site/src/components/BlogPostList',
        modules: {
          blogPosts: 'contentful-blog-posts.json',
        },
        exact: true,
      });
    },
  };
};
```

3. Add the plugin to your `docusaurus.config.js`:

```javascript
module.exports = {
  // ...
  plugins: [
    [
      path.resolve(__dirname, 'plugins/contentful-plugin'),
      {
        spaceId: 'your-space-id',
        accessToken: 'your-access-token',
      },
    ],
  ],
};
```

## 10. API Documentation Generation

Automatically generate API documentation from OpenAPI/Swagger specifications:

1. Install the required packages:

```bash
yarn add docusaurus-plugin-openapi-docs docusaurus-theme-openapi-docs
```

2. Configure the plugin in `docusaurus.config.js`:

```javascript
module.exports = {
  // ...
  plugins: [
    [
      'docusaurus-plugin-openapi-docs',
      {
        id: 'apiDocs',
        docsPluginId: 'classic',
        config: {
          petstore: {
            specPath: 'examples/petstore.yaml',
            outputDir: 'docs/petstore',
            sidebarOptions: {
              groupPathsBy: 'tag',
            },
          },
        },
      },
    ],
  ],
  themes: ['docusaurus-theme-openapi-docs'],
};
```

3. Generate the API documentation:

```bash
yarn docusaurus gen-api-docs all
```

This extended tutorial covers advanced topics and real-world scenarios for Docusaurus projects. It includes complex project structures, custom plugin development, advanced theming, handling multiple products and versions, performance optimization, advanced search implementations, large-scale internationalization, CI/CD setup, headless CMS integration, and API documentation generation.

Is there any specific area you'd like me to elaborate on further or any additional advanced topics you'd like me to cover?