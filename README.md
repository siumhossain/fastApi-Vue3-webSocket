# fastApi-Vue3-webSocket

![demo:fastapi vue websocket](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ngku8zg9ca7sstjtvutf.gif)


###  Part1: FastAPI
Create virtualenv (optional)
Install FastAPI and all necessary things by-
```py
pip install fastapi uvicorn websockets
```

Create `main.py` file
 
```py
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
def root():
    return {"msg":"welcome"}

```
and Run by -
```bash
uvicorn main:app --reload 
```
Open this link in your browser `http://127.0.0.1:8000`

![fastAPI](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4vf49ll61l8130b2ydhf.png)

If you see something like this you are ready to go.

Now let's create websocket endpoint - 
```py

from fastapi import FastAPI, WebSocket

app = FastAPI()


# @app.get("/")
# def root():
#     return {"msg":"welcome"}

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:

        data = await websocket.receive_text()
        print(data)
        await websocket.send_text(f"{data}")
```

This `async` function will await until accept connection from frontend via `await websocket.accept()`. And then when connection is created, our websocket will ready to communicate with frontend until connection closed.
 
```py
 data = await websocket.receive_text()
```
By this line of code we will receive data from frontend.

```py
await websocket.send_text(f"{data}")
```
And by this line of code we are able to send data to frontend. And that's how we create two way communication.

<hr>

### Part2: Vue3 (Frontend)
let's create vue3 applicaion- [Vue3 installation guide](https://vuejs.org/guide/quick-start.html)

```bash 
npm init vue@latest
```

![vue](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c837jz4o9yash20qdqvj.png)


![vue](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l3gkme8robdeqkr6xv8m.png)

Delete all of the boilerplate code. And let's code in `app.vue` file just for make things easier.

```vue
<script setup>
import { onMounted,ref } from 'vue'

const data = ref()
const inputData = ref()
const connection = new WebSocket("ws://localhost:8000/ws")


function submit()  {
  connection.send(inputData.value)
}
onMounted(() => {
  
  connection.onmessage = function(e){
   
    data.value = e.data

  }

})

</script>

<template>
 
  <h1>hello {{data}}</h1>
  <input type="text" v-model="inputData" @keyup.enter="submit()">
  <button @click="submit()">submit</button>
  <RouterView />
</template>
```

Start building connection with fastAPI websocket by-

```js
const connection = new WebSocket("ws://localhost:8000/ws")
```

In mounted hook we start listen what message is send by backend. Abd whatever data comes from backend we can store those data and can render it in tamplate by-  

```js
onMounted(() => {
  
  connection.onmessage = function(e){
   
    data.value = e.data

  }

})
```
```vue
<!-- in template -->
<template>
 
  <h1>hello {{data}}</h1>
 
</template>
```

Let's create a input field and send data from frontend and again receive those data in frontend via backend.

```vue
<template>
 
  <h1>hello {{data}}</h1>
  <input type="text" v-model="inputData" @keyup.enter="submit()">
  <button @click="submit()">submit</button>
  <RouterView />
</template>
```

And In `submit()` function we will send data to the backend 

```js

function submit()  {
  connection.send(inputData.value)
}
```
Voila.... We are done 😎. Now you can do more experiment. Retrieve data from database and send it to frontend something like that. Or whatever you may wish.
