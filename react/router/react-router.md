## react-router

### RouteComponentProps

```
export interface RouteComponentProps<Params extends { [K in keyof Params]?: string } = {}, C extends StaticContext = StaticContext, S = H.LocationState> {
  history: H.History;
  location: H.Location<S>;
  match: match<Params>;
  staticContext?: C;
}
```

- 路由组件可以直接获取这些属性（history,location,match等），而非路由组件就必须通过withRouter修饰后才能获取这些属性了
- 目的就是让被修饰的组件可以从props属性中获取history,location,match等方法

 useHistory, useLocation, useParams