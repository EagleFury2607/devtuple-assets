---
date: 2021-05-21
title: "Creating Custom Input Element for Form using 'react-jsonschema-form'"
author: "Aditya Kulkarni"
categories:
  - Cheatsheet
tags:
  - React
  - NodeJS
  - Cheatsheet
---

react-jsonschema-form is a React component that can be used to build forms using JSON Schema.
In this article we'll discuss about creating custom form elements to use in a Web form. react-jsonschema-form supports multiple themes but we will be using Material-UI theme. In this example, we will be creating a duration component which captures duration in terms of {days,weeks,months,years}.

### Requirements:

1. React
2. @material-ui/core >= 4.2.0, @material-ui/icons
3. @rjsf/core >= 2.0.0, @rjsf/material-ui

### Process to create custom input elements:

1. Create a react project.
2. Add the required node_modules. Also, add Bootstrap 4 cdn in public/index.html.

```bash
yarn add @material-ui/core @material-ui/icons @rjsf/core @rjsf/material-ui
```

3. Project structure:

```
├──proj_root/
    ├──public/                     # The root folder that gets dealt by the web server
    ├──src/                        # Where your components live
    |   ├──components/             # All components reside here
    |   |   ├──DurationComponent/  # A component called 'DurationComponent'
    |   |   |   ├──DurationComponent.jsx
    |   |   ├──App.js
    |   ├──index.jsx               # Entrypoint, this is where react binds the component to a div with an ID of root
    ├──nodemodules/                # A black hole
    ├──.gitignore                  # Files to be ignored by git/docker
    ├──package-lock.json           # Wizardy
    ├──package.json                # Info on your project and its dependencies
```

4. For creating a form element using react-jsonschema-form we need to declare a json schema which is basically a json object.
   In App.js:

```js
const formSchema = {
  type: "object",
  properties: {
    years: {
      type: "number",
    },
    months: {
      type: "number",
    },
    weeks: {
      type: "number",
    },
    days: {
      type: "number",
    },
  },
};
```

We specify type of element using "type" and as type is "object" we specify attributes inside "properties". For each property we have:

```
name: {
  type: [string, number, object, etc..],
  [other options]
}
```

5. Now, let's create a React component to go with the formSchema. This is a class component with name DurationComponent
   In DurationComponent.jsx:

```js
class DurationComponent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      days: 0,
      weeks: 0,
      months: 0,
      years: 0,
    };
  }

  onChange(name, value) {
    this.setState(
      {
        ...this.state,
        [name]: value,
      },
      () => {
        this.props.onChange(this.state);
      }
    );
  }

  render() {
    return (
      <div>
        <Typography>Duration (Days)</Typography>
        <Slider
          defaultValue={0}
          name="days"
          aria-labelledby="discrete-slider"
          valueLabelDisplay="auto"
          min={0}
          max={6}
          value={this.state.days}
          onChange={(e, val) => {
            this.onChange("days", val);
          }}
        />
        <Typography>Duration (Weeks)</Typography>
        <Slider
          defaultValue={0}
          name="weeks"
          aria-labelledby="discrete-slider"
          valueLabelDisplay="auto"
          min={0}
          max={3}
          value={this.state.weeks}
          onChange={(e, val) => {
            this.onChange("weeks", val);
          }}
        />
        <Typography>Duration (Months)</Typography>
        <Slider
          defaultValue={0}
          name="months"
          aria-labelledby="discrete-slider"
          valueLabelDisplay="auto"
          min={0}
          max={12}
          value={this.state.months}
          onChange={(e, val) => {
            this.onChange("months", val);
          }}
        />
        <Typography>Duration (Years)</Typography>
        <Slider
          defaultValue={0}
          name="years"
          aria-labelledby="discrete-slider"
          valueLabelDisplay="auto"
          min={0}
          max={5}
          value={this.state.years}
          onChange={(e, val) => {
            this.onChange("years", val);
          }}
        />
      </div>
    );
  }
}
export default DurationComponent;
```

6. Similarly, we create fields and uiSchema objects. (fields is for add ing custom field and uiSchema is for adding UI styling to form)
   In App.js:

