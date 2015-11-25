Redux Namespace
=============

Dead simple tool moving component local state to a Redux store namespace.

```shell
npm install --save redux-namespace
```

## Motivation
Transient state like toggles and form input require too much boiler plate.

[`redux-namespace`](https://www.npmjs.com/package/redux-namespace)
helps you connect your component with a trivial key value store. This solves the
far–too–painful/should–be–easier problems with managing view state after routing.

### Why store component state outside the component?
To get the incredible time traveling super powers provided by complete hydration
in your app state. Time travel is fun, play with [this](http://todo.cmyk.nyc).

Redux Namespace builds on [React Redux](https://www.npmjs.com/package/react-redux/),
but with two new methods: ```select``` and ```assign``` along with `dispatch`.
Calling `assign(key, value)` puts a value in your namespace and `select(key)` gets
it out. Calling `select()` with no parameters returns the entire namespace.

Connecting your components with their own namespace is trivial. Use it like
React Redux, but forget about writing the selectors.
```js
export namespace.connect('route/namespace')(Component)
```

## Installation

```js
npm install --save redux-namespace
```

#### Attach the Reducer
```js
import { createStore, combineReducers } from 'redux';
import namespace from 'redux-namespace';

const store = createStore(combineReducers({namespace}));
```

#### Bind your component

## Usage


#### Native
```js
import React, {View, Text, TextInput, TouchableHighlight, } from 'react-native'
import namespace from 'redux-namespace/native';

// If decorators are your thing
@namespace.connect('component/namespace')
class Form extends React.Component {
  static propTypes = {...@namespace.shape}

  render () {
    let {select, assign, dispatch} = this.props;
    return (
      <View>
        <TextInput
          value={select('email')} onChange={assign('email')}/>

        <TextInput
          value={select('password')} onChange={assign('password')}/>

        <TouchableHighlight onPress={e => dispatch(someAction(select()))}>
          <Text>Submit</Text>
        </TouchableHighlight>
      </View>
    )
  }
}
```


#### Web
```js
import React from 'react'
import namespace from 'redux-namespace';

class Form extends React.Component {
  static propTypes = {...@namespace.shape}

  render () {
    let {select, assign, dispatch} = this.props;
    return (
      <form onSumbit={() => dispatch(someAction(select()))}/>
        <input
          value={select('email')}
          onChange={e => assign('email', e.target.value)}/>
        <input
          value={select('password')}
          onChange={e => assign('password', e.target.value)}/>
      </form>
    )
  }
}

// Or, if decorators aren't your thing
export namespace.connect('component/namespace')(Form)
```

#### Lazy binding… sort of
```js
import React from 'react'
import namespace from 'redux-namespace';

@namespace.connect('documents')
class DocumentEditor extends React.Component {
  static propTypes = {...@namespace.shape}

  render () {
    let document = namespace.connect(`documents/${select('currentId')}`);
    let autosave = namespace.connect(`documents/${id}/autosave`);

    let Loader = document(Await)
    let Autosave = bridge(autosave(DebouncedSave))

    return (
      <Loader>
        <Autosave/>
        { map(React.createElement,
            map(document, [
              Menu,
              WYSIWYG,
              Editor,
              WordCount
            ]))
          }
      </Loader>
    )
  }
}

// Or, if decorators aren't your thing
export namespace.connect('component/namespace')(Form)
```


### Skip writing actions
Using the `namespace.BIND` constant you can write new reducers to work with
your events without adding more plumbing.

```js
function (state, action) {
  if (action.type === namespace.BIND) {
    let {namespace, key, value} = action.payload;

    switch (namespace) {
      case 'app/signup':
        state[namespace][key].error = validate(key, value.field);
        break;

      case 'my/easter/egg':
        if (key === 'menuToggle')
          if (++state.finalCountdown > 5)
            state.surpriseCatGif = true
        break;
    }
  }
  return state;
}
```

Although you probably shouldn't do this often, it's faster than scaffolding
the additional constants and action creators. Beware, with great power comes greater side effects…
that we probably should avoid unless we liked Angular 1—better known as Whoopsie Daisy.


## License

MIT
