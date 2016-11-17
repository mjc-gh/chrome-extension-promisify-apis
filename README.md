First, we'll need a small function to wrap all the callback based API
methods with. This function will return a promise and will check for
Chrome runtime errors.

```javascript
const asPromised = (block) => {
    return new Promise((resolve, reject) => {
        block((...results) => {
            if (chrome.runtime.lastError) {
                reject(chrome.extension.lastError);
            } else {
                resolve(...results);
            }
        });
    });
};
```

Now we can freely wrap any callback based API with our `asPromised`
function!

```javascript
const storage = {
    set(items){
        return asPromised((callback) => {
            chrome.storage.local.set(items, callback);
        });
    },

    get(keys) {
        return asPromised((callback) => {
            chrome.storage.local.get(keys, callback);
        });
    },

    remove(keys) {
        return asPromised((callback) => {
            chrome.storage.local.remove(keys, callback);
        });
    }
};
```

Now we have a promise based storage API wrapper that we can use like
such:

```javascript
// This will log:
//   Object {key: "value"}
storage.set({ key: 'value' }).then(() => storage.get('key')).then(console.log)
```
