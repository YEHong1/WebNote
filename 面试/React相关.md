## 1.将字符串转义为html并渲染

```react
<div dangerouslySetInnerHTML={{__html: "字符串内容"}} />
// __html 两个下划线
```



## 2.setState是异步的还是同步的

-   不可变值 (不可以通过this.state.count来改变count的值)
-   可能是异步的
-   可能会被合并

```react
// 在一般情况下，setState时异步的
// 点击前count值为0
handleClick = ()=>{
    this.setState({
        count: this.state.count + 1
    }, ()=>{
        console.log('callback', this.state.count) // count为1
    });
    console.log(this.state.count); // count为0，此处setState为异步
};


// 在setTimeout中， setState时同步的
handleClick = ()=>{
    setTimeout(()=>{
        this.setState({
            count: this.state.count + 1
        });
        console.log(this.state.count); // count值为1， 此处setState为同步
    }, 0)
};


// 自己定义的Dom事件中，setState是同步的
componentDidMount() {
    document.addEventListener('click', ()=>{
        this.setState({
            count: this.state.count + 1
        });
        console.log(this.state.count) // count值为1， 此处setState为同步
    })
}



// 传入对象，会被合并。下面的结果为 count + 1
// 因为setState在这种情况下为异步， 所以在第一次setState后，
// this.staet.count还是+1之前的值，后面执行多少次结果都一样
this.setState({
    count: this.setState.count + 1
})

this.setState({
    count: this.setState.count + 1
})

this.setState({
    count: this.setState.count + 1
})


// 传入函数不会被合并, 下面的结果为count + 3
this.setState((preState, props)=>{
    return {
        count: prestate.count + 1
    }
})

this.setState((preState, props)=>{
    return {
        count: prestate.count + 1
    }
})

this.setState((preState, props)=>{
    return {
        count: prestate.count + 1
    }
})
```

