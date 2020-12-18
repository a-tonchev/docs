# A simple Scroll Restoration in React Router (But can be modified easily):

Your component:

```jsx
  const { key } = location || {};
  useLayoutEffect(() => {
    const saveScroll = () => {
      if (key) {
        ScrollStore.set(key, window.scrollY);
      }
    };
    return () => {
      saveScroll();
    };
  }, [key]);

  useEffect(() => {
    const savedScrollPosition = ScrollStore.get(key);
    if (savedScrollPosition) {
      setTimeout(() => {
        window.scrollTo(0, savedScrollPosition);
      }, 0);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);
```

We are useing useLayoutEffect because it will be unmounted earlyer than the useEffect. So we can catch the exact window.scrollY position. 

If the component is resized later the position would be 0, because the elements are already removed from the dom tree.

The scroll store:

```js
const scrollMap = new Map();

class ScrollStore {
  static set(key, number) {
    if (number !== 0) {
      if (scrollMap.size >= 5) scrollMap.clear();
      scrollMap.set(key, number);
      console.log(key, number, scrollMap, scrollMap.size);
    }
  }

  static get(key) {
    return scrollMap.get(key);
  }
}

export default ScrollStore;

```
