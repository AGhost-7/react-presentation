# State and Context

---

## State

---

State in react enables us to make our applications interactive.

---

The `useState` function:
- "remembers" what state was at from last time you rendered.
- Accepts a starting value.
- Returns an array containing two items:
	- The first item is the current value of your state.
	- The second item is a function which you can call to update your state. When
	you call this function, it will cause your component to re-render.

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

---

## Context

---

Context is global state. Example use cases are:
- Keeping track of the currently logged in user
- Translations
- Themes

---

The `createContext` function:
- Accepts one parameter which is the default value of the context.
- Returns a context identifier that you can use to get and set the context's
value.

---

The `useContext` function:
- Accepts one parameter which is the context identifier returned by the
`createContext` function.
- Returns the current value of the context.

---

## Example

---

```js
import { createContext } from 'react';

const english = {
	'first_name': 'First Name',
	'last_name': 'Last Name',
	'submit': 'Submit'
};

const french = {
	'first_name': 'Prenom',
	'last_name': 'Nom de famille',
	'submit': 'Soumettre'
};

const TranslationContext = React.createContext(english);
```

---

```js
import { useContext } from 'react';

function LoginForm() {
	const translations = useContext(TranslationContext);
	return (
		<form>
				<label htmlFor="firstName">{translations['first_name']}</label>
				<input type="text" name="firstName"/>

				<label htmlFor="lastName">{translations['last_name']}</label>
				<input type="text" name="lastName"/>

			<button>{translations['submit']}</button>
		</form>
	);
}
```

---

```js
import { useState } from 'react';

function App() {
	const [language, setLanguage] = useState('english');
	const translations = language === 'english' ? english : french;
	const onChange = (event) => {
		setLanguage(event.target.value);
	};
	return [
		<select onChange={onChange}>
			<option key='english' value='english'>English</option>
			<option key='french' value='french'>Francais</option>
		</select>,
		<TranslationContext.Provider value={translations}>
			<LoginForm/>
		</TranslationContext.Provider>
	];
}

const html = <App/>;
```
