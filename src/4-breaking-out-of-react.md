# Breaking Out of React

---

Sometimes we need to break out of react. Either because there's some browser
API we need to call directly, have to use a legacy libary built using jquery,
or something else.

---

## Effects

---

Effects allow you to hook into the lifecycle of a component. More specifically,
it allows to listen for two types of events:

- `mount`: The underlying DOM element has been rendered.
- `unmount`: The underlying DOM element has been removed.
- `update`: The underlying DOM element has been updated.

---

Suppose we want to use code outside of react implementing a publish/subscribe
pattern:
```js
import {EventEmitter} from 'events';

const countEmitter = new EventEmitter();

let count = 0;
setInterval(() => {
	countEmitter.emit('count', count++);
}, 1000);
```

---

The first way we might implement it is like this.
```js
function Counter() {
	const [count, setCount] = useState(0);
	const onClick = () => setCount(count + 1);
	countEmitter.on('count', (count) => {
		setCount(count);
	});

	return <p>Count: {count}</p>;
}

const html = [
	<Counter/>
];
```

---

After running for a little while however, you will start to see a memory leak
problem...

---

### Solution

The `useEffect` accepts the following parameters:
- The first parameter is a function which will be called whenever the effect
should be applied. The function can also optionally return a cleanup function
which will be called on every unmount or re-render.
- The second parameter is optional. It tells react to only run your effect if
the value has changed since the last render.
- Doesn't return anything.

---

```js
import {useEffect} from 'react';

function Counter() {
	const [count, setCount] = useState(0);
	const onClick = () => setCount(count + 1);

	useEffect(() => {
		const onCount = (count) => {
			setCount(count);
		};
		countEmitter.on('count', onCount);
		return () => countEmitter.off('count', onCount);
	});

	return <p>Count: {count}</p>;
}
```

---

### Another Example

```js
import { useState, useEffect } from 'react';

function App() {
	const [page, setPage] = useState(1);
	const [posts, setPosts] = useState([]);

	useEffect(() => {
		fetch('https://jsonplaceholder.typicode.com/posts?_page=' + page)
			.then(response => response.json())
			.then(body => setPosts(body));
	});

	return [
		<p>
			<a onClick={() => setPage(page - 1)}>Previous</a>
			-
			<a onClick={() => setPage(page + 1)}>Next</a>
		</p>,
		<ul>
			{posts.map(post => <li key={post.id}>{post.title}</li>)}
		</ul>
	];
}

const html = <App/>;
```

---

All we need to do is specify the option parameter for `useEffect`. This will
tell react to only run our effect when the value changes.

```patch
diff --git a/lib/client/main.js b/lib/client/main.js
index 66efb8a..44ad91f 100644
--- a/lib/client/main.js
+++ b/lib/client/main.js
@@ -16,7 +16,7 @@ function App() {
 		fetch('https://jsonplaceholder.typicode.com/posts?_page=' + page)
 			.then(response => response.json())
 			.then(body => setPosts(body));
-	});
+	}, [page]);
 
 	return [
 		<p>
```

---

## Refs

---

Refs are identical to state with the exception that mutation does not trigger
a re-render of the component.

---

Refs are useful for keeping track of things such as the underlying DOM element
of a component.

---

The `useRef` function:
- Accepts a default value.
- Returns an object with a `current` property. `current` is the current value
of the ref.

---

## Example

---

Lets say we want to write a simple canvas 2d component which renders a circle:

```js
function renderCanvas(canvas) {
	const canvasContext = canvas.getContext('2d');
	canvasContext.clearRect(0, 0, canvas.width, canvas.height);
	canvasContext.fillStyle = 'Tomato';
	canvasContext.beginPath();
	canvasContext.ellipse(100, 100, 50, 50, Math.PI * 2, 0, Math.PI * 2);
	canvasContext.fill();
}
```

---

We can gain access to the underlying html element like so:
```js
import { useRef, useEffect } from 'react';

function App() {
	const canvasRef = useRef(null);

	useEffect(() => {
		renderCanvas(canvasRef.current);
	});

	return (
		<canvas ref={canvasRef}></canvas>
	);
}

const html = <App/>;
```

---

## Another Example

Lets integrate tui-editor with React.
```bash
yarn add -D tui-editor highlight.js codemirror style-loader css-loader file-loader
```

---

To do this we will need to update our webpack configuration:
```patch
diff --git a/webpack.config.js b/webpack.config.js
index 6657b39..b9b9c19 100644
--- a/webpack.config.js
+++ b/webpack.config.js
@@ -11,6 +11,14 @@ module.exports = {
 	devtool: mode === 'development' ? 'eval-source-map' : false,
 	module: {
 		rules: [
+			{
+				test: /\.png$/i,
+				loader: 'file-loader'
+			},
+			{
+				test: /\.css$/i,
+				use: ['style-loader', 'css-loader']
+			},
 			{
 				test: /.js$/,
 				exclude: /node_modules/,
```

---

Next step is to implement a text editor component.
```js
import 'codemirror/lib/codemirror.css';
import 'tui-editor/dist/tui-editor.css';
import 'tui-editor/dist/tui-editor-contents.css';
import 'highlight.js/styles/github.css';

import Editor from 'tui-editor';
import { useEffect, useRef } from 'react';

function TuiEditor(props) {
	const editorElement = useRef(null);
	const editorInstance = useRef(null);

	useEffect(() => {
		if (!editorInstance.current) {
			editorInstance.current = new Editor({
				el: editorElement.current,
				initialEditType: 'markdown',
				usageStatistics: false,
			});
		}

		editorInstance.current.setValue(props.value);

		editorInstance.current.on('change', props.onChange);
		return () => editorInstance.current.off('change', props.onChange);
	}, [props.value, props.onChange]);

	return <div ref={editorElement}></div>;
}
```

---

And then putting it together.
```js
import { useState } from 'react';
function App() {
	const [subject, setSubject] = useState(null);
	const [body, setBody] = useState(null);

	const onSubjectChanged = (event) => {
		setSubject(event.target.value);
	};
	const onBodyChanged = (editor) => {
		setBody(editor.getValue());
	};
	const onShow = () => {
		alert(`Subject: ${subject}\nBody: ${body}`)
	};
	return [
		<div>
			<label htmlFor='subject'>Subject</label>
			<input type='text' name='subject' onChange={onSubjectChanged}/>
		</div>,
		<TuiEditor value={body} onChange={onBodyChanged}/>,
		<button onClick={onShow}>show</button>
	];
}

const html = <App/>;
```
