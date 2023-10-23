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
## Without using `useDeferredValue`
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
## using `useDeferredValue`
The `useDeferredValue` hook allows us to fix this slow render problem by implementing a delay before some information is calculated. This works in a very similar way to `debouncing` and `throttling` since our deferred value will only be calculated after the important state updates have finished running. If you are unfamiliar with debouncing/throttling, please get the brief idea on it by clicking on it.
<br/>
[debouncing Exlained]: https://www.freecodecamp.org/news/javascript-debounce-example/