```js
const fields = {
  duration: DurationComponent,
};
const uiSchema = { "ui:field": "duration" };
```

7. To create the form we add <Form> element.
   In App.js:

```js
<Form schema={formSchema} uiSchema={uiSchema} fields={fields} />
```

Finally, our code lokks like:

## App.js

```js
import { Grid } from "@material-ui/core";
import React from "react";
import Form from "@rjsf/material-ui";
import DurationComponent from "./DurationComponent/DurationComponent";

function App() {
  const formSchema = {
    type: "object",
    properties: {
      years: {
        type: "number",
      },
      months: {
        type: "number",
      },
      weeks: {
        type: "number",
      },
      days: {
        type: "number",
      },
    },
  };
  const fields = {
    duration: DurationComponent,
  };
  const uiSchema = { "ui:field": "duration" };

  const formSubmit = ({ formData }, e) => {
    console.log(formData);
  };

  return (
    <div>
      <Grid container>
        <Grid item xs={12} style={{ padding: "20px" }}>
          <Form
            schema={formSchema}
            uiSchema={uiSchema}
            fields={fields}
            onSubmit={formSubmit}
          />
        </Grid>
        <Grid item xs={12} style={{ padding: "20px" }}></Grid>
      </Grid>
    </div>
  );
}

export default App;
```

## DurationComponent.jsx

```js
import { Grid, Slider, Typography } from "@material-ui/core";
import React, { Component } from "react";

class DurationComponent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      days: 0,
      weeks: 0,
      months: 0,
      years: 0,
    };
  }

  onChange(name, value) {
    this.setState(
      {
        ...this.state,
        [name]: value,
      },
      () => {
        this.props.onChange(this.state);
      }
    );
  }

  render() {
    return (
      <div>
        <Grid container>
          <Grid item xs={12} style={{ margin: "25px" }}>
            <Grid container>
              <Grid item xs={6} style={{ padding: "15px" }}>
                <Typography>Duration (Days)</Typography>
                <Slider
                  defaultValue={0}
                  name="days"
                  aria-labelledby="discrete-slider"
                  valueLabelDisplay="auto"
                  min={0}
                  max={6}
                  value={this.state.days}
                  onChange={(e, val) => {
                    this.onChange("days", val);
                  }}
                />
              </Grid>
              <Grid item xs={6} style={{ padding: "15px" }}>
                <Typography>Duration (Weeks)</Typography>
                <Slider
                  defaultValue={0}
                  name="weeks"
                  aria-labelledby="discrete-slider"
                  valueLabelDisplay="auto"
                  min={0}
                  max={3}
                  value={this.state.weeks}
                  onChange={(e, val) => {
                    this.onChange("weeks", val);
                  }}
                />
              </Grid>
            </Grid>
          </Grid>
          <Grid item xs={12} style={{ margin: "25px", padding: "15px" }}>
            <Typography>Duration (Months)</Typography>
            <Slider
              defaultValue={0}
              name="months"
              aria-labelledby="discrete-slider"
              valueLabelDisplay="auto"
              min={0}
              max={12}
              value={this.state.months}
              onChange={(e, val) => {
                this.onChange("months", val);
              }}
            />
          </Grid>
          <Grid item xs={12} style={{ margin: "25px", padding: "15px" }}>
            <Typography>Duration (Years)</Typography>
            <Slider
              defaultValue={0}
              name="years"
              aria-labelledby="discrete-slider"
              valueLabelDisplay="auto"
              min={0}
              max={25}
              value={this.state.years}
              onChange={(e, val) => {
                this.onChange("years", val);
              }}
            />
          </Grid>
        </Grid>
      </div>
    );
  }
}

export default DurationComponent;
```

## index.js

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./components/App";

ReactDOM.render(<App />, document.getElementById("root"));
```

### Outputs:

![react-jsonschema-form Output](https://github.com/EagleFury2607/devtuple-assets/blob/main/rjsf-outputimage.jpg)

```bash
{days: 3, weeks: 1, months: 8, years: 2}
```
