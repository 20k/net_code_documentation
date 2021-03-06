# net_code documentation

## SCRIPTING

All scripts should go in the ./scripts/yourusername folder. Scripts should be named "scriptname.js", and can be uploaded with #up scriptname. EG if I am logged in as user "example", I make hello_world.js in ./scripts/example, and #up hello_world

Scripts follow the format

    function(context, args)
    {
    
    }
    
All JS is es6+ by default, via quickjs

### Implemented scripts

\#hs.cash.balance()

\#ms.cash.xfer_to({user:"user", amount:number})

\#fs.cash.xfer_to_caller({amount:number})

\#hs.cash.expose({user:"user"}) -> shows you the amount of cash a target has, as well as how much you can steal

\#ms.cash.steal({user:"user", amount:number}) -> steals cash from a target with a breached breach node

\#fs.scripts.get_level({name:"user.scriptname"})

\#fs.scripts.core() -> takes optional {array:1} parameter

\#ms.scripts.me() -> takes optional {array:1} parameter

\#fs.scripts.public -> takes optional {array:1} or optional {sec:number}

\#hs.msg.send({channel:"name", msg:"message"})

\#hs.msg.tell({user:"name", msg:"hello"}) -> sends a private message to a user

\#ms.msg.recent({channel:"name"}) -> channel defaults to "0000", takes optional {count:number} or {array:1}. Additionally takes {tell:true} to retrieve tells instead

\#ms.channel.join({name:"some_name"}) -> joins a chat channel, optionally takes {password:"some_password"}. Name must be alphanumeric (including '_') and is limited to 16 characters

\#ms.channel.leave({name:"some_name"}) -> leaves a chat channel

\#ms.channel.create({name:"some_name"}) -> creates a chat channel, optionally takes {password:"some_password"}

\#ms.channel.list() -> lists current chat channels, takes optional {array:1} to return an array

\#ns.users.me() -> takes optional {array:1} argument

\#ls.item.steal({user:"target", idx:0}) -> steals the user's item at idx:id, costs cash which you can confirm with {confirm:true}. Also accepts an array of indices

\#hs.item.expose({user:"target"}) -> lists a users current items if they are breached, as well as how many items you can steal from them

\#ls.item.xfer_to({user:"user", idx:item_index}) -> also accepts an array of indices

\#ms.item.list() -> takes optional {array:1} or {full:1} (which displays detailed info)

\#ms.item.load({idx:item_index}) -> also accepts an array of indices

\#ms.item.unload({idx:item_index}) -> also accepts an array of indices

\#ls.item.bundle_script({name:"scriptname", idx:0, tag:"shrt_tag"}) -> inserts the source of a script (host.scriptname) into a bundle at idx:id. Tag modifies the item name, must be 8 characters or less

\#ls.item.cull({idx:0}) -> unloads and deletes the item at idx:id. Also takes an array of indices

\#ns.item.register_bundle({name:"arbitraryname", idx:0}) -> registers a bundle at idx:id to be run as host.arbitraryname()

\#ls.item.configure_on_breach({idx:0, name:"i20k.script_name"}) -> idx refers to an on_breach item, and name can be one of the following: Blank, in which case it defaults to host.on_breach, a string with no host, in which case it defaults (dynamically and always) to host.string, and script.name, in which case that script name is always called

\#ls.nodes.manage() -> displays nodes and attached locks. Is no longer used for equipping locks, use #items.manage

\#ls.log.expose({user:"user", NID:id}) -> takes optional {array:1}. Must have a clear breach path to the node in question

\#ls.net.view({user:"name", n:5}) -> views the network connections associated with a user or npc, gives position as well. In array:1 mode, returns an array where each member is an object with the properties name, x, y, z, links, stabilities

\#fs.net.hack({user:"name", extra_args:"example"}) -> hacks a user at user:"name", passes args forwards

\#ns.net.switch({user:"name"}) -> switches your terminal input to be run through this npc instead. Currently only works for npcs, and your main user that you are running on (from the user <username>) command

\#ns.net.path({user:"name", target:"destination", min_stability:23}) -> returns the visible path from user to target which has an optional minimum link stability. The stability calculated is the most direct path, not the visible path. In array:1 mode, returns {path:array, total_stability:num, avg_stability:num}

