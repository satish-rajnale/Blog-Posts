#  Getting Started with TypeScript<T> in React 

  **First :** Open the terminal and run this command

```yaml
npx create-react-app my-app --template typescript
# or
yarn create react-app my-app --template typescript
```
##### You will be get a up and running react app with typescript configurations with this template.

Now you can make changes to your App.tsx file.
 > Note : in  your tsconfig.json file strict mode is set true, while it is a good practice to have it true you might get a lot errors and feel more restrict if not used proper ways around typescripting your app.

```tsx
const  App : React.FC =() => {
 // App body
}
```
Note : You must have installed a dependancy @types/node & @types/react. Check your package.json file

#### Defining a state with type
```js
 const [task, setTask] = useState<string>("");  
    // <string | null> throws error that value cannot be null; 
 const [todoList, setTodoList] = useState<Array<string>>([]);  
    // null does not work Array<string |null> it shows error that string[] cannot be null

```
```

```


`<> By Satish Rajnale </>`
