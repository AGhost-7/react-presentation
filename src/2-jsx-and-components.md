# JSX and Components

---

## JSX

---

JSX is HTML in Javascript.

---

Basic example:

```js
const html = <p>Hello world!</p>;
```

---

There should always be **one** top-level element.

Invalid:
```js
const html = <span>Hello</span> <span>world!</span>;
```

Valid:
```js
const html = <div><span>Hello</span> <span>world!</span></div>;
```

You can also make an array of html elements:
```js
const html = [<span>Hello</span>, <span>world!</span>];
```

---

Parameters are specified with brackets:
```js
const name = 'Rick';

const html = <p>Hello {name}!<p>;
```

---

Brackets also work with any kind of expression (function calls, ternary
operators, etc):

```js
const name = 'Rick';
const exclamation
const html = <p>Hello {name}{exclamation ? '!' : ''}</p>;
```

---

A more complex example:
```js
const people = [
	{ name: 'Rick Sanchez' },
	{ name: 'Morty Smith' },
	{ name: 'Bird Person' },
	{ name: 'Noob Noob' }
];

const html = (
	<ul>
		{people.map(person => <li key={person.name}>{person.name}</li>)}
	</ul>
);
```

---

You can pass elements to other elements:
```

const title = <h1>Example title</h1>;
const paragraph = <p>Example paragraph</p>;

const html = <section>{title}{paragraph}</section>;
```
---

## Components

---

A component is a function which accepts a props object and returns an element.

---

```js

function List(props) {
	return (
		<ul>
		{props.items.map(item =>
			<li key={item}>{item}</li>
		)}
		</ul>
	);
}

const items = ['One', 'Two'];

const html = <List items={items}/>;
```

---

Children are passed to the component through the `children` prop:

```js
function Red(props) {
	const style = { color: 'red' };
	return (
		<div style={style}>
			{props.children}
		</div>
	);
}

const html =
	<Red>
		<h1>Example Title</h1>
		<p>Example paragraph</p>
	</Red>;
```