\#ls.sys.map({n:-1, w:160, h:80, centre:false}) -> gives a map of every system in the game. The parameter n specifies how many hops out from your current system to show, and centre can be used to centre the camera. In array:1 mode returns an array of {name:"name", x:float, y:float, z:float, links:array}

\#ls.sys.view({user:"name", w:80, h:40, scale:0.5, fit:false, n:-1}) -> generates a map of the local system. all parameters are optional. Without a user arg, it will return the overall system view, with a user arg it will display the currently viewable links from that user. Scale sets the overall zoom level, fit fits the view into the minimum space possible, and n specifies how many hops from the current user to show. In array:1 mode, returns an array like [{name:"name", x:float, y:float, z:float, links:array, stabilities:array}]

\#ns.sys.move({to:"name", stop:false, queue:false, fraction:1, offset:0}) -> moves the current user to the target user. Stop may be used to stop movement. Queue sets whether or not to move immediately, or queue after the current set of moves are finished. Fraction sets the fraction of how much move, and offset sets an offset in units to stop before when moving. In array:true mode returns {current:{x:float, y:float, z:float, timestamp_ms:time_in_ms}, queue:[queue_type]}. queue_type is an object that is one of two formats. 1 is {type:"move", x:float, y:float, z:float, timestamp_ms:timestamp, finish_in_ms:timestamp} and 2 is {type:"activate", system_to_arrive_at:"system_name"}

\#ns.sys.access({user:"name"}) -> accesses the control panel for a user or npc. Can be used to travel between systems on a special npc. Also used to modify network links

\#ms.sys.limits() -> shows you the limits in place due to seclevels, EG you may send less cash to a fullsec system vs nullsec systems. Takes optional user:"name" or sys:"name" to view specific limits

### Realtime Scripting

To set a script to be realtime, use

`set_is_realtime_script();`

The script then is set into realtime mode. There are 5 callbacks that you can return:

on_draw, on_update(dt), on_input(char, is_repeated), on_textinput(str), and on_resize(dim). Dim is an object with a width and height property, in character sized units

For every 16ms you get 4ms of processing. You can terminate a realtime script with control-c, or by closing the associated window

To exit realtime script mode, use `terminate_realtime();`, and to query realtime script mode use `is_realtime_script();`

`set_close_window_on_exit();` may be used to automatically close the client script window on terminating a realtime script

`set_start_window_size({width:50, height:25});` will set the starting width and height of the window in character sized units

`is_key_down(char)` may be used to test if a key is down. Uses the love2d naming convention https://love2d.org/wiki/KeyConstant but bear in mind that not all keycodes are implemented yet, noteably any keycodes that would require shift to be pressed, numpad keys, F keys, and caps/scroll/numlock. F keys and *lock keys will never be supported, numpad is a maybe, and shift+1 keys are on the todo list. This also supports mouse clicks under the constants "lmouse", "mmouse" and "rmouse" 

`set_realtime_framerate_limit(30)` may be used to set a framerate limit. Valid ranges are 1-60fps

`set_is_square_font(1)` may be used to change the font type from regular to square

For an example script, look here https://pastebin.com/uHDfhp0k

An example for many of the imgui functions can be found here: https://pastebin.com/8d3jsZCX

### Realtime Scripting: ImGui

Net\_code\_ supports a js wrapper around ImGui, allowing you to build your own custom uis in realtime scripts that users can interact with on the client. There are two key deviations from the standard ImGui API here

Pt 1. Javascript does not support reference semantics. You must use an object for 'referency' parameters. These objects are simply {v:some_value}, or can be manipulated with the helper functions var obj = imgui.ref(val) or imgui.get(obj);

For example:

```js
///equivalent to 
//var input_buffer = {v:""}
var input_buffer = imgui.ref("");

function on_draw()
{
    if(imgui.inputtext("Input Text", input_buffer))
    {
        ///equivalent to
        //var actual_string = input_buffer.v;
        var actual_string = imgui.get(input_buffer);
        
        async_print("Client modified input buffer " + actual_string);
        ///modifying actual_string would not affect the contents of input_buffer
    }
}
```

Pt 2. Where raw ImGui uses the convention:

```c++
if(ImGui::TreeNode("unique_id"))
{
    ImGui::Text("Hello!");
    ImGui::TreePop();
}

//or

if(ImGui::BeginDragDropSource())
{
    ImGui::SetDragDropPayload("filt", "your_data_here");
    ImGui::EndDragDropSource();
}
```

