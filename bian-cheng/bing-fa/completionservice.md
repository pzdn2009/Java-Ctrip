# CompletionService

`CompletionService`实际上可以看做是`Executor`和`BlockingQueue`的结合体。

`CompletionService`在接收到要执行的任务时，通过类似`BlockingQueue`的put和take获得任务执行的结果。

`CompletionService`的一个实现是`ExecutorCompletionService`，`ExecutorCompletionService`把具体的计算任务交给Executor完成。

