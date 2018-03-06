# Shippabo JavaScript Style Guide() {

## Table of Contents

  1. [PropTypes](#proptypes)
  2. [Render Blocks](#render)
  3. [Spacing](#spacing)
  4. [Lodash](#lodash)
  5. [Pure Components](#pure-components)

## PropTypes

  <a name="proptypes--segregation"></a><a name="1.1"></a>
  - [1.1](#proptypes--segregation) Separate `ProvidedProps` injected by `HOCs` and `ReduxProps` injected by redux's `mapStateToPops` and local component `Props`.

    > Why? Seperate props clarifies component interface and provided properties by higher order components

    ```javascript
    // bad
    function Component(props) {
      // ...
    }

    Component.propTypes = {
      classes: PropTypes.object.isRequired,
      onSubmit: PropTypes.func,
      isDefaulted: PropTypes.bool,
    };

    Component.defaultProps = {
      isDefaulted: true,
    };

    export default (withStyles(styles)(Component);

    // good
    const ProvidedProps = {
      classes: PropTypes.object.isRequired,
    };

    export const Props = {
      onSubmit: PropTypes.func,
      isDefaulted: PropTypes.bool,
    };

    const DefaultProps = {
      isDefaulted: true,
    };

    function Component(props) {
      // ...
    }

    Component.propTypes = {
      ...ProvidedProps,
      ...Props,
    };

    Component.defaultProps = DefaultProps;

    export default (withStyles(styles)(Component);
    ```

  <a name="proptypes--export"></a><a name="1.2"></a>
  - [1.2](#proptypes--export) Export local component props interface

    > Why? Exporting props allows other components to inherit from the property interface. However only properties that can be specified should be exported

    ```javascript
    // bad
    const ProvidedProps = {
      // ...
    };

    const Props = {
      // ...
    };

    // bad
    export const ProvidedProps = {
      // ...
    };

    export const Props = {
      // ...
    };

    // good
    const ProvidedProps = {
      // ...
    };

    export const Props = {
      // ...
    };
    ```

**[⬆ back to top](#table-of-contents)**

## Render

  <a name="render--blocks"></a><a name="2.1"></a>
  - [2.2](#render--blocks) Use render blocks (e.g. render methods) instead of inline code

    > Why? Inline render functions causes component rerender even for pure components

    ```javascript
    // bad
    render() {
      return (
        <div>
          {contacts.map(item => (
            <div>
              <span>{contact.firstName}</span>
              <span>{contact.lastName}</span>
              <span>{contact.email}</span>
              <span>{contact.dateOfBirth}</span>
            </div>
          )}
        </div>
      );
    }

    // good
    renderContact(contact) {
      return (
        <div>
          <span>{contact.firstName}</span>
          <span>{contact.lastName}</span>
          <span>{contact.email}</span>
          <span>{contact.dateOfBirth}</span>
        </div>
      );
    }

    render() {
      return (
        <div>
          {contacts.map(this.renderContact)}
        </div>
      );
    }
    ```

  <a name="render--fragment"></a><a name="2.2"></a>
  - [2.2](#render--fragment) Use `React.Fragment` instead of `div` if a root element is needed

    > Why? Less DOM clutter since [react 16](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)

    ```javascript
    // bad
    render() {
      <div>
        <Comp1/>
        <Comp2/>
        <Comp3/>
      </div>
    }

    // good
    render() {
      <React.Fragment>
        <Comp1/>
        <Comp2/>
        <Comp3/>
      </React.Fragment>
    }
    ```

**[⬆ back to top](#table-of-contents)**

## Spacing

  <a name="spacing--vertical"></a><a name="3.1"></a>
  - [3.1](#spacing--vertical) Allocate additional line between logical groupings of statements

    > Why? Vertical white space helps readability by distinguishing logical groupings quickly

    ```javascript
    // bad
    const foo = 'foo';
    const bar = 'bar';
    const fooBar = {
      foo,
      bar,
    };
    let baz = 'baz';
    if (foo === bar) {
      baz = 'foobar';
    }
    ```

    // good
    const foo = 'foo';
    const bar = 'bar';

    const fooBar = {
      foo,
      bar,
    };

    let baz = 'baz';

    if (foo === bar) {
      baz = 'foobar';
    }
    ```

  <a name="spacing--jsx"></a><a name="3.2"></a>
  - [3.2](#spacing--jsx) Allocate additional line between multiline JSX component declarations

    ```javascript
    // bad
    <Container>
      <SearchBar />
      <List>
        <ListHeader>
          My Contacts
        </ListHeader>
        <ListBody>
          {items.map(item => renderItem(item))}
        </ListBody>
        <ListFooter />
      </List>
    </Container>

    // good
    <Container>
      <SearchBar />

      <List>
        <ListHeader>
          My Contacts
        </ListHeader>

        <ListBody>
          {items.map(item => renderItem(item))}
        </ListBody>

        <ListFooter />
      </List>
    </Container>
    ```

**[⬆ back to top](#table-of-contents)**

## Lodash

  <a name="lodash--prefer"></a><a name="4.1"></a>
  - [4.1](#lodash--prefer) Prefer lodash helper methods or consider cases for undefined variables

    > Why? All lodash methods considers cases for falsey values, while native methods do not. Additionally for speed considations lodash will defer to the native implementation if available

      ```javascript
      // bad
      maybeArrayOrEmpty.forEach((item) => {
        // ...
      });

      // good
      _.forEach(maybeArrayOrEmpty, (item) => {
        // ...
      });

      // good
      if (maybeArrayOrEmpty) {
        maybeArrayOrEmpty.forEach((item) => {
          // ...
        });
      }

      // bad
      onClick = (event) => {
        const { handleClick } = this.props;

        if (typeof handleClick === 'function') {
          handleClick(event);
        }
      }

      // good
      onClick = (event) => {
        const { handleClick } = this.props;

        if (_.isFunction(handleClick)) {
          handleClick(event);
        }
      }
      ```

**[⬆ back to top](#table-of-contents)**

## Pure Components

  <a name="pure-components--prefer"></a><a name="5.1"></a>
  - [5.1](#pure-components--prefer) Prefer using React's `PureComponent` over standard `Component`

    > Why? React rerenders on all changes for standard components, however pure components only rerenders for shallow changes

      ```javascript
      // bad
      class MyComponent extends React.Component {
        // ...
      }

      // good
      class MyComponent extends React.PureComponent {
        // ...
      }

      // good
      function MyComponent(props) {
        // ...
      }

      // good
      class MyComponent extends React.Component {
        shouldComponentUpdate(nextProps, nextState) {
          // ...

          return !shallowEquals(nextProps, this.props) || !!shallowEquals(nextState, this.state);
        }
      }
      ```

**[⬆ back to top](#table-of-contents)**
