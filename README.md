# PubSub
An alternative way of state lifting in React Js

  It is a big deal to exchange state between two sibling components or between nested components. To solve this problem common approaches are 
 - State lifting to parent component / or to pass state to nested components as a props.
 - Redux, connecting our components to store and subscribing the data.
 - React Context API, By using react context API we can create context Providers and we can consume data where ever we want.

  From the above all three approaches we need to code additionally to pass state from one component to another and some basic configuration setup also required.
  
  Apart from the above three approaches, we have another approach that is `PubSub` with minimal config and code effort.
  It is one of the design patterns `publish and subscribe`  the publisher will publish the data to a particular topic and the subscriber will receive the data based on the subscription topic. To know more about this click [here](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ff649664(v=pandp.10))
  
```jsx
const PubSub = {
  events: {},
  publish(event, data) {
    if (!this.events[event]) return;
    this.events[event].forEach(callback => callback(data));
  },
  subscribe(event, callback) {
    if (!this.events[event]) this.events[event] = [];
    this.events[event].push(callback);
  },
  unsubscribe(event) {
      delete this.events[event]
  }
};
```
## Example
The below snipet code will show how to subscribe, publish and unsubscribe topic or type.
```jsx
PubSub.subscribe("SHOW_NOTIFICATIONS", message => {
  console.log("SHOW_NOTIFICATIONS", message);
});
PubSub.publish('SHOW_NOTIFICATIONS', {payload:'Notifiication1'})
```
From the below code ComponentA will subscribe a perticuler topic or type with handler method which will receive the data when ever data published to this topic or type.

```jsx
import React from "react";
import PubSub from "../Utils/PubSub";

class ComponentA extends React.Component {
  constructor(props) {
    super(props);
    // subscribing a type DONE.
    PubSub.subscribe("DONE", this.handleAction);
  }

  componentWillUnmount(){
    PubSub.unsubscribe('DONE')
  }
  
  handleAction(data) {
    // perform action
  }
}
```
From the below code ComponentB will publish the data to a perticuler topic or type by using handleSubmit method.
```jsx
import React from "react";
import PubSub from "../Utils/PubSub";

class ComponentB extends React.Component {
  constructor(props) {
    super(props);
  }

  handleSubmit() {
    const { someState } = this.state;
    PubSub.publish("DONE", someState);
  }
}
```

Like the above three approaches, this also has its some limitations,
 - We can not pass more than one argument data at a time. if we need to pass we have to wrap into a single object and then we need to send. or we need to write one more function in `PubSub` to handle it. 
 - We need to unsubscribe the topic or type to avoid memory leakage.
 
By comparing with remaining approaches it is easy to design and use.
