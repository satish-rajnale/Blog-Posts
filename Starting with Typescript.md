# Getting Started with TypeScript<T> in React

**First :** Open the terminal and run this command

```yaml
npx create-react-app my-app --template typescript
# or
yarn create react-app my-app --template typescript

```

##### You will be get a up and running react app with typescript configurations with this template.

Now you can make changes to your App.tsx file.

> Note : in your tsconfig.json file strict mode is set true, while it is a good practice to have it true you might get a lot errors and feel more restrict if not used proper ways around typescripting your app.

```tsx
const App: React.FC = () => {
  // App body
};
```

Note : You must have installed a dependancy @types/node & @types/react. Check your package.json file

#### Defining a state with type

```js
const [task, setTask] = useState < string > '';
// <string | null> throws error that value cannot be null;
const [todoList, setTodoList] = useState < Array < string >> [];
// null does not work Array<string |null> it shows error that string[] cannot be null
```

Input and onchange handler

```js
<input type="text" value={task} onChange={handleChange} />
```

```tsx
const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
  // you have to specify what event type
  setTask(event.target.value);
};
```

So apparently typescript needs the event type also which is provided by our browser (Js) events and React extends those events & puts them in the `React.syntheticEvent` variable. Typescript wants the event provided by React.

Lets create a useReducer component with typescript and react.
1.Defining our action types & initial state

```tsx
import { useReducer } from 'react';

const initialState = {
  counter: 100,
};

type ACTIONTYPES =
  | { type: 'increment'; payload: number }
  | { type: 'decrement'; payload: number };
```

2.Count reducer

```tsx
function counterReducer(state: typeof initialState, action: ACTIONTYPES) {
  switch (action.type) {
    case 'increment':
      return {
        ...state,
        counter: state.counter + action.payload,
      };
    case 'decrement':
      return {
        ...state,
        counter: state.counter - action.payload,
      };
    default:
      throw new Error('Bad action');
  }
}
```

3. UseReducer component

```tsx
export default function UseReducerComponent() {
  const [state, dispatch] = useReducer(counterReducer, initialState);
  return (
    <div>
      <div>{state.counter}</div>
      <div>
        <button onClick={() => dispatch({ type: 'increment', payload: 56 })}>
          Increment
        </button>
        <button onClick={() => dispatch({ type: 'decrement', payload: 26 })}>
          decrement
        </button>
      </div>
    </div>
  );
}
```

And that is how you create a simple reusable type based component in react.
`<> By Satish Rajnale </>`
