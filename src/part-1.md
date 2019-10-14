
# Getting a basic webpack setup going

---

To build static assets, most people are using webpack nowadays. We won't be
going into too much detail, just enough to get things going.

Install dependencies:
```bash
yarn add -D html-webpack-plugin webpack webpack-cli webpack-dev-server
```

---

Webpack accepts a configuration file defining how to build the application. For
this tutorial we will be serving the entire application through the
`webpack-dev-server` tool. To do this, we will need an `index.html` file to
serve. The `html-webpack-plugin` generates the file automatically based on the
entrypoint. If you need to customize things further you may have to override
the template.

---

Here is the `webpack.config.js` configuration:
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
	entry: './lib/client/main.js',
	mode: process.env.NODE_ENV || 'development',
	output: {
		path: path.resolve(__dirname, 'dist')
	},

	plugins: [
		new HtmlWebpackPlugin({
			filename: 'index.html',
			title: 'React Tutorial'
		})
	]
};
```

---

The last file need before we have a visible page is the `main.js`. We just want
to display a simple message to validate that things are wired correctly for
now. Create the file `lib/client/main.js` with the following content:
```js
document.body.append('Hello world!');
```

---

Now we can run the following command to start the development server:
```bash
yarn webpack-dev-server
```

---

## Adding React Into the Mix

---

Adding support for React is relatively simple - we just need to install a few
more dependencies and configure babel.

```bash
yarn add -D babel-loader react react-dom @babel/preset-env @babel/core @babel/preset-react
```

---

Add the following to your webpack configuration:
```patch
diff --git a/webpack.config.js b/webpack.config.js
index a25cb78..b26f11b 100644
--- a/webpack.config.js
+++ b/webpack.config.js
@@ -7,6 +7,20 @@ module.exports = {
 	output: {
 		path: path.resolve(__dirname, 'dist')
 	},
+	module: {
+		rules: [
+			{
+				test: /.js$/,
+				exclude: /node_modules/,
+				use: {
+					loader: 'babel-loader',
+					options: {
+						presets: ['@babel/preset-env', '@babel/preset-react']
+					}
+				}
+			}
+		]
+	},
 	plugins: [
 		new HtmlWebpackPlugin({
 			filename: 'index.html',

```

---

If you're already using babel in your project (along with webpack), all you
need to add is `@babel/preset-react` and load the preset in the
`webpack.config.js` file.

---

## Testing It Out

---

Replace the content of your `main.js` with the following:
```jsx
import React from 'react';
import ReactDom from 'react-dom';

// Since the html page we're using is generated, create
// a root element to render against.
const root = document.createElement('div');
root.id = 'root';
document.body.appendChild(root);

ReactDom.render(
	<p>Hello world!</p>,
	root
);
```

---