Net\_code\_ uses the convention:

```js
if(imgui.treenode("Unique_id"))
{
    imgui.text("Hello");
}

imgui.treepop(); ///always called

if(imgui.begindragdropsource())
{
    imgui.setdragdroppayload("filt", "your_data_here");
}

imgui.enddragdropsource(); ///always called
```

This allows you to hide the network latency in some situations, by omitting the if statement check. The following code is exactly equivalent, trading bandwidth for client responsiveness:

```js
imgui.treenode("Unique_id");
imgui.text("Hello"); ///the client ignores this unless the above treenode returns true 
imgui.treepop();

imgui.begindragdropsource();
imgui.setdragdroppayload("filt", "your_data_here"); ///this is ignored unless begindragdropsource() returns true on the client
imgui.enddragdropsource();
```

This deviation from the regular api is important to maximise the responsiveness of imgui on the client

#### API Reference

The API is still a work in progress, flags are generally not currently support because of sandboxing difficulties. Any type which is named \*_ref expects an object of the format {v:value}, that may be modified by the function

Labels, ids, and strs, are all strings. Colours are values between 0 and 1 in the sRGB colour space

`imgui.text(str)`

`imgui.textcolored(r, g, b, a, str)` -> colours are in sRGB, between 0 and 1

`imgui.textdisabled(str)` -> gray text

`imgui.bullettext(str)` -> shortcut for bullet() + text()


The following items return true when clicked or the value is changed. They also work with the isitem** functions

`imgui.button(label, width=0, height=0)` -> simple button

`imgui.smallbutton(label)` -> button with no framepadding

`imgui.invisiblebutton(id, width, height)` -> an invisible button, useful for building custom widgets

`imgui.arrowbutton(id, direction)` -> accepts directions 0, 1, 2, 3 for left, right, up, and down respectively

`imgui.checkbox(label, value_ref)` -> checkbox

`imgui.radiobutton(label, is_active)` -> radiobutton

`imgui.progressbar(fraction, width=0, height=0, overlay="")`

`imgui.bullet()` -> Draws a small circle, and keeps the cursor on the same line

The following items return true when the value is changed by the client. Dragfloats can be ctrl-clicked to turn them into an input box


`imgui.dragfloat(label, v_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragfloat2(label, v1_ref, v2_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragfloat3(label, v1_ref, v2_ref, v3_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragfloat4(label, v1_ref, v2_ref, v3_ref, v4_ref, v_speed=1, v_min=0, v_max=0)`



`imgui.dragint(label, v_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragint2(label, v1_ref, v2_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragint3(label, v1_ref, v2_ref, v3_ref, v_speed=1, v_min=0, v_max=0)`

`imgui.dragint4(label, v1_ref, v2_ref, v3_ref, v4_ref, v_speed=1, v_min=0, v_max=0)`



`imgui.sliderfloat(label, v_ref, v_min, v_max)`

`imgui.sliderfloat2(label, v1_ref, v2_ref, v_min, v_max)`

`imgui.sliderfloat3(label, v1_ref, v2_ref, v3_ref, v_min, v_max)`

`imgui.sliderfloat4(label, v1_ref, v2_ref, v3_ref, v4_re, v_min, v_max)`

`imgui.sliderangle(label, vrad_ref, v_degrees_min=-360, v_degrees_max=360)`

`imgui.sliderint(label, v_ref, v_min, v_max)`

`imgui.sliderint2(label, v1_ref, v2_ref, v_min, v_max)`

`imgui.sliderint3(label, v1_ref, v2_ref, v3_ref, v_min, v_max)`

`imgui.sliderint4(label, v1_ref, v2_ref, v3_ref, v4_re, v_min, v_max)`



`imgui.inputtext(label, v_text_ref)`

`imgui.inputtextmultiline(label, v_text_ref)`

`imgui.inputfloat(label, v_ref)`

`imgui.inputfloat2(label, v1_ref, v2_ref)`

`imgui.inputfloat3(label, v1_ref, v2_ref, v3_ref)`

`imgui.inputfloat4(label, v1_ref, v2_ref, v3_ref, v4_ref)`

`imgui.inputint(label, v_ref)`

`imgui.inputint2(label, v1_ref, v2_ref)`

`imgui.inputint3(label, v1_ref, v2_ref, v3_ref)`

