# A simple Scroll Restoration in React Router (But can be modified easily)

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
const MAX_MAP_ELEMENTS = 10;
const CLEAN_TO = 5;

class ScrollStore {
  static clean() {
    if (scrollMap.size >= MAX_MAP_ELEMENTS) {
      let index = 0;
      for (const [key] of scrollMap) {
        if (index <= CLEAN_TO) {
          scrollMap.delete(key);
        }
        index++;
      }
    }
  }

  static set(key, number) {
    if (number !== 0) {
      ScrollStore.clean();
      scrollMap.set(key, number);
    }
  }

  static get(key) {
    return scrollMap.get(key);
  }
}

export default ScrollStore;

```

It makes probably no sense to store more than 5-10 sites. But if you want you can change it, or remove this check completely to store unlimited pages in the history.
