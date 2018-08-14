# Advanced React Patterns
Notes and examples from a Front End Masters course.

## TOC
- [Compound Components: Basic](#compound-components-basic)
- [Compound Components: Flexible (uses context)](#compound-components-flexible-uses-context-)
- [Render Props: Basic](#render-props-basic)
- [Render Props: Prop Collections](#render-Props-prop-collections)
- [Render Props: Prop Getters](#render-props-prop-getters)
- [State Initializers](#state-initializers)
- [State Reducer: Basic](#state-reducer-basic)
- [State Reducer: With Types](#state-reducer-with-types)
- [Primer](#primer)
- [Provider Pattern](#provider-pattern)

## Compound Components: Basic
- Allows consumer of component to reorder elements
- Components that share implicit state with main component
- Likens it to `<select><options>` in html

### Usage
```jsx
<Toggle onToggle={onToggle}>
  <Toggle.On>The button is on</Toggle.On>
  <Toggle.Off>The button is off</Toggle.Off>
  <Toggle.Button />
</Toggle>
```
### Setup
```jsx
class Toggle extends React.Component {
  static On = ({on, children}) => (on ? children : null)
  static Off = ({on, children}) => (on ? null : children)
  static Button = ({on, toggle, ...props}) => (
    <Switch on={on} onClick={toggle} {...props} />
  )
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  render() {
    return React.Children.map(this.props.children, child =>
      React.cloneElement(child, {
        on: this.state.on,
        toggle: this.toggle
      })
    )
  }
}
```

## Compound Components: Flexible (uses context)
- Uses context to pass data to static components
- Avoid rerenders by only passing this.state instead of each prop individually

### Usage
```jsx
<Toggle onToggle={onToggle}>
  <Toggle.On>The button is on</Toggle.On>
  <Toggle.Off>The button is off</Toggle.Off>
  <div>
    <Toggle.Button />
  </div>
</Toggle>
```

### Setup
```jsx
const ToggleContext = React.createContext({})
class Toggle extends React.Component {
  static On = ({children}) => (
    <ToggleContext.Consumer>
      {({on}) => (on ? children : null)}
    </ToggleContext.Consumer>
  )
  static Off = ({children}) => (
    <ToggleContext.Consumer>
      {({on}) => (on ? null : children)}
    </ToggleContext.Consumer>
  )
  static Button = props => (
    <ToggleContext.Consumer>
      {({on, toggle}) => (
        <Switch on={on} onClick={toggle} {...props} />
      )}
    </ToggleContext.Consumer>
  )
  toggle = () => (
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  )
  state = {on: false, toggle: this.toggle}
  render() {
    return (
      <ToggleContext.Provider value={this.state}>
        {this.props.children}
      </ToggleContext.Provider>
    )
  }
}
```

## Render Props: Basic
- More work for the implentor but also way more freedom
- Explicit about what state/props you are exposing
- Children pass as one elem (not array)
- He's not a fan of component injection (a bit hairy) this is easier, less nesting

### Usage
```jsx
<Toggle onToggle={onToggle}>
  {({on, toggle}) => (
    <div>
      {on ? 'The button is on' : 'The button is off'}
      <Switch on={on} onClick={toggle} />
      <hr />
      <button aria-label="custom-button" onClick={toggle}>
        {on ? 'on' : 'off'}
      </button>
    </div>
  )}
</Toggle>
```
### Setup
```jsx
class Toggle extends React.Component {
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## Render Props: Prop Collections
- Uses render props uses a function to get collections of props used for similiar components/html tags
### Usage
```jsx
<Toggle onToggle={onToggle}>
  {({on, togglerProps}) => (
    <div>
      <Switch on={on} {...togglerProps} />
      <hr />
      <button aria-label="custom-button" {...togglerProps}>
        {on ? 'on' : 'off'}
      </button>
    </div>
  )}
</Toggle>
```
### Setup
```jsx
class Toggle extends React.Component {
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  getStateAndHelpers() { // The big change here
    return {
      on: this.state.on,
      toggle: this.toggle,
      togglerProps: {
        'aria-pressed': this.state.on,
        onClick: this.toggle,
      },
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## Render Props: Prop Getters
- Improves prop collections and merges like props

### Usage
```jsx
<Toggle onToggle={onToggle}>
  {({on, getTogglerProps}) => (
    <div>
      <Switch {...getTogglerProps({on})} />
      <hr />
      <button
        {...getTogglerProps({
          'aria-label': 'custom-button',
          onClick: onButtonClick,
          id: 'custom-button-id',
        })} >
        {on ? 'on' : 'off'}
      </button>
    </div>
  )}
</Toggle>
```
### Setup
```js
const callAll = (...fns) => (...args) => {
  fns.forEach(fn => fn && fn(...args))
}
class Toggle extends React.Component {
  state = {on: false}
  toggle = () => {
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  }
  getTogglerProps = ({onClick, ...props} = {}) => ({
    'aria-pressed': this.state.on,
    onClick: callAll(onClick, this.toggle),
    ...props,
  })
  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
      getTogglerProps: this.getTogglerProps,
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## State Initializers
- Can be used outside of render props but using with for now
- Allows you to reset to an initial state and to start at a given state
### Usage
```jsx
<Toggle
  initialOn={initialOn}
  onToggle={onToggle}
  onReset={onReset}>
  {({getTogglerProps, on, reset}) => (
    <div>
      <Switch {...getTogglerProps({on})} />
      <hr />
      <button onClick={() => reset()}>Reset</button>
    </div>
  )}
</Toggle>
```

### Setup
```jsx
const callAll = (...fns) => (...args) =>
  fns.forEach(fn => fn && fn(...args))

class Toggle extends React.Component {
  static defaultProps = {
    initialOn: false,
    onReset: () => {},
  }
  initialState = {on: this.props.initialOn}
  state = this.initialState
  reset = () =>
    this.setState(this.initialState, () =>
      this.props.onReset(this.state.on),
    )
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  getTogglerProps = ({onClick, ...props} = {}) => ({
    onClick: callAll(onClick, this.toggle),
    'aria-pressed': this.state.on,
    ...props,
  })
  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
      reset: this.reset,
      getTogglerProps: this.getTogglerProps,
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## State Reducer: Basic
- Enables users to control how the state logic works
- Nothing to do with redux
- Breaks enscapsulation but allows for a lot of flexibility

### Usage
```jsx
class Usage extends React.Component {
  static defaultProps = {
    onToggle: (...args) => console.log('onToggle', ...args),
    onReset: (...args) => console.log('onReset', ...args),
  }
  initialState = {timesClicked: 0}
  state = this.initialState
  handleToggle = (...args) => {
    this.setState(({timesClicked}) => ({
      timesClicked: timesClicked + 1,
    }))
    this.props.onToggle(...args)
  }
  handleReset = (...args) => {
    this.setState(this.initialState)
    this.props.onReset(...args)
  }
  toggleStateReducer = (state, changes) => {
    if (this.state.timesClicked >= 4) {
      return {...changes, on: false}
    }
    return changes
  }
  render() {
    const {timesClicked} = this.state
    return (
      <Toggle
        stateReducer={this.toggleStateReducer}
        onToggle={this.handleToggle}
        onReset={this.handleReset}
      >
        {toggle => (
          <div>
            <Switch
              {...toggle.getTogglerProps({
                on: toggle.on,
              })}
            />
            {timesClicked > 4 ? (
              <div data-testid="notice">
                Whoa, you clicked too much!
                <br />
              </div>
            ) : timesClicked > 0 ? (
              <div data-testid="click-count">
                Click count: {timesClicked}
              </div>
            ) : null}
            <button onClick={toggle.reset}>Reset</button>
          </div>
        )}
      </Toggle>
    )
  }
}
```

### Setup
```jsx
const callAll = (...fns) => (...args) =>
  fns.forEach(fn => fn && fn(...args))

class Toggle extends React.Component {
  static defaultProps = {
    initialOn: false,
    onReset: () => {},
    stateReducer: (state, changes) => changes,
  }
  initialState = {on: this.props.initialOn}
  state = this.initialState
  internalSetState(changes, callback) {
    this.setState(state => {
      // handle function setState call
      const changesObject =
        typeof changes === 'function' ? changes(state) : changes

      // apply state reducer
      const reducedChanges =
        this.props.stateReducer(state, changesObject) || {}

      // return null if there are no changes to be made
      // (to avoid an unecessary rerender)
      return Object.keys(reducedChanges).length
        ? reducedChanges
        : null
    }, callback)
  }
  reset = () =>
    this.internalSetState(this.initialState, () =>
      this.props.onReset(this.state.on),
    )
  toggle = () =>
    this.internalSetState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  getTogglerProps = ({onClick, ...props} = {}) => ({
    onClick: callAll(onClick, this.toggle),
    'aria-pressed': this.state.on,
    ...props,
  })
  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
      reset: this.reset,
      getTogglerProps: this.getTogglerProps,
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## State Reducer: With Types
- So that we check context of updating state and update accordingly
- Helpful for different user inputs or actions
### Usage
```jsx
class Usage extends React.Component {
  static defaultProps = {
    onToggle: (...args) => console.log('onToggle', ...args),
    onReset: (...args) => console.log('onReset', ...args),
  }
  initialState = {timesClicked: 0}
  state = this.initialState
  handleToggle = (...args) => {
    this.setState(({timesClicked}) => ({
      timesClicked: timesClicked + 1,
    }))
    this.props.onToggle(...args)
  }
  handleReset = (...args) => {
    this.setState(this.initialState)
    this.props.onReset(...args)
  }
  toggleStateReducer = (state, changes) => {
    if (changes.type === 'forced') {
      return changes
    }
    if (this.state.timesClicked >= 4) {
      return {...changes, on: false}
    }
    return changes
  }
  render() {
    const {timesClicked} = this.state
    return (
      <Toggle
        stateReducer={this.toggleStateReducer}
        onToggle={this.handleToggle}
        onReset={this.handleReset}
        ref={this.props.toggleRef}
      >
        {({on, toggle, reset, getTogglerProps}) => (
          <div>
            <Switch
              {...getTogglerProps({
                on: on,
              })}
            />
            {timesClicked > 4 ? (
              <div data-testid="notice">
                Whoa, you clicked too much!
                <br />
                <button onClick={() => toggle({type: 'forced'})}>
                  Force Toggle
                </button>
                <br />
              </div>
            ) : timesClicked > 0 ? (
              <div data-testid="click-count">
                Click count: {timesClicked}
              </div>
            ) : null}
            <button onClick={reset}>Reset</button>
          </div>
        )}
      </Toggle>
    )
  }
}
```
### Setup
```jsx
const callAll = (...fns) => (...args) =>
  fns.forEach(fn => fn && fn(...args))

class Toggle extends React.Component {
  static defaultProps = {
    initialOn: false,
    onReset: () => {},
    stateReducer: (state, changes) => changes,
  }
  // 💰 any time I use a string as an identifier for a type,
  // I prefer to give it a variable name. That way folks who
  // want to reference the type can do so using variable which
  // will help mitigate the problems of indirection.
  static stateChangeTypes = {
    reset: '__toggle_reset__',
    toggle: '__toggle_toggle__',
  }
  initialState = {on: this.props.initialOn}
  state = this.initialState
  internalSetState(changes, callback) {
    this.setState(state => {
      // handle function setState call
      const changesObject =
        typeof changes === 'function' ? changes(state) : changes

      // apply state reducer
      const reducedChanges =
        this.props.stateReducer(state, changesObject) || {}

      // remove the type so it's not set into state
      const {type: ignoredType, ...onlyChanges} = reducedChanges

      // return null if there are no changes to be made
      return Object.keys(onlyChanges).length ? onlyChanges : null
    }, callback)
  }

  reset = () =>
    this.internalSetState(
      {...this.initialState, type: Toggle.stateChangeTypes.reset},
      () => this.props.onReset(this.state.on),
    )
  toggle = ({type = Toggle.stateChangeTypes.toggle} = {}) =>
    this.internalSetState(
      ({on}) => ({type, on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  getTogglerProps = ({onClick, ...props} = {}) => ({
    onClick: callAll(onClick, () => this.toggle()),
    'aria-pressed': this.state.on,
    ...props,
  })
  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
      reset: this.reset,
      getTogglerProps: this.getTogglerProps,
    }
  }
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

## Primer
- Two separate components to synchronize states
- Likens it to two-way binding in Angular
### Usage
```jsx
class Usage extends React.Component {
  state = {bothOn: false}
  handleToggle = on => {
    this.setState({bothOn: on})
  }
  render() {
    const {bothOn} = this.state
    const {toggle1Ref, toggle2Ref} = this.props
    return (
      <div>
        <Toggle
          on={bothOn}
          onToggle={this.handleToggle}
          ref={toggle1Ref}
        />
        <Toggle
          on={bothOn}
          onToggle={this.handleToggle}
          ref={toggle2Ref}
        />
      </div>
    )
  }
}
```
### Setup
```jsx
class Toggle extends React.Component {
  state = {on: false}
  isControlled(prop) {
    return this.props[prop] !== undefined
  }
  getState() {
    return {
      on: this.isControlled('on') ? this.props.on : this.state.on,
    }
  }
  toggle = () => {
    if (this.isControlled('on')) {
      this.props.onToggle(!this.getState().on)
    } else {
      this.setState(
        ({on}) => ({on: !on}),
        () => {
          this.props.onToggle(this.getState().on)
        },
      )
    }
  }
  render() {
    return <Switch on={this.getState().on} onClick={this.toggle} />
  }
}
```
## Provider Pattern
- Solves the problem of prop drilling
- You can expand to use this with render props
### Usage
```jsx
function Usage({
  onToggle = (...args) => console.log('onToggle', ...args),
}) {
  return (
    <Toggle onToggle={onToggle}>
      <Layer1 />
    </Toggle>
  )
}
```
### Setup
```jsx
const ToggleContext = React.createContext()

class Toggle extends React.Component {
  static Consumer = ToggleContext.Consumer
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )
  render() {
    return (
      <ToggleContext.Provider
        value={{on: this.state.on, toggle: this.toggle}}
        {...this.props}
      />
    )
  }
}

const Layer1 = () => <Layer2 />
const Layer2 = () => (
  <Toggle.Consumer>
    {({on}) => (
      <Fragment>
        {on ? 'The button is on' : 'The button is off'}
        <Layer3 />
      </Fragment>
    )}
  </Toggle.Consumer>
)
const Layer3 = () => <Layer4 />
const Layer4 = () => (
  <Toggle.Consumer>
    {({on, toggle}) => <Switch on={on} onClick={toggle} />}
  </Toggle.Consumer>
)
```