`imgui.inputint4(label, v1_ref, v2_ref, v3_ref, v4_ref)`

`imgui.inputdouble(label, v1_ref)`


`imgui.coloredit3(label, r_ref, g_ref, b_ref)`

`imgui.coloredit4(label, r_ref, g_ref, b_ref, a_ref)`

`imgui.colorpicker3(label, r_ref, g_ref, b_ref)`

`imgui.colorpicker4(label, r_ref, g_ref, b_ref, a_ref)`


`imgui.colorbutton(label, r_ref, g_ref, b_ref, a_ref, unused=0, width=0, height=0)`

The following functions have miscellaneous return values


`imgui.treenode(label)` -> begins a new tree node element. Must always call imgui.treepop(). Returns true when open. If false, all imgui calls are ignored until the next call to treepop()

`imgui.treepush(label)` -> calls indent() + pushid() (pushid is unavailable in the scripting environment currently). Automatically called by treenode when true, if you use this you must call imgui.treepop(). No return value

`imgui.treepop()` -> no return value. Must always be called after at treenode, or a treepush

`imgui.collapsingheader(label)` -> returns true when the header is open. Does not indent, does not need treepop. 

`imgui.setnextitemopen(is_open)` -> sets whether or not the next tree element is open

The treenode API can be used like this:

```js
if(imgui.treenode("Unique_id"))
{
    imgui.text("Hello");
}

imgui.treepop();
```

or like this

```js
imgui.treenode("Unique_id")
imgui.text("Hello");
imgui.treepop();
```

These are functionally exactly the same. The only difference is that in the first situation, imgui.text is only sent when the server has acknowledged the treenode being opened. In the second situation, the text is always sent, resulting in effectively no latency at the expensive of more bandwidth usage


`imgui.selectable(label, is_selected, unused, width=0, height=0)` -> returns true if clicked

`imgui.selectable(label, is_selected_ref, unused, width=0, height=0)` -> returns true if clicked. This function is overloaded depending on whether or not the second parameter is a reference


`imgui.listbox(label, current_item_ref, items, height_in_items=-1)` -> items is an array of strings. current_item_ref indexes this array. Returns true when current_item_ref changes

`imgui.plotlines(label, values, value_offset=0, overlay_string="", scale_min=FLT_MAX, scale_max=FLT_MAX, width=0, height=0)` -> values is an array of numbers

`imgui.plothistogram(label, values, value_offset=0, overlay_string="", scale_min=FLT_MAX, scale_max=FLT_MAX, width=0, height=0)` -> values is an array of numbers


`imgui.pushstylecolor(idx, r, g, b, a)` -> Unfortunately there are not numeric constants available for this, so you need to look up the numeric values in imgui. This will be fixed in the future

`imgui.popstylecolor(num=1)`


`imgui.pushitemwidth(item_width)` -> pushes item width

`imgui.popitemwidth()`

`imgui.setnextitemwidth(item_width)` -> sets the next item width


`imgui.separator()` -> horizontal separator

`imgui.sameline(offset_from_start=0, spacing=-1)` -> horizontal layout for widgets

`imgui.newline()`

`imgui.spacing()`

`imgui.dummy(width, height)`

`imgui.indent(indent_w=0)`

`imgui.unindent(indent_w)`

`imgui.begingroup(id)` -> treats a group of widgets or items as one megawidget, eg calling isitemhovered on a group will return if any item in the bounding box of the group is hovered 

`imgui.endgroup()`

All the following functions return booleans

`imgui.isitemhovered()` -> is the last item hovered

`imgui.isitemactive()` -> is the last item active

`imgui.isitemfocused()` -> is the last item focused

`imgui.isitemclicked()` -> was the last item just clicked

`imgui.isitemvisible()` -> is the last item visible

`imgui.isitemedited()` -> did the last item just modify its underlying value, or was it clicked

`imgui.isitemactived()` -> was the last item just made active

`imgui.isitemdeactivated()` -> was the last item just made inactive

`imgui.isitemdeactivatedafteredit()` -> was the last item just made inactive, and made an underlying value change when it was active

`imgui.istoggledopen()` -> was the last items open state toggled, for treenode()

`imgui.isanyitemovered()` -> is any item in this realtime script hovered. Currently quite performance hungry

`imgui.isanyitemactive()` -> is any item in this realtime script active. Currently quite performance hungry

