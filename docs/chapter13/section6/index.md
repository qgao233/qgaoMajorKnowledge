# 使用函数式组件替代类组件

类组件中相较于函数组件比较特殊的功能，如state,生命周期等可以使用钩子函数hooks替代。
>类组件中不能使用钩子函数

## 函数组件中的钩子函数
https://zh-hans.reactjs.org/docs/hooks-overview.html
https://blog.csdn.net/landl_ww/article/details/102158814

### 1. useState

useState替换state, 见坑
```
const [msg, setMsg] = useState('');  
```

### 2. useRef

* uesRef 返回的对象将在组件的整个生命周期内保持。
* 2个作用：
   1.	获取子组件的实例(只有子组件是类组件时可用)【访问DOM节点，ref.current 即可访问到组件实例】
   2.	在函数组件中的一个全局变量，不会因为重复 render 重复申明， 类似于类组件的 this.xxx【通用容器，其 current 属性是可变的，可以保存任何值】

例子：

* 3秒内先点击增加button，后点击减少button，3秒后先alert 1，后alert 0【capture value特性】，
* 而不是alert两次0【预想结果】。

```
function App() {  
  const [count, setCount] = useState(0);  
  
  useEffect(() => {  
    setTimeout(() => {  
      alert("count: " + count);  
    }, 3000);  
  }, [count]);  
  
  return (  
    <div>  
      <p>You clicked {count} times</p>  
      <button onClick={() => setCount(count + 1)}>增加 count</button>  
      <button onClick={() => setCount(count - 1)}>减少 count</button>  
    </div>  
  );  
}  
```
useRef 创建一个引用，就可以有效规避 React Hooks 中 Capture Value 特性，总会获取到最新的值

### 3. useImperativeHandle

可以让你在使用 ref 时自定义暴露给父组件的实例值，说简单点就是，子组件可以选择性的暴露给副组件一些方法，这样可以隐藏一些私有方法和属性，官方建议，useImperativeHandle应当与 forwardRef 一起使用，

同时也解决了useRef只能获得子组件是类组件的缺点。

```
function Kun (props, ref) {  
  const kun = useRef()  
  
  const introduce = useCallback (() => {  
    console.log('i can sing, jump, rap, play basketball')  
  }, [])  
  useImperativeHandle(ref, () => ({  
    introduce: () => {  
      introduce()  
    }  
  }));  
  
  return (  
    <div ref={kun}> { props.count }</div>  
  )  
}  
  
const KunKun = forwardRef(Kun)  
  
function App () {  
  const [ count, setCount ] = useState(0)  
  const kunRef = useRef(null)  
  
  const onClick = useCallback (() => {  
    setCount(count => count + 1)  
    kunRef.current.introduce()  
  }, [])  
  return (  
    <div>  
      点击次数: { count }  
      <KunKun ref={kunRef}  count={count}></KunKun>  
      <button onClick={onClick}>点我</button>  
    </div>  
    )  
}  
```
### 4. useEffect

useEffect替换生命周期函数，如下所示。

* 什么都不传，组件每次 render 之后 useEffect 都会调用，相当于 componentDidMount 和 componentDidUpdate
* 传入一个空数组 [], 只会调用一次，相当于 componentDidMount 和 componentWillUnmount
* 传入一个数组，其中包括变量，只有这些变量变动时，useEffect 才会执行

```
const [ count, setCount ] = useState(0)  
useEffect(() => {  
    // 相当于 componentDidMount  
    console.log('add resize event')  
    window.addEventListener('resize', onChange, false)  

    return () => {  
        // 相当于 componentWillUnmount  
        window.removeEventListener('resize', onChange, false)  
    }  
}, [])  
  
useEffect(() => {  
    // 相当于 componentDidUpdate  
    document.title = count  
})  

useEffect(() => {  
    console.log(`count change: count is ${count}`)  
}, [ count ]) 
```

### 5. useMemo
useMemo 会在渲染的时候执行，而不是渲染之后执行，这一点和 useEffect 有区别，所以 useMemo 不建议有 副作用相关的逻辑

同时，useMemo 可以作为性能优化的手段，但不要把它当成语义上的保证，将来，React 可能会选择“遗忘”以前的一些 memoized 值，并在下次渲染时重新计算它们。

### 6. useCallBack
useMemo 的语法糖，能用 useCallback 实现的，都可以使用 useMemo,

向子组件传递函数props时，每次 render 都会创建新函数，导致子组件不必要的渲染，浪费性能，这个时候，就是 useCallback 的用武之地了，useCallback 可以保证，无论 render 多少次，我们的函数都是同一个函数，减小不断创建的开销，

### 7. 自定义hook
假设我们有诸多组件都需要这个逻辑，那么我们只需要将其抽取成一个自定义 hook 即可.

获取屏幕宽度变化的例子
```
function useWidth (defaultWidth) {  
  const [width, setWidth] = useState(document.body.clientWidth)  
  
  const onChange = useCallback (() => {  
    setWidth(document.body.clientWidth)  
  }, [])  
  
  useEffect(() => {  
    window.addEventListener('resize', onChange, false)  
  
    return () => {  
      window.removeEventListener('resize', onChange, false)  
    }  
  }, [onChange])  
  
  return width  
}  
  
function App () {  
  
  const width = useWidth(document.body.clientWidth)  
  
  return (  
    <div>   
      页面宽度: { width }  
    </div>  
    )  
}  
```