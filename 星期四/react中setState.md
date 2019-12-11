## setState(updater,[callback])方法
updater为新的state或者props，既可以是一个对象也可以是一个函数。<br>
[callback]为回调函数。

#### setState是异步的：大概可以理解为，setstate()是一个重新渲染的请求，而不是立即更新的一个命令，为了有更好的性能，React可能会推迟执行，然后会批量进行处理，React不会保证在setState操作后立即拿到最新的state值。    如果想在setState后立即得到最新的state值，可以通过函数来实现。

setState方法接受的第一个参数是对象或者函数，二者的处理方式是不一样的。<br>
接收的是函数时：React会讲函数放到一个任务队列中依次执行，所以每个函数执行后返回的都是最新的state值。
接收的是state对象时：会把state放到一个状态队列中去，中途会对多次发生的state修改进行合并，结果就是只执行依次setstate。


