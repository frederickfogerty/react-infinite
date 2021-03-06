React Infinite
===

**A browser-ready efficient scrolling container based on UITableView**

In the browser, when a long list of DOM elements are placed in a scrollable container, all of them are kept in the DOM tree even when they are scrolled out the user's view. This is highly inefficient, especially in cases when scrolling lists can be tens or hundreds of thousands of items long; the number of additional nodes may well reach the millions.

React Infinite solves this with some inspiration from iOS's UITableView and AirBnB's Infinity.js. In sum, DOM nodes that are not visible to the user at the top and the bottom of the container are rendered as a single blank node that takes up the space those nodes would otherwise have taken up. With React, this becomes even easier. Its virtual DOM allows the same set of nodes to be reused; only their content is changed.

React Infinite is ready for use with both browsers and Node.js. Its only dependency is React, and minified and development versions are available to be dropped in.

SeatGeek currently uses React Infinite in production on our event pages; because we only have pages for events in the future, a link would not be appropriate. To see one, head to one of our team pages for the [New York Giants](https://seatgeek.com/new-york-giants-tickets), or the [New York Mets](https://seatgeek.com/new-york-mets-tickets), or the [New York Knicks](https://seatgeek.com/new-york-knicks-tickets), and click on the green button for an event to see them in action in the Omnibox.

![](http://cl.ly/image/0y0L04220U2T/Screen%20Shot%202014-10-16%20at%2010.56.19%20AM.png)

## Basic Use

React Infinite currently supports displaying a container with numerous rows of items of equal height. Each row should also take up the full width of the container. It also supports downwards infinite scrolling; it will display an infinite loading spinner at the bottom.

To use React Infinite, call it with a list of children that should be rendered by the component:

```xml
<Infinite containerHeight={200} elementHeight={40}>
    <div className="one"/>
    <div className="two"/>
    <div className="three"/>
</Infinite>
```

## Configuration Options

#### Children
The children of the `<Infinite>` element are the components you want to render. This gives you as much flexibility as you need in the presentation of those components. Each child can be a different component if you desire, and each

#### (Required) Number `elementHeight`
The height of each row in pixels.

#### (Required) **Number** `containerHeight`
The height of the scrolling container in pixels.

#### Number `preloadBatchSize`
Defaults to `this.props.containerHeight / 2`. Imagine the total height of the scrollable divs. Now divide this equally into blocks `preloadBatchSize` pixels high. Every time the container's scrollTop enters each of these blocks the set of elements rendered in full are those contained within the block and elements that are within `preloadAdditionalHeight` above and below it.

#### Number `preloadAdditionalHeight`
Defaults to `this.props.containerHeight`. The total height of the area in which elements are rendered in full is height of the current scroll block (see `preloadBatchSize`) as well as `preloadAdditionalHeight` above and below it.

#### **Function** `handleScroll(DOMNode node)`
Defaults to `function(){}`. A function that is called when the container is scrolled, i.e. when the `onScroll` event of the infinite scrolling container is fired. The only argument passed to it is the native DOM [Node](https://developer.mozilla.org/en-US/docs/Web/API/Node) of the scrolling container.

#### **Number** `infiniteLoadBeginBottomOffset`
When the user reaches this number of pixels from the bottom, the infinite load sequence will be triggered by showing the infinite load spinner delegate and calling the function `onInfiniteLoad`. To disable infinite loading, do not provide this property.

#### Function `onInfiniteLoad()`
Defaults to `function(){}`. This function is called when the scroll exceeds `infiniteLoadBeginBottomOffset`. Before this function is called, **the infinite loading spinner is automatically turned on**. You can set up infinite scrolling with this function like this:

1. Fetch a new page of records from the appropriate API
2. When the AJAX call returns, send the new list of elements (with the items that were just fetched) back as the children of React Infinite.
3. Set React Infinite's `isInfiniteLoading` prop to `false` to hide the loading spinner display

`onInfiniteLoad` relies heavily on passing props as a means of communication in the style of idiomatic React.

#### Renderable `loadingSpinnerDelegate`
Defaults to `<div/>`. The element that is provided is used to render the loading view when React Infinite's `isInfiniteLoading` property is set to `true`.

#### Bool `isInfiniteLoading`
Defaults to `false`. This property determines whether the infinite spinner is showing.

#### String `className`
Allows a CSS class to be set on the scrollable container.

## Sample Code
To get you started, here is some sample code that implements an infinite scroll with an simulated delay of 2.5 seconds. A [live demo of this example is available](http://chairnerd.seatgeek.com/react-infinite-a-browser-ready-efficient-scrolling-container-based-on-uitableview/) on our blog.

```javascript
var ListItem = React.createClass({
    render: function() {
        return <div className="infinite-list-item">
        List Item {this.props.key}
        </div>;
    }
});

var InfiniteList = React.createClass({
    getInitialState: function() {
        return {
            elements: this.buildElements(0, 20),
            isInfiniteLoading: false
        }
    },

    buildElements: function(start, end) {
        var elements = [];
        for (var i = start; i < end; i++) {
            elements.push(<ListItem key={i}/>)
        }
        return elements;
    },

    handleInfiniteLoad: function() {
        var that = this;
        this.setState({
            isInfiniteLoading: true
        });
        setTimeout(function() {
            var elemLength = that.state.elements.length,
                newElements = that.buildElements(elemLength, elemLength + 1000);
            that.setState({
                isInfiniteLoading: false,
                elements: that.state.elements.concat(newElements)
            });
        }, 2500);
    },

    elementInfiniteLoad: function() {
        return <div className="infinite-list-item">
            Loading...
        </div>;
    },

    render: function() {
        return <Infinite elementHeight={40}
                         containerHeight={250}
                         infiniteLoadBeginBottomOffset={200}
                         onInfiniteLoad={this.handleInfiniteLoad}
                         loadingSpinnerDelegate={this.elementInfiniteLoad()}
                         isInfiniteLoading={this.state.isInfiniteLoading}
                         >
            {this.state.elements}
        </Infinite>;
    }
});

React.renderComponent(<InfiniteList/>,
        document.getElementById('react-example-one'));
```

## Developing
React Infinite is built with Gulp. To get started, install the development dependencies with `npm install`. If you do not already have Gulp, you might wish to install it globally with `npm install -g gulp`. Then run `gulp`, which builds both the production and development versions. To build just the former, run `gulp buildp`, and to build just the latter, run `gulp build`.

## Future Development

Tests, possibly with Jest, are next on the agenda. It would be useful for React Infinite to be adapted to any arbitrary two-dimensional grid.

## License

**The MIT License (MIT)**

Copyright (c) 2014 SeatGeek

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