`imgui.isanyitemfocused()` -> is any item in this realtime script focused. Currently quite performance hungry

Drag and drop, source:

`imgui.begindragdropsource()` -> Starts the last widget as a drag and drop source. If false, all imgui calls are ignored until the next call to imgui.enddragdropsource(). Returns true during a drag and drop operation

`imgui.setdragdroppayload(filter_unimplemented_string, data)` -> must be called between begindragdropsource() and enddragdropsource(). Sets the current drag and drop element to data

`imgui.enddragdropsource()` -> finishes a drag and drop source

The following two examples are equivalent

Example 1. 

```js
imgui.button("Source");

if(imgui.begindragdropsource())
{
    imgui.setdragdroppayload("none", "some_data");
    imgui.text("Currently dragging and dropping something")
}

imgui.enddragdropsource();
```

Example 2. 

```js
imgui.button("Source");

imgui.begindragdropsource())
imgui.setdragdroppayload("none", "some_data");
imgui.text("Currently dragging and dropping something")
imgui.enddragdropsource();
```

Drag and drop, destination

`imgui.begindragdroptarget()` -> Starts the last widget as a drag and drop target. Returns true repeatedly when a drop operation is in progress. Must always call enddragdroptarget() afterwards

`imgui.acceptdragdroppayload()` -> returns null when no item is dropped, or a string when an item is dropped. Must be called begin begindragdroptarget() and enddragdroptarget()

`imgui.enddragdroptarget()` -> finishes a drag drop target

The following two examples are equivalent functionally

Example 1.

```js
imgui.button("Target");

if(imgui.begindragdroptarget())
{
    var res = imgui.acceptdragdroppayload("none");

    if(res != null)
       async_print("DRAGDROP " + res);
}

imgui.enddragdroptarget();
```

Example 2.

```js
imgui.button("Target");

imgui.begindragdroptarget();

var res = imgui.acceptdragdroppayload("none");

if(res != null)
   async_print("DRAGDROP " + res);

imgui.enddragdroptarget();
```

There is an extra network roundtrip and script run in example 1 and only minimal extra bandwidth in example 2, which makes it essentially an upgrade. Do not create other widgets between a begindragdroptarget() and an enddragdroptarget() pair

`imgui.ref(val)` -> takes a copy of val, and produces an object {v:val}. Any function which needs to modify its argument uses this convention

`imgui.get(obj)` -> takes an object, and returns the underlying value

### Autocompletes

You may specify a scripts autocompletes like #autos(test:1, test2:"hello", test3:"bitconnect");. The parser isn't that fancy so you may end up with slightly incorrect behaviour if you do eg #autos(test:1, test2:"test1:");

Warning: You MUST terminate an #autos statement with a semicolon otherwise itll break

### DB

\#db.f({example:"query"}) -> returns a cursor, use .array() or .first() to get the results. Takes an optional second projection argument

\#db.u({doot:{$exists:true}}, {$set:{doot:"nope"}}) -> finds all documents which have a key doot, and sets their key doot to 'nope'

\#db.i({key:"something", key2:"somethingelse"}) -> inserts a new document, returns what was inserted

\#db.r({key:something}) -> deletes all documents that have a key:something

There is additionally a db object under $db. This exposes the structure of the db directly. It is an array of objects (eg $db[0] and $db[1])

$db[0].some.thing.$fetch() -> gets the value of the key some.thing out of the db in slot 0

$db[0].some.thing.$delete() -> deletes the value of some.thing from slot 0

$db[0].some.thing = "hello" -> sets some.thing to be "hello"

$db[1] = {x:12, y:13} -> creates a new entry in db[1] and sets it to {x:12, y:13}

This db object is generally intended for simple usage to treat the entire db as if it were a single object. Root db indices must be numbers (eg you cannot have $db["hello"])

### Misc

\#D("hello_there") -> overwrites your return value with any #D strings, which are appended together with newlines, works even if the script breaks except for timeouts but will overwrite errors. Only works for caller

print("some_string") -> gets printed onto the terminal prior to any return values, works even if the script breaks except for timeouts, will not overwrite errors. Works for everyone

async_print("some_string") -> Dynamically and asynchronously sends a string to the client. Works correctly with realtime scripts unlike print(). Lightly ratelimited to prevent server abuse

