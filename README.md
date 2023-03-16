Step 1: Create a new empty branch
Step 2: npx create-mf-app
Name: host
Port: 8080
Application
Solid js
Javascript
Tailwind

Step 3: cd host
Step 4: npm i
Step 5: npm start

Step 6: npx create-mf-app
Name: remote
Port: 3000
Application
Solid js
Javascript
Tailwind

Step 7: npm start

Step 8: In remote src folder create a new file named: Counter.jsx
Paste the following:

import { createSignal } from "solid-js";

export default () => {
const [count, setCount] = createSignal(0);

return (

<div class="bg-blue-900 text-white p-5">
<div>Count = {count()}</div>
<button onClick={() => setCount(count() + 1)}>Add One</button>
</div>
);
};

Step 9: In app.js paste the following:

import { render } from "solid-js/web";

import Counter from "./Counter";

import "./index.scss";

const App = () => (

  <div class="mt-10 text-3xl mx-auto max-w-6xl">
    <div>Name: remote</div>
    <Counter />
  </div>
);

render(App, document.getElementById("app"));

Step 10: Now in the webpack.config.js we need to expose the path in the "ModuleFederationPlugin" add the following in the exposes key:
"./Counter": "./src/Counter.jsx",

Step 11: Restart remote client, so it looks like everything is still working? But now if you go to the url in the browser and append the following:
/remoteEntry.js
You can see a new file which is auto generated from webpack. It is a manifest of all modules which are exposed from that remote.

Step 12: Now copy the url "http://localhost:3000/remoteEntry.js"

Step 13: Got to the host app now and open the webpack.config.js of the host, and past the following into the remotes key: remote: "remote@http://localhost:3000/remoteEntry.js"

Step 14: Stay in host and got to App.js paste the following:

import { render } from "solid-js/web";

import Counter from "remote/Counter";

import "./index.scss";

const App = () => (

  <div class="mt-10 text-3xl mx-auto max-w-6xl">
    <div>Name: host</div>
    <Counter />
  </div>
);
render(App, document.getElementById("app"));

Step 15: Reboot host client

Step 16: BOOM now works, if you don't believe it go to the remote folder and go to counter.jsx and changed the color from blue to red. Check remote and it will change, then got to the host browser and refresh and it will change.

Step 17: Now we have module federation in place on runtime, what happens if we introduce lovely react? Lets try it:
npx create-mf-app
Name: react-host
Port: 3001
Application
React
Javascript
Tailwind

Step 18: cd react-host
Step 19: npm i
Step 20: npm start

Step 21: paste the same url form earlier into the react-host webpack.config.js remotes key: remote: "remote@http://localhost:3000/remoteEntry.js"

Step 22: Now we know react doesn't know what Solid js is so we need to create a universal function and wrap the counter in it.
go to remote folder and create a new file in src called: counterWrapper.jsx, paste the following:

import { render } from "solid-js/web";

import Counter from "./Counter";

export default (el) => {
render(Counter, el);
};

Step 23: Now we need to expose it, go to the remote webpack.config.js and in the exposes key paste:
"./counterWrapper": "./src/counterWrapper.jsx",

Step 24: Restart remote client

Step 25: Now go to react-host and in App.jsx paste the following:
import React, { useRef, useEffect } from "react";
import ReactDOM from "react-dom";

import counterWrapper from "remote/counterWrapper";

import "./index.scss";

const App = () => {
const divRef = useRef(null);

useEffect(() => {
counterWrapper(divRef.current);
}, []);

return (

<div className="mt-10 text-3xl mx-auto max-w-6xl">
<div>Name: react-host</div>
<div ref={divRef}></div>
</div>
);
};
ReactDOM.render(<App />, document.getElementById("app"));

Step 26: Now restart react-host client

Step 27: All done :P
