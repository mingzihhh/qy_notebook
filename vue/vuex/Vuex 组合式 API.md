```
import { endsWith } from 'lodash-es';
import { getCurrentInstance, reactive } from 'vue';

export function useStore() {
  const instance = getCurrentInstance();
  return instance.proxy.$store;
}

export function getStoreModuleByNamespace(store, namespace) {
  if (!endsWith(namespace, '/')) {
    namespace += '/';
  }
  return namespace !== '/' ? store['_modulesNamespaceMap'][namespace] : store['_modules'].root;
}
function useDefaultStore() {
  const vm = getCurrentInstance();
  return vm['$store'];
}
export function normalizeUseArguments(store, namespace) {
  if (typeof store !== 'object') {
    namespace = store;
    store = useDefaultStore();
  }
  if (typeof namespace !== 'string') {
    namespace = '';
  }
  return [store, namespace];
}

export function useStoreState(arg1, arg2) {
  const [store, namespace] = normalizeUseArguments(arg1, arg2);
  const { context } = getStoreModuleByNamespace(store, namespace);
  // `readonly` will not create reactive object in `@vue/composition-api`
  return reactive(context.state);
}

export function useStoreGetters(arg1, arg2) {
  const [store, namespace] = normalizeUseArguments(arg1, arg2);
  const { context } = getStoreModuleByNamespace(store, namespace);
  return reactive(context.getters);
}

export function useStoreMutations(arg1, arg2) {
  const [store, namespace] = normalizeUseArguments(arg1, arg2
  const { context, _rawModule } = getStoreModuleByNamespace(store, namespace);
  return Object.fromEntries(
    Object.keys(_rawModule.mutations).map((key) => [key, context.commit.bind(store, key)]),
  );
}

export function useStoreActions(arg1, arg2) {
  const [store, namespace] = normalizeUseArguments(arg1, arg2);
  const { context, _rawModule } = getStoreModuleByNamespace(store, namespace);
  return Object.fromEntries(
    Object.keys(_rawModule.actions).map((key) => [key, context.dispatch.bind(store, key)]),
  );
}
```

