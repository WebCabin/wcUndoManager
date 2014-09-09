# Web Cabin Undo Manager #


Web Cabin's Undo Manager can be used to effectively provide undo/redo events into your own web application with ease.

View a simple demonstration here: [http://undo.webcabin.org](http://undo.webcabin.org)

****
## Basic Usage ##

Begin by initializing an instance of the undo manager:

    var undoManager = new wcUndoManager();

To add an event into the manager, call addEvent() and pass in a description and three functions as parameters:
* Init(): This will initialize the event, use 'this' to assign new object variables to it.
* Undo(): This function will be called whenever the event is being un-done.  Use values stored previously by the init function by accessing 'this'.  A return object value can optionally be provided and used after the event has processed.
* Redo(): This function will be called whenever the event is being re-done.  Use values stored previously by the init function by accessing 'this'.  A return object value can optionally be provided and used after the event has processed.

    undoManager.addEvent(Init, Undo, Redo);

Here is an example:

    var oldValue; // This should contain the old value of the element before the change.
    var newValue = document.getElementById("edit").value;
    undoManager.addEvent("Text Changed",
      // Init Function
      function() {
        this.oldValue = oldValue;
        this.newValue = newValue;
      },
      // Undo Function
      function() {
        document.getElementById("edit").value = this.oldValue;
      },
      // Redo Function
      function() {
        document.getElementById("edit").value = this.newValue;
      });

To perform an undo or redo event, simply call the undo or redo functions on the manager.

    undoManager.undo();
    // or
    undoManager.redo();

****
## Advanced Usage ##

You can group several undo events into a single event by wrapping all calls to addEvent() between a call to beginGroup() and endGroup().  Multiple groups can also be made inside of another group (you must still call endGroup() for each one), although it has no functional advantage as they are all combined into a single final event.

The Undo and Redo event function callbacks from addEvent() can optionally return an object value with properties inside.  This object can then be caught as a result of the managers undo() or redo() call and then used as desired.  Most notably, this feature is meant to be used to specify what changed in order to more accurately target page refresh.  For example, if we were to change the above example to this instead:

    undoManager.addEvent("Text Changed",
      // Init
      function() {
        this.oldValue = oldValue;
        this.newValue = newValue;
      },
      // Undo
      function() {
        return {
          editValueChanged: this.oldValue,
        };
      },
      // Redo
      function() {
        return {
          editValueChanged: this.newValue,
        };
      });

If you notice, in this example we no longer call document.getElementById() to change the displayed value directly, instead we return an object with a property containing the changed value and then we can catch it during our call to undo or redo:

    var obj = undoManager.undo();
    if (obj.hasOwnProperty("editValueChanged")) {
      document.getElementById("edit").value = obj.editValueChanged;
    }

The reason the return value is in the form of an object with properties is because those properties can automatically be combined during a group operation.

Assuming all saveable actions are recorded as undo events, the manager can also keep track of whether the current state of the application is modified from its last recorded position.  This means whenever a new undo event is added, the state is modified, but that modified state will go away if the user undoes that action.  When the application is saved, a call to clearModified() will assign the current state as the un-modified state.  You can then use isModified() at any time to check whether your application contains any changes.

****
## License ##

[MIT License](http://www.opensource.org/licenses/mit-license.php)

&copy; 2014 Jeff P. Houde ([lochemage@gmail.com](mailto:lochemage@gmail.com))

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

****
## Suggestions/Comments? ##
Please feel free to contact me, Jeff Houde ([lochemage@gmail.com](mailto:lochemage@gmail.com)), for any information or to give feedback and suggestions.

Thank you
