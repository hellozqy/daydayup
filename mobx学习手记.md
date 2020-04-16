# [mobx学习手记]

## 1.class语法

## 2.装饰器语法

 [@observable](http://cn.mobx.js.org/refguide/observable-decorator.html)

类修饰器

类属性修饰器

类方法修饰器

## 3.可观察数据observable

computed计算可观察数据，autorun在可观察数据发生改变时自动触发，when提供条件执行数据，reaction提供选择性执行数据

import { observable, isArrayLike, computed, autorun, when ,reaction} from 'mobx'



// 复杂数据类型array object map处理，会生成一个类似的类型但可以应用原类型但各种方法，但不是原类型

const arr = observable(['a', 'b', 'c'])
console.log(arr, Array.isArray(arr), isArrayLike(arr))
// 越界访问数组的成员将会引起mobx提示越界成员访问不会被mobx所监视

const obj = observable({ a: 1, b: 2 })
console.log(obj, obj.a, obj.b)
const map = observable(new Map())
console.log(map, map.has('a'))

var num = observable.box(20)
var str = observable.box('hello')
var bool = observable.box(true)
console(num, str, bool, num.get(), num.get(), bool.get())
num.set(30)
str.set('world')
bool.set(false)
console(num, str, bool, num.get(), num.get(), bool.get())
// 用作装饰器语法时observable会自动识别所装饰但变量的类型而不需要使用observable.box

class Todo {
id = Math.random()
title = ''
finished = false
}
decorate(Todo, {
title: observable,
finished: observable
})

// computed values计算值
// 将其他可观察数据通过计算成另一个可观察属性，或对其他可观察数据作出反应
// 可以嵌套引用其他computed但是不能循环引用

class TodoList {
@observable todos = []
@computed get unfinishedTodoCount () {
return this.todos.filter(todo => !todo.finished).length
}
}

// Autorun自动运行,在可观察数据被修改之后自动执行依赖可观察数据对行为,
var numbers = observable([1, 2, 3])
var sum = computed(() => numbers.reduce((a, b) => a + b, 0))

var disposer = autorun(() => console.log(sum.get()))
// 输出 '6'
numbers.push(4)
// 输出 '10'

disposer()
numbers.push(5)
// 不会再输出任何值。`sum` 不会再重新计算。
// 可以传入第二个参数delay:100防抖
// onError以此来用另一种方式覆盖控制台报错现象如
// onError(e) {
// window.alert("Please enter a valid age")
// }

//when when(predicate: () => boolean, effect?: () => void, options?)
// 传入两个函数类型的参数，第一个参数提供判断条件，执行第二个函数

class MyResource {
constructor() {
when(
// 一旦...
() => !this.isVisible,
// ... 然后
() => this.dispose()
);
}

@computed get isVisible() {
// 标识此项是否可见
}

dispose() {
// 清理
}
}

// reaction reaction(() => data, (data, reaction) => { sideEffect }, options?)。
// 传入两个函数类型的参数，第一个函数返回数据给第二个函数作为其参数，然后执行第二个函数
// 若第一个函数无返回值或者返回一个null值则不执行第二个函数，
// 有第三个参数

//computed计算可观察数据，autorun在可观察数据发生改变时自动触发，when提供条件执行数据，reaction提供选择性执行数据

## 4.mobx的应用