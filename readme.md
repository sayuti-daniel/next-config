# Next.js + Config

Universal runtime configuration in Next.js project using [node-config](https://github.com/lorenwest/node-config)

## Installation

```bash
npm install --save config next-config
```

or

```bash
yarn add config next-config
```

## Usage

Create a `next.config.js` in your project

```js
// next.config.js
const withConfig = require("next-config");
module.exports = withConfig();
```

Create a custom document `pages/_document.js`

```js
import Document, { Head, Main, NextScript } from "next/document";
import htmlescape from "next/dist/server/htmlescape";
import config from "config";

const __NEXT_CONFIG__ = { ...config };
// exclude server config
delete __NEXT_CONFIG__.server;

export default class extends Document {
  render() {
    return (
      <html>
        <Head />
        <body>
          <Main />
          <script
            id="__NEXT_CONFIG__"
            type="application/json"
            nonce={this.props.nonce}
            crossOrigin={this.props.crossOrigin || process.crossOrigin}
            dangerouslySetInnerHTML={{
              __html: htmlescape(__NEXT_CONFIG__)
            }}
          />
          <NextScript />
        </body>
      </html>
    );
  }
}
```

Create a config file `config/default.json`

```json
{
  "universalConfigA": "UNIVERSAL_CONFIG_A",
  "universalConfigB": "UNIVERSAL_CONFIG_B",
  "server": {
    "secretA": "SECRET_A",
    "secretB": "SECRET_B"
  }
}
```

Create a page file `pages/index.js`

```js
import config from "config";

const IndexPage = () => (
  <div>
    Hello World!<br />
    ${config.get("universalConfigA")}
    <br />
    ${config.get("universalConfigB")}
    <br />
  </div>
);

IndexPage.getInitialProps = () => {
  console.log(config.get("server.secretA"));
  console.log(config.get("server.secretB"));
  return {};
};

export default IndexPage;
```

### Configuring Next.js

Optionally you can add your custom Next.js configuration as parameter

```js
// next.config.js
const withConfig = require("next-config");
module.exports = withConfig({
  webpack(config, options) {
    return config;
  }
});
```
