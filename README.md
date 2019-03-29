# Summary
 A helper Class for quick ScriptUI dialogs in Adobe ExtendScript with UMD wrapper for cross-compatability with AMD and node.js require.

The idea being to replace native dialogs with prettier ScriptUI versions that have optional additional functionality and customization. Plus a dropdown menu dialog... cause I use that a lot too.

Basically I got tired of rewriting an alert that has a "Do not ask again" checkbox.

All dialogs function like the native version *or* accept an options object with custom arguments with default settings as fallbacks.

Quick example of a confirmation dialog that has three buttons and a skip-me checkbox...
```
var weapon = confirm({text:"Kill it with...", skippable:true, buttons:["Boredom","Fire","Hugs"]});
// returns the index number of the button selected.
```

# Features
- Works with node.js require, AMD(probably), and vanilla ExtendScript.
- Tries to simplify basic dialog creation to a couple lines.
- Retrievable dialog cache (for skippable dialogs!)
- Optionally replace native Javascript dialogs
- Easily attach to the `$.global` space or your own namespace in a `Dialogs` object

# Import
## NPM
If running Node NPM, you can `npm install ExtendScript_Dialogs` to add to your node_modules folder
## github
Clone or download the repo and copy the extendscript_dialogs.jsxinc to your project

# Include

## NPM
`var Dialogs = require("ExtendScript_Dialogs");`

## AMD
I don't know but it's probably not difficult? Firmly in the untested-but-should-work category

## ExtendScript
### Eval into environment
`$.evalFile("<path>/extendscript_dialogs.jsxinc")`

### Include in scripts
`//@include "<path>/extendscript_dialogs.jsxinc"`

### concatinate or copy-paste directly
Add to a build script or, I dunno, just copy-pasta it in there?

# Use:

## Initialize Dialogs object
Make a "new" ExtendScript_Dialogs. *Note: check the constructor options below for how to override native dialogs.*

You can either pass a root object to attach the Dialogs object to, or directly capture the Dialogs object in a variable. Or both?
```
var myDialogDad = {};
var myDialogs = new ExtendScript_Dialogs(myDialogDad);// two ways to attach Dialogs object...through root arg or return capture
myDialogs.alert('Whatever dad. I don't need you.');
myDialogDad.Dialogs.alert('Can I borrow the car?');// hint, it's the same Dialogs object...
```
### Constructor options
"new" constructor takes 2 optional arguments.

First argument is an alternate root object to tack on a 'Dialogs' alias.

By passing $.global as first arg, we get global Dialogs object!
```
var root = $.global;// root to add convenience aliases to
var overrideNative = false;// replace native dialogs or not

myDialogs = new ExtendScript_Dialogs(root, overrideNative);

Dialogs.alert('Like magic.');
Dialogs.dropdown(["Thing 1","Thing 2"]);
```

The second argument is whether or not to replace native JS dialogs with prettier ExtendScript versions.
```
// don't care about the Dialogs object right now... just native stuff
new ExtendScript_Dialogs(null, true);

alert('I've been overridden!');
var myBool = confirm('Do the thing?');
var myText = prompt('What to say?','Replace these words...');
```

## Dialog Types
### Alert
The alert dialog has a variable-typed parameter to either mimic native Alert or add customization.
Options:
* title: title string
* text: static text string message
* id: id number for retrieval from the cache
* skippable: whether or not to add a "Do not ask again" checkbox

```
/**
 * Replaces standard alert with prettier and skippable ScriptUI version
 * @param {String, Object} String of text or options object.
 *                          args = {
 *                             title:"Confirm",
 *                             text:"Message",
 *                             id:234,
 *                             skippable:true,
 *                          }
 */

 new ExtendScript_Dialogs($.global, true);
 alert("Beware!");
 alert({text:"Beware!"});
```

### Confirm
The confirm dialog has a variable-typed first parameter to either mimic native Confirm or add customization.

Note: If you pass an options object as first argument, it returns the button number (0, 1, 2, etc) instead of a boolean value.

Options Object:
* title: title string
* text: static text string message
* id: id number for retrieval from the cache
* skippable: whether or not to add a "Do not ask again" checkbox
* buttons: a string array that is converted to buttons on the bottom of the dialog. First is "dismiss" function and last is "confirm" by default.

```
/**
 * Replaces standard confirm with prettier and skippable ScriptUI version
 * @param {String, Object} String of text or options object.
 *                          Button order assumes first is cancel, last is accept.
 *                          args = {
 *                             title:"Confirm",
 *                             text:"Message",
 *                             id:234,
 *                             skippable:true,
 *                             buttons:["Cancel","Select","Delete"]
 *                          }
 *
 * @return {Boolean, Number} boolean if 1st arg is string, index of the clicked button if 1st arg is options object
*/

 new ExtendScript_Dialogs($.global, true);
 var killCheck = confirm("Should I do it?");// returns true/false

 // for more complex choices...
 var weapon = confirm({text:"Kill it with...", skippable:true, buttons:["Boredom","Fire","Hugs"]});
 // returns the index number of the button selected.
```
### Prompt
The prompt dialog has a variable-typed first parameter to either mimic native Prompt or add customization.