timeout_yield(); -> cooperatively terminates the script if past the execution timeout. Print and #D only give output if the script is cooperatively terminated, which includes calling any function whatsoever, including any script, *s_call, db functions, and misc functions. The only method by which to get non cooperative termination is to timeout in pure JS code after 6 seconds, instead of the usual 5s timeout cap

### Other (non scriptable)

\#up scriptname -> will upload a script to the server

\#dry scriptname -> will test run a script upload to the server but not commit the changes

\#private scriptname -> will make a script private

\#public scriptname -> will make a script public

\#remove scriptname -> will remove a script from the server

\# -> lists all local scripts for that user

\#dir -> opens the script directory (shared between users)

\#edit scriptname -> creates or opens a script for editing, defaults to es6

\#edit_es6 scriptname -> creates or opens a script in es6/typescript mode. Will convert an es5 file to es6

\#edit_es5 scriptname -> same as above, but will convert an es6 file to es5

\#open scriptname -> opens a file for editing, but does not create

\#clear_autos -> clears autocompletes

\#shutdown -> shuts down the client

\#cls -> clears the terminal and chat

\#clear_term -> clears the main terminal window

\#clear_chat -> clears the main chat window

user \<username\> -> changes user, create automatically (will be changed in a future update)

\#delete_user \<username\> -> deletes a user, does not confirm or warn

### Calling Scripts from a String

While \#ns.script.name() is the most straightforward way to call a hardcoded script, its also possible to call a script from a string or variable

When you call \#ns.script.name(), it expands to ns_call("script.name")(). ns_call("script.name") returns a function object and does not call the function itself, so you may do var x = ns_call("script.name"); x()

If your script is lowsec, all higher seclevel functions are available, eg. If you call ls_call, ls_call, ms_call, hs_call, and fs_call are available, but your script must be nullsec for ns_call to be available (so you cannot eval ns_call without at least one ns_call statement being made available to the parser as a regular statement)

The *s_call series of functions take a second boolean argument to indicate if the script should be launched asynchronously. You may not get the return value of an asynchronous script launch, or pass it arguments

Example:

    ///this script is nullsec due to ns_call
    function(c, a)
    {
        ns_call("i20k.some_nullsec", true)(); //launches some_nullsec asynchronously
        return hs_call("i20k.highsec")(); //dont forget the second set of ()s!
    }

Additionally, ofs_call, ohs_call, oms_call, ols_call, ons_call, os_call, as well as the prefixes #os., #ofs., #ohs., #oms., #ols., and #ons. are available. These call another script but with the caller set to the person hosting the script (ie you, if you are uploading it, or if you call #i20k.name() then that script using #os.script.name() would set the caller to i20k). Eg, #os.cash.xfer_to({user:context.caller, amount:1}) will send one cash from the scripts host, to the person calling the script. These are all fullsec statements, with the o*s variants being provided for convenience
    
## GENERAL

The console prompt you're given is a raw JS terminal, essentially of the form "return \<input\>". This means that if you enter 1+1, you get 2

Scripts are run through eg #fs.script.name(), or #script.name() which maps to nullsec

### The Sandbox

Due to the way its implemented, there are no enforced restrictions in what you can do in the sandbox, you should not be able to edit the global object in a meaningful way. __proto__, getproto, setproto, and constructor have all been removed to prevent global object references leaking across scripts

If you are able to mess with any of the global properties in a way that carries over into another script, let me know


## API

The official client uses websockets on 6770 with TLS to netcodegame.com - the server will respond back to you in binary mode

It is also strongly recommended that you enable compression, as realtime scripts generate a lot of highly compressible traffic. This will probably be enforced in the future to save server bandwidth

The server will automatically disconnect you after 30s of inactivity. Any message sent or received to/from a client will reset the timer

#### General Networking

Every request is in json format, as described below. Every json object has a .type property, giving a string of possible types

The requests and responses are listed by the .type property. Eg a request of type "autocomplete_request", with a data parameter of "i20k.mandelbrot" would result in a final json object of {type:"autocomplete_request", data:"i20k.mandelbrot"}. 

### Client -> Server

At least one auth method is required. To get a key token for 2. or 3., you must #dl_auth it. You must be authenticated to retrieve a key token, aka the only way to get one is to have authed successfully at least once through steam and downloaded it

## Auth

