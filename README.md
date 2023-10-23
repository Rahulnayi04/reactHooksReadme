# The Ultimate Playground for Mastering React's Cutting-Edge Hooks! 🚀

This project serves as a comprehensive introduction to the new hooks introduced in React 18.3 and some of other important hooks. Each section below will provide a detailed explanation and example code for the following hooks, And as you can see i have explained this by comparing between the scenario of using hooks and without using hooks, try the demo you are likely to get the idea how things are working under the hood:

- `useTransition`
- `useDeferredValue`
- `useLayoutEffect`
- `useImperativeHandle`
- `useId`
- `useDebugValue`

# useTransition

Understand the power of the `useTransition` hook in, highlighting its impact on responsiveness and performance compared to a code without it.

## Without `useTransition`

The component `WithoutuseTransition` demonstrates a scenario where React renders after both states (`input` and `lists`) are updated in the `HandleChange` function. This can potentially lead to reduced responsiveness.

```jsx
import { useState } from "react"

const WithoutuseTransition = () => {
    const [input,setInput] = useState('');
    const [lists,setLists] = useState([]); 
    const list_size = 10000;
    
    const HandleChange = (e) => {
        setInput(e.target.value);
        const l = [];
            for(let i=0;i<list_size;i++){
                l.push(e.target.value)
            }
            setLists(l);
       
    }
    return ( 
        <>
        <input value={input} onChange={HandleChange} placeholder="Search here..."/>
        {lists.map((item,index) => {
            return <div key={index}>{input}</div>
        })}
        </>
    )
}

export default WithoutuseTransition;
```
## Using `useTransition`

The component `WithoutuseTransition` demonstrates a scenario where React renders after both individual states (`input` and `lists`) updates in the `HandleChange` function. This will aids in responsiveness to the user and does not make it slow and sluggish. 

```jsx
import { useState,useTransition } from "react"

const Transition = () => {
    const [input,setInput] = useState('');
    const [lists,setLists] = useState([]);
    const [isPending, startTransition] = useTransition()
    const list_size = 10000;
    
    const HandleChange = (e) => {
        setInput(e.target.value);
        startTransition(()=>{
            const l = [];
            for(let i=0;i<list_size;i++){
                l.push(e.target.value)
            }
            setLists(l);
        })
    }
    return ( 
        <>
        <input value={input} onChange={HandleChange} placeholder="Search here..."/>
        {isPending ? <div>Loading...</div> : lists.map((item,index)=> {
            return <div key={index}>{input}</div>
        })}
        </>
    )
}

export default Transition;
```

# useDeferredValue

This is one of the most important hook helps to deal with concurrency and slow rendering. 
Before knowing this hook, let's understand what happenes if we don't use this hook? in Below code we have a very slow code in useMemo and input is changing on every key-stroke. As the code inside useMemo is expensive and being called on input change, if we do not make this input deferred then it will run as very slow and sluggish to the user. And so the user experience will be compromised.

## Without `useDeferredValue`

```jsx
const DeferredValue = () => {
    const [input, setInput] = useState('');
    function handleChange(e){
        setInput(e.target.value)
    }
    return (
        <>
        <input placeholder='Search here...' value={input} onChange={handleChange}></input>
        
        <List input={input} />
        </>
    )
}
//List.jsx
function List({input}) {
    const deferredInput = useDeferredValue(input)
    const list_size = 15000;
    const list = useMemo(()=>{
        const l = []
        for(let i =0;i<list_size;i++){
            l.push(<div key={i}>{input}</div>)
        }
        return l
    },[input])

    useEffect(()=>{
        console.log(`input ${input}`);
        console.log(`deferred input ${deferredInput}`);
    },[input,deferredInput])
    return list;
}
```

## Using `useDeferredValue`

The `useDeferredValue` hook allows us to fix this slow render problem by implementing a delay before some information is calculated. This works in a very similar way to `debouncing` and `throttling` since our deferred value will only be calculated after the important state updates have finished running. If you are unfamiliar with debouncing/throttling, please get the brief idea on it by clicking on it.

<br/>

[debouncing Exlained]: https://www.freecodecamp.org/news/javascript-debounce-example/

```jsx
import { useState } from "react";
const DeferredValue = () => {
    const [input, setInput] = useState('');
    function handleChange(e){
        setInput(e.target.value)
    }
    return (
        <>
        <input placeholder='Search here...' value={input} onChange={handleChange}></input>
        <List input={input} />
        </>
    )
}
//List.jsx
function List({input}) {
    const deferredInput = useDeferredValue(input)
    const list_size = 15000;
    const list = useMemo(()=>{
        const l = []
        for(let i =0;i<list_size;i++){
            l.push(<div key={i}>{deferredInput}</div>)
        }
        return l
    },[deferredInput])

    useEffect(()=>{
        console.log(`input ${input}`);
        console.log(`deferred input ${deferredInput}`);
    },[input,deferredInput])
    return list;
}
export default DeferredValue;
```
- The purpose of using `useDeferredValue` in this example is to ensure that the large list creation (list) is delayed until after any important state updates have been processed.
deferredInput holds a value that's potentially delayed. If there are other important state updates happening at the same time, React may prioritize them over calculating deferredInput.
- When list is created using useMemo, it uses the deferred value (deferredInput) to generate the list elements. This ensures that the potentially expensive operation of generating the list only happens after any critical state updates are complete.
- Without `useDeferredValue`, React might try to calculate input immediately, potentially causing performance issues if it's a costly operation.
- By using `useDeferredValue`, you're optimizing the rendering process, especially when dealing with potentially time-consuming tasks like generating large lists. This helps keep your application responsive and smooth.
- Remember, use useDeferredValue judiciously, focusing on scenarios where it genuinely improves performance.

# useLayoutEffect

Don't counfuse yourself when it comes to `useEffect` vs `useLayoutEffect`.
## The Main Difference: 
The `useLayoutEffect` hook runs `synchronously` directly after React calculates the DOM changes but before it paints those changes to the screen. This means that `useLayoutEffect` code will delay the painting of a component since it runs synchronously before painting, while `useEffect` is `asynchronous` and will not delay the paint.

## `uselayoutEffect`
`useLayoutEffect` is used when our code directly modifies the DOM in a way that's noticeable to the user. For instance, if we need to change a DOM element's background color, it's best to use useLayoutEffect. This ensures that the DOM is updated synchronously, preventing any initial incorrect rendering that might occur with useEffect. In short, useLayoutEffect guarantees immediate, user-visible changes when manipulating the DOM.

```jsx
import { useState, useLayoutEffect } from "react";

function LayoutEffect() {
  const [color, setColor] = useState("red");

  useLayoutEffect(() => {
    console.log("Use Layout Effect executed");
    setColor("green");
    return () => {};
  }, []);

  return (
    <>
      <div style={{ padding: "2rem", backgroundColor: color }}>
        <p>Do not Flickers in case of useLayoutEffect.</p>
        <p>
          UseLayoutEffect is called somewhere between Dom calculation and the
          DOM painting to the screen. It works Synchronously.
        </p>
      </div>
    </>
  );
}

export default LayoutEffect;
```
In the provided code, `useLayoutEffect` is used to immediately change the color of the element to 'green' after it's rendered. If `useEffect` were used instead, the color change might not be immediately visible, potentially resulting in a flicker.
