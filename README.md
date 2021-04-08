

 Godot Lua Module
===============
**Table of contents:**
  * [About](#about)
  * [Features](#features)
  * [TODO](#todo)
  * [Compiling](#compiling)
  * [Examples](#examples)
  * [Contributing And Feature Requests](#contributing-and-feature-requests)

About
-------
This is a Godot engine module that adds lua support via GDScript. Importantly this is **NOT** meant to be a replacment for GDScript. The main purpose of this module is to add runtime execution of code for tasks such as modding or in game scripting. 

While the original purpose of this module was for my own use I understand more may find it useful. If a feature is missing that you would like to see feel free to create a [Feature Request](https://github.com/Trey2k/lua/issues/new?assignees=&labels=feature%20request&template=feature_request.md&title=) or submit a PR 

By default lua executes on its own detached thread. If thread saftey is required you can either use mutex locks or disable threading.

To use you can either [Compile from source](#compiling) or if you are on windows or linux you can download one of the pre built binarays from th [releas](https://github.com/Trey2k/lua/releases) page.

**Default lua libs loaded**
- base
- math
- string
- table

Features
--------------------------------
- Run lua directly from a string or a text file.
- Push any Variant as a global.
- Expose GDScript functions to lua with a return value and up to 5 arguments.
- Call lua functions from GDScript.
- By default the lua print function is set to print to the GDEditor console. This can be changed by exposing your own print function as it will overwrite the existing one.
- Basic types are passed as userdata (currently: Vector2, Vector3 and Color) with a useful metatable. This means you can do things like:  
```lua
local v1 = Vector2(1,2)
local v2 = Vector2(100,100)
print( v2.x ) -- "100"
print( v1+v2 ) -- "(101,102)"
change_my_sprite_color( Color(1,0,0,1) ) -- if "change_my_sprite_color" was exposed, in GDScript it will receive a Color variant.
```

TODO
-----
- Add support to kill individual lua threads.
- Add option to load specific lua librarys.
- Methods for userdata types, i.e. `.length()` for Vector2
- Object type passed as userdata? This probably should be optional. User should be careful with reference being freed

Compiling
------------
  Please note this module was written for **Godot Version 3.2.3**
- Start by cloning the Godot 3.2.3 [source](https://github.com/godotengine/godot/tree/3.2.3-stable) with this command `git clone https://github.com/godotengine/godot -b 3.2.3-stable`

- Next clone this repository into the module folder inside the Godot source code.

- Now you can follow the Godot build instructions on their [site](https://docs.godotengine.org/en/latest/development/compiling/).

Examples
------------
**Running lua from a string:**
```
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.doString("for i=1,10,1 do print('Hello lua!') end")
```
<br />

**Running lua from a file:**
```
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.doFile("user://luaFile.lua")
```
<br />

**Pushing a Variant as a global:**
```
extends Node2D

onready var lua = Lua.new()
var test = "Hello lua!"

func _ready():
	lua.pushVariant(test, "str")
	lua.doString("print(str)")
```
<br />

**Exposing a GDScript function to lua:**
```
extends Node2D

onready var lua = Lua.new()

func luaAdd(a, b):
	return a + b

func _ready():
	lua.exposeFunction(self, "luaAdd", "add")
	lua.doString("print(add(2, 4))")
```
<br />

**Calling a lua function from GDScript:**
```
extends Node2D

onready var lua = Lua.new()

func _ready():
	lua.doFile("user://luaFile.lua")
	if( lua.luaFunctionExists("set_colours") ):
		lua.callFunction( "set_colours", ["red", "blue"])
```
<br />

**Capturing lua errors:**
```
extends Node2D

onready var lua = Lua.new()

func luaCallBack(err):
	print(err)

func _ready():
	lua.doString("print(This wont work)", true , self, "luaCallBack")
```
<br />

**Disable threading:**
```
extends Node2D

onready var lua = Lua.new()


func _ready():
	lua.setThreaded(false)
	lua.doString("while true do print("The entire game will freeze") end" )
```
<br />

**Kill all lua threads:**
```
extends Node2D

onready var lua = Lua.new()


func _ready():
	lua.doString("while true do pass end")
	lua.killAll()
```
Contributing And Feature Requests
---------------
All contributions are welcome, if you would like to contribute submit a PR.
<br />
Additionally if you do not have the time and or the knowledge you can create a [Feature Request](https://github.com/Trey2k/lua/issues/new?assignees=&labels=feature%20request&template=feature_request.md&title=)