```
Type 1:
.type -> steam_auth
.data -> encrypted app token as hex (make sure you get the endianness right, you may need to swap)
```
```
Type 2:
.type -> steam_auth
.data -> encrypted app token as hex, with a hex auth token embedded in the request. Useful for tying a steam account to a specific auth token
```
```
Type 3:
.type -> key_auth
.data -> key token. You must use the token supplied previously by the server
```
## Other
```
Executes a generic user supplied command on the server, will get a response when the command completes of type server_msg
.type -> generic_server_command
.data -> "some string", eg "user i20k" or "#i20k.mandelbrot()"
[optional] .tag -> the .tag property is returned as-is in the server response

Note: Scripts are uploaded with the format {type:"generic_server_command", data:"#up_es6 scriptname raw_script_data"}. You may replace #up_es6 with #up for es5 scripts for faster parsing
```
```
Executes a client chat command. By default does not fetch a response from the server
.type -> client_chat
.data -> "some string", eg "#msg.send({channel:\"global\", msg:\"hello\"})"
[optional] .respond -> set to 1 to get a server response of the type chat_api_response
```
```
Requests the autocompletes for a script
.type -> autocomplete_request
.data -> "script.name", eg "i20k.mandelbrot"
```
```
Terminates one or all realtime scripts
.type -> client_terminate_scripts
.id -> either -1 to terminate all, or a numeric id to terminate a specific realtime script
```
```
Sends keyboard input information to realtime scripts
.type -> send_keystrokes_to_script
.id -> numeric id of a realtime script
.input_keys -> keys that have been input, as for text entry, in utf8. Array of strings, eg ["a", ";", "/", " "]
.pressed_keys -> keys that have been pressed down, including mouse buttons, only needs to be updated when it happens. Uses love2d conventions
.released_keys -> same as above but for keys that have just been released
```
```
Sends mouse information to realtime scripts
.type -> update_mouse_to_script
.id -> numeric id of a realtime script
.mouse_x -> mouse x position relative to the left hand side, in 'character sized' units. Eg if your font has a 30 pixel width, and your mouse is 60 pixels from the left of your content area, mouse_x is 2 characters from the left
.mouse_y -> same as above, y's origin is from the top
.mousewheel_x -> horizontal mousewheel
.mousewheel_y -> vertical mousewheel
```
```
Updates realtime script generic information
.type -> send_script_info
.id -> numeric id of a realtime script
.width -> width in character sized units
.height -> height in character sized units
```

### Server -> Client
```
Generic server response
.type -> server_msg
.data -> response string, should be displayed to the user
[optional] .tag -> tag value, as possibly sent in generic_server_command
[optional] .pad -> if not present, or set to 0, add a newline to the end of the response. TODO: unsure if this is really still used or valid
[optional] .authenticated -> if present and set to 1, auth has been accepted. Only given once upon a successful auth
```
```
Realtime script info
.type -> command_realtime
.id -> numeric id of a realtime script
[optional] .width -> width in character sized units. Eg if set to 3, and your font is 10 pixels wide, your window should be 30 pixels wide
[optional] .height -> see .width
[optional] .close -> realtime script window should be closed
[optional] .msg -> window contents
```
```
Chat api info
.type -> chat_api
.channels -> array of strings, joined channels
.tells -> array of json objects, where the json objects each contain .user and .text
.notifs -> server notifications, array of strings
.data -> array of json objects, where the json objects each contain .channel and .text
.root_user -> base executing user, useful if you've tunneled through an npc
.user -> currently executing user, eg your main user or an npc
```
```
Script args response
.type -> script_args
.script -> script name, eg "i20k.mandelbrot"
.keys -> array of script keys
.vals -> array of script key values, this array is exactly the same length as .keys
```
```
Script args invalid response -> you requested an invalid script, or a bad format
.type -> script_args_invalid
.script -> script name, eg "i20k.mandelbrot"
```
```
Script args ratelimit response -> ratelimit is currently 1/s
.type -> script_args_ratelimit
.script -> script name, eg "i20k.mandelbrot"
```
```
Server ping -> ignore, or use to detect server death
.type -> server_ping
```
```
Script download
.type -> script_down
.name -> script name, eg "i20k.mandelbrot"
.data -> script contents
```
```
Chat api response. Indended to be a response to commands like /leave and /join, see client_chat with respond:1
.type -> chat_api_response
.data -> server message
```
```
Key auth download, from #dl_auth while authenticated. Can be used as type 3 authentication
.type -> auth
.data -> hex key token
```