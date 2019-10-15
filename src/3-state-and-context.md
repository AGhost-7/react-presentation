# State and Context (Global State)

---

State in react enables us to make our applications interactive.

---

The `useState` function:
- "remembers" what state was at from last time you rendered.
- Accepts a starting value.
- Returns an array containing two items:
	- The first item is the current value of your state.
	- The second item is a function which you can call to update your state.

---

## Example

---

```js
import { useState } from 'react';

function Counter() {
	const [count, setCount] = useState(0);
	const onClick = () => setCount(count + 1);

	return [
		<p>Times clicked {count}</p>,
		<button onClick={onClick}>Click me</button>
	];
}

const html = <Counter/>;
```

---

Another example:

```js
import { useState } from 'react';

function Greeter() {
	const [name, setName] = useState(null);
	const onChange = (event) => {
		setName(event.target.value);
	}
	return (
		<div>
			{name ? <p>Hello {name}!</p> : ''}
			<input type='text' onChange={onChange}/>
		</div>
	);
}
const html = <Greeter/>;
```

---

## Questions?