Options Object:
* title: title string
* text: static text string message
* defaultText: placeholder string in the input field
* multiline: if true, adds a multiline input instead of single line
* id: id number for retrieval from the cache
* skippable: whether or not to add a "Do not ask again" checkbox
* buttons: a string array that is converted to buttons on the bottom of the dialog. First is "dismiss" function and last is "confirm" by default.

```
/**
 * Replaces standard prompt with prettier and skippable ScriptUI version
 * @param {String, Object} args String of text or options object.
 *                          args = {
 *                             title:"Prompt",
 *                             text:"Message",
 *                             defaultText:"Example...",
 *                             multiline: true,
 *                             id:234,
 *                             skippable:true,
 *                             buttons:["Cancel","Select","Delete"]
 *                          }
 * @param {String} defaultText String to put as default in text field.
 *
 * @return {String} Input from text field
*/

 new ExtendScript_Dialogs($.global, true);
 var answer = prompt("Favorite ice-cream flavor?","Margarine");

 // for more complex choices...
 var bio = prompt({text:"Write your life story here...", defaultText:"It was the best of times, it was the blurst of times.", multiline:true});
```
### Dropdown
The dropdown dialog has a variable-typed first parameter as an array for quick use or as an options object to add customization.

Options Object:
* title: title string
* text: static text string message
* defaultText: placeholder string in the input field
* multiline: if true, adds a multiline input instead of single line
* id: id number for retrieval from the cache
* skippable: whether or not to add a "Do not ask again" checkbox
* buttons: a string array that is converted to buttons on the bottom of the dialog. First is "dismiss" function and last is "confirm" by default.

```
/**
 * Dialog with dropdown menu
 * @param {Array, Object} args Array of strings or options object.
 *                          options: {
 *                          title:"Confirm",
 *                          text:"Message",
 *                          id:234,
 *                          skippable:true,
 *                          menuItems:["Salmon","Tuna","Eel"],
 *                          buttons:["Cancel","OK"]
 *                          }
 *
 * @return {String} Selected menu item
*/

 new ExtendScript_Dialogs($.global, true);
 var fish = Dialogs.dropdown(["Salmon","Tuna","Eel"]);

 // for more complex choices...
 var hero = Dialogs.dropdown({text:"Favorite Robotroid Character...", menuItems:["Black", "Red", "Blue", "Green", "Yellow"]});
```

## Cached values and skippable dialogs
What if you are looping through a bunch of things and want to have a dialog pop up once with the option to not ask again? Or maybe just be able to retrieve a previous dialog selection?

Gotcha covered. Behold the somewhat clunky dialog cache!
### Cache set and clear
* `Dialogs.getCached(id)` retrieves the saved properties from an id.
If no argument is passed, it defaults to an id of -1.
Cached data
    * button: index number of which button was pressed to dismiss dialog
    * skippable: Boolean, if true, dialog should be skipped
    * value: the return value from the dialog (various types)
* `Dialogs.clearCached(id)` sets the saved properties from an id to undefined.
If no argument is passed, it defaults to an id of -1.


```
new ExtendScript_Dialogs($.global, true);
for (i=0;i<5;i++)
{
    var cached = Dialogs.getCached();
    if(cached && cached.skippable) {
        break;
    } else {
        alert({text:"You have been warned...", skippable:true});
    }
}
Dialogs.clearCached();

for (i=0;i<5;i++)
{
    var answer;
    var buttons = ["Naw","Meh","Yeah"];
    var cached = Dialogs.getCached(55);
    if(cached && cached.skippable) {
        answer = Dialogs.getCached(55).value;
        button = Dialogs.getCached(55).button;
    } else {
        answer = prompt({text:"Tedious input", id:55, skippable:true, buttons:buttons});
        button = Dialogs.getCached(55).button;
    }
    // could also get buttons and values down here with cache
    $.writeln(buttons[button]);
    $.writeln(answer);
}
Dialogs.clearCached(55);
```

# Disclaimer
This was hacked together and refactored a lot before it became an actual distributable thing so... it's not the prettiest.
Emphasis was on making something really easy to use out of the box with extra bonus features. Not efficiency or elegance.

Also, I'm not trying to solve every dialog problem in one API. If there's something more complex than single input, several buttons, it's probably best to just make a custom dialog.
