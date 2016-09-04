# Tic-Tac-Toe.js

Tic-Tac-Toe game written in vanilla javascript using redux-like approach.


<img src="https://raw.githubusercontent.com/ramonvictor/tic-tac-toe-js/master/assets/tic-tac-toe.png" width="888" height="auto" alt="Mobile and desktop Tic-Tac-Toe.js screenshots">

## How the game applies Redux pattern?

It uses the unidirectional data flow:

<img src="https://raw.githubusercontent.com/ramonvictor/tic-tac-toe-js/master/assets/tic-tac-toe-js-data-flow.png" width="872" height="auto" alt="Mobile and desktop Tic-Tac-Toe.js screenshots">

### The key principles

**1. Single source of truth** 

One single [store.js](https://github.com/ramonvictor/tic-tac-toe-js/blob/master/src/store.js):

```javascript
function Store() {
  this.state = {};
  this.state = this.update(this.state, {});
  // `this.update()` will return the initial state:
  // ----------------------------------------------
  // {
  //   grid: ['', '', '', '', '', '', '', '', ''],
  //   turn: 'x',
  //   score: { x: 0, o: 0 },
  //   winnerSequence: [],
  //   turnCounter: 0,
  //   player: ''
  // }
}
```

**2. State is read-only**

[Game.js](https://github.com/ramonvictor/tic-tac-toe-js/blob/master/src/game.js) dispatches actions whenever needed:

```javascript
this.$table.addEventListener('click', function(event) {
  var state = store.getState();
  // [Prevent dispatch under certain conditions]
  // Otherwise, trigger `SET_X` or `SET_O`
  store.dispatch({
    type: state.turn === 'x' ? 'SET_X' : 'SET_O',
    index: parseInt(index, 10)
  });
});
```

**3. Changes are made with pure functions**

[Store.js](https://github.com/ramonvictor/tic-tac-toe-js/blob/master/src/store.js): reducers receive actions and return new state.

```javascript
// Reducer (pure function)
function updatePlayer(player, action) {
  switch (action.type) {
    case 'PICK_SIDE':
      return action.side;
    default:
      return player || '';
  }
}

// Call reducer on Store.update()
Store.prototype.update = function(state, action) {
  return {
    player: updatePlayer(state.player, action)
    // [...other cool stuff here]
  };
};
```

**4. After update, UI can render latest state**

[Game.js](https://github.com/ramonvictor/tic-tac-toe-js/blob/master/src/game.js) handles UI changes:

```javascript
var store = require('./store');
var gridView = require('./grid-view');

TicTacToe.prototype.eventListeners = function() {
  store.subscribe(this.render.bind(this));
};

TicTacToe.prototype.render = function(prevState, state) {
  // You can even check whether new state is different
  if (prevState.grid !== state.grid) {
    this.gridView.render('grid', state.grid);
  }
};
```


