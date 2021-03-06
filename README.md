# saga-axios
החבילה הזו נועדה לספק ממשק פשוט בשביל לשרשר בקשות שאחרי ולפני הבקשות יהיה אפשר להכניס פונקציות, עוד בקשות וחוזר 
חלילה.
this package ment to simplify the creating of complex web applications. 
this package provide simple interface to chain request with function's or request's

הוראות התקנה
## Quick use
<!-- 1. צריך לקבל `takeEverySendToProcess` לתוך הסאגא ואצא' -->
1. import `takeEverySendToProcess` from `saga-axios` into `sagaMiddleware.run()`: 
```
function* rootSaga() {
  yield all([fork(takeEverySendToProcess)]);
}
sagaMiddleware.run(rootSaga)
```

2. צריך לקחת `sendToProcess` מcore
2. import `sendToProcess` from `saga-axios`
3. use inside `dispatch`
    ```
    import { sendToProcessAction } from 'saga-axios/core';

    const App = () => {
        const dispatch = useDispatch()
        const sendReq = () => {
        dispatch(
            sendToProcessAction({
            actionsBefore: [],
            url: url,
            method: // ["post" , "get" , "put" , "patch" , "delete],
            payload : // any object to send over http,
            options: {
                withToken = true,
                contentType = "application/json",
                config = {
                    \\destructor to axios config (e.g. axios.get(url , config) | axios.post(url , data, config))
                },
            },
            actions: [],
            }));
        }
        return <button onClick={sendReq}>SEND</button>
        
    }
    ```

4. שרשור פונקציות
אפשר לשרשר פונציה עם `wrapAction`  

```
    dispatch(
        sendToProcessAction({
          actionsBefore: [
            wrapAction({
              on: "ANY",
              func: () => {
                do_something()
              },
            }),
          ],
          url: url,
          method,
          payload,
          options: {},
          actions: [
            wrapAction({
                \\run when the request send successfuly    
              on: "SUCCESS",
              func: () => {
                    do_something()
              },
            }),
            wrapAction({
                \\run when the request send fail
              on: "FAIL",
              payload: "data.user.name"
              func: (name) => {
                do_something_with_user_name(name)
              },
            }),
            { // call to another api call with action, beforeAction...
            \\run when the request send successfuly
              on: "SUCCESS",
              payload: "data",
              func: (data) => {
                dispatch(sendToProcessAction({
                    same_attr_etc..
                }));
              },
            },
          ],
        })
      );
```