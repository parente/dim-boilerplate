Level 1: Define the World
=========================

Much of the effort in creating a game is spent defining the scenes, items, and events in the world JSON file. The world.json file resides in the webapp/data directory. The following sections describe its format by way of an example game in which the player needs to cook and serve dinner.

The World Array
---------------

The top element of the JSON file is the world array. The array contains objects describing elements of the game world. The object properties declared in the JSON file represent the state of the elements at the start of the game. As a player interacts with the game world, the contents of the original world array and its objects change.

.. code-block:: javascript

    [
        {
            /* a game world element */
            "type": "scene",
            "id": "garage"
        },
        {
            /* a second game world element */
            "type": "item",
            "id": "car"
        }
    ]

Every object in the world must have a *type* property and many must have an *id* property unique within its type. The following sections detail the different types and their associated properties.

.. note:: The JSON snippets below contain /\* comments \*/ for documentation purposes. They are not allowed in your actual JSON file according to the JSON standard. Including them may cause your game to fail to load.

Player
~~~~~~

The player object represents the state of the player. The player may hold zero or more items and reside in one scene at a time. The world array may contain one and only one player object.

.. code-block:: javascript

    {
        "type": "player",
        /* items the player is carrying */
        "items": ["groceries"],
        /* scene containing the player */
        "scene": "garage"
    }

Scenes
~~~~~~

A scene object describes a location in the game world. A scene may contain zero or more items and adjoin zero or more other scenes. A scene may have visual and aural properties describing how to appears to the player. The world array may contain any number of scenes.

.. code-block:: javascript

    {
        "type": "scene",
        /* unique scene id */
        "id": "kitchen",
        /* ids of items in the scene */
        "items": ["stove", "dishwasher"],
        /* ids of scenes connected to this one */
        "adjoins": ["living room"],
        /* visual properties of the scene */
        "visual": {
            /* text name */
            "name": "Kitchen",
            /* text description */
            "description": "A modern kitchen with a stainless steel stove, dishwasher, and refrigerator",
            /* background image */
            "backdrop": "image://backdrop/kitchen.png"
        },
        /* aural properties of the scene */
        "aural": {
            /* spoken name */
            "name": "sound://speech/kitchen",
            /* spoken description */
            "description": "sound://sound/kitchenDescription"
            /* looping ambiance */
            "backdrop": "sound://music/cookingMusic"
        },
        /* (optional) controller that activates when the user enters the scene */
        "controller": "dim/controllers/meta/explore"
    }

If the scene does not define a controller, the one specified in the :ref:`default object <defaults>` is used instead. The aural properties can contain :ref:`media URIs <media-sounds>` pointing to sound files or plain text to synthesize as speech.

Items
~~~~~

An item object describes an inanimate object in the game world. An item may have zero or more properties. An item may have visual and aural properties describing how to appears to the player. The world array may contain any number of items.


.. code-block:: javascript

    {
        "type": "item",
        "id": "stove",
        "visual": {
            "name": "Stove",
            "description": "It's a gas stove. It's off."
        },
        "aural": {
            "name": "sound://speech/stove",
            "description": "sound://speech/stoveOffDesc"
        },
        "properties": ["useable"]
    }

:doc:`Controllers <code>` give meaning to item properties. They have no impact on the game world on their own.

Events
~~~~~~

An event object describes changes made to the game world upon some player interaction with the world and how those changes are reported to the player. An event has three main properties:

#. *on*, an array of strings which state when the event fires
#. *exec*, an array of actions which change the state of the game world when the event fires
#. *report*, an array of visual and aural information to report to the player about the event

For example, the following example event fires when the player uses the stove item. When it fires, it disables the event from firing again and enables the *panToStove* event. It also changes the description of the *stove* object to explain that the stove is now lit. Finally, the event object visually describes what happens when the player uses the stove and narrates it as well.

.. code-block:: javascript

    {
        "type": "event",
        "id": "lightStove",
        /* conditions for triggering this event */
        "on": [
            "use",
            "stove"
        ],
        /* actions to take on the game world state when the event fires */
        "exec": [
            {
                /* sets a property arg0 to value arg1 */
                "action": "set",
                /* here, set the event.lightStove.disabled to true */
                "args": [
                    "event.lightStove.disabled",
                    true
                ]
            },
            {
                "action": "set",
                "args": [
                    "event.panToStove.disabled",
                    false
                ]
            },
            {
                "action": "set",
                "args": [
                    "item.stove.visual.description",
                    "The gas stove is hot and ready to cook."
                ]
            },
            {
                "action": "set",
                "args": [
                    "item.stove.aural.description",
                    "sound://speech/stoveOnDesc"
                ]
            }
        ],
        /* report to give after making the changes to the world */
        "report": [
            {
                "description": "You light the stove.",
                "narration": "sound://speech/lightStove"
            }
        ]
    }

The ability of events to affect the game world is limitless. The flexibility of the *on*, *exec*, and *report* properties make this possible. The next sections describe these properties in more detail.

On - Event Triggers
###################

The *on* array supports any number of strings of any value. All but two reserved strings are meaningless on their own. The rest are given meaning by the :doc:`game controllers <code>`.

Upon certain user interactions (e.g., taking an item, examining a scene, shooting a bad guy), a controller may ask the game world to evaluate its events to see if one matches an array of strings provided by the controller. The world returns any event object that has an *on* array matching the controller's array of strings. The controller may then fire any of the returned events.

For example, if a controller does the following:

.. code-block:: javascript

    var events = world.evaluate('use', 'stove');
    events.fires();

the event defined above will fire.

Two strings have special meaning in the *on* array. A single star, \*, matches any single string element in a controller provided array. For instance, if the world.json includes this event:

.. code-block:: javascript

    {
        "type": "event",
        "on": ["use", "*"]
    }

then it will match the first two controller calls below, but not the third:

.. code-block:: javascript

    world.evaluate('use', 'stove');
    world.evaluate('use', 'sink');
    world.evaluate('use', 'dishwasher', 'quickly');

Two stars, \*\*, at the end of the *on* array match any number of string elements. For instance, if the world.json includes this event:

.. code-block:: javascript

    {
        "type": "event",
        "on": ["use", "**"]
    }

then it will match all three controller calls above.

.. note::

    The boilerplate has controllers that evaluate and fire events matching the following patterns. If you want to support other events, add them to the world.json and write controllers to trigger them. See the :doc:`controllers <code>` section for details.

    * ["use", "<item id>"]
    * ["use", "<item id>", "<other item id>"]
    * ["move", "<scene id>"]
    * ["examine", "<item or scene id>"]
    * ["take", "<item id w/ takeable property>"]

Exec - Event Consequences
#########################

The *exec* array supports a variety of actions that modify the game world when the event fires. Each action takes a set of arguments which may have simple values, refer to names of properties in the world, or values of argument properties. The next sections explain the actions supported by the boilerplate, their arguments, and sample uses by way of example.

.. note:: The following sections define the actions as functions. In practice, they are used declaratively in the world JSON.

.. js:function:: append(target_ref, value)

    The append action adds a value to the end of an array declared in the world JSON.

    :param string target_ref: Name of the target array, referenced by its *type.id.property*
    :param any value: Value to append to the target array

    For example, the following action adds the item with the *pan* ID to the array of player items (i.e., adds the pan to the player inventory).

    .. code-block:: javascript

        {
            "action": "append",
            "args": ["player.items", "pan"]
        }

.. js:function:: del(target_ref)

    The del action deletes a property declared in the world JSON.

    :param string target_ref: Name of the target property, referenced by *type.id.property*

    For example, the following action deletes the *exec* property from the *panToStove* event (i.e., prevents the event from changing the game world again).

    .. code-block:: javascript

        {
            "action": "del",
            "args": ["event.panToStove.exec"]
        }


.. js:function:: publish(topic, arg1, arg2, ...)

    :param string topic:
    :param any args:

    .. code-block:: javascript

        {
            "action": "publish",
            "args": [
                "controller.request",
                "dim/controllers/puzzle/omlette"
            ]
        }

.. js:function:: remove(target_ref, value)

    :param string target_ref: Name of the target array, referenced by its *type.id.property*
    :param any value: Value to remove from the target array

    .. code-block:: javascript

        {
            "action": "remove",
            "args": ["scene.cupboard.items", "pan"]
        }

.. js:function:: set(target_ref, value)

    :param string target_ref: Name of the target property, referenced by its *type.id.property*
    :param any value: Value to set for the property

    .. code-block:: javascript

        {
            "action": "set",
            "args": ["item.pan.visual.description", "The pan is blazing hot!"]
        }


TODO: explain complex example using templates

.. code-block:: javascript

    {
        "type": "event",
        "id": "itemToStove",
        "on": [
            "use",
            "*",
            "stove"
        ],
        "exec": [
            {
                "action": "remove",
                "args": [
                    "player.items",
                    "{{{args.1.id}}}"
                ]
            },
            {
                "action": "append",
                "args": [
                    "scene.kitchen.items",
                    "{{{args.1.id}}}"
                ]
            },
            {
                "action": "append",
                "args": [
                    "item.{{{args.1.id}}}.properties",
                    "hot"
                ]
            }
        ],
        "report": [
            {
                "description": "You place the {{args.1.visual.name}} on the hot stove.",
                "narration": "sound://speech/{{{args.1.id}}}ToStove"
            }
        ]
    }

Report - Event Explainations
############################

TODO


.. _defaults:

Defaults
~~~~~~~~

TODO

.. code-block:: javascript

    {
        "type": "default",
        /* controller to use when scenes do not specify one */
        "controller": "dim/controllers/explore/explore",

        /* maps scene / item properties to output channels */
        "objectReport": {
            /* on user selection in a controller */
            "user.select": [
                {
                    "visual.name": "title",
                    "aural.name": "narration",
                    "aural.sound": "sound"
                }
            ],
            /* on user activation in a controller */
            "user.activate": [
                {
                    "visual.name": "title"
                }
            ]
        },

        /* defines channels for aural / visual rendering */
        "channels": {
            /* continuous, background audio channel */
            "ambience": {
                "type": "aural",
                "loop": True,
                /* does not stop if interrupted by the user, only if a sound with a different URI attempts is queued on the channel */
                "swapstop": True,
                /* volume gain, 0.0 to 1.0 */
                "gain": 0.15,
                /* crossfade when changing sounds */
                "crossfade": True
            },
            /* channel for brief sound effects */
            "sound": {
                "type": "aural",
                "gain": 0.7
            },
            /* channel for spoken narration */
            "narration": {
                "type": "aural",
                "gain": 0.9
            },
            /* channel for visual backgrounds */
            "backdrop": {
                "type": "visual"
            },
            /* channel for lengthy text descriptions */
            "description": {
                "type": "visual"
            },
            /* channel for brief text descriptions */
            "title": {
                "type": "visual"
            }
        }
    }

Controller Resources
~~~~~~~~~~~~~~~~~~~~

TODO

.. code-block:: javascript

    {
        "type": "ctrl",
        "id": "win",
        "report": [
            {
                "title": "You Win"
            }
        ]
    }

Media Assets
------------

TODO

.. _media-sounds:

Sounds
~~~~~~

TODO

Images
~~~~~~

TODO

Styles
~~~~~~

TODO