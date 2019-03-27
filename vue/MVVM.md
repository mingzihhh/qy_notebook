## MVVM

MVVM是把MVC里的Controller和MVP里的Presenter改成了ViewModel

model + View + ViewModel

View的变化会自动更新到ViewModel，ViewModel的变化也会自动同步到View上显示。

这种自动同步是因为ViewModel中的属性实现了Observer，当属性变更时都能触发对应的操作。

View是html显示页面

ViewModel是业务逻辑层（一切JS可视业务逻辑，比如表单按钮提交，自定义事件的注册和处理逻辑都在viewmodel里面负责监控两边的数据）

model数据层负责对数据的处理（比如增删改查）

