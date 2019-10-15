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

After running for a little while however, you will start to see a leaking
problem...

---

### Solution

The `useEffect` accepts the following parameters:
- The first parameter is a function which will be called whenever the effect
should be applied. The function can also optionally return a cleanup function
which will be called on every unmount or re-render.
- The second parameter is optional.

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
