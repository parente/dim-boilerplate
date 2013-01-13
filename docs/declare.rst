Level 1: Define the World
=========================

Much of the effort of creating a game is in defining the scenes, items, and events in the world JSON file. The world.json file resides in the webapp/data directory. The following sections describe its format by way of an example game in which the player needs to cook and serve dinner.

The World Array
---------------

The top element of the JSON file is the world array. The array contains objects describing elements of the game world. The object properties declared in the JSON file represent the state of the elements at the start of the game. As user interacts with the game world, the contents of the original world array and its objects may change.

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

.. note:: The JSON snippets below contain /* comments */ for documentation purposes. They are not allowed in your actual JSON file according to the JSON standard. Including them may cause your game to fail to load.

Player
~~~~~~

The player object represents the state of the player. The world array may contain one and only one player object.

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

An item object describes a

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

Events
~~~~~~

.. code-block:: javascript

    {
        "type": "event",
        "id": "lightStove",
        "on": [
            "use",
            "stove"
        ],
        "exec": [
            {
                "action": "set",
                "args": [
                    "event.lightStove.disabled",
                    true
                ],
                "action": "set",
                "args": [
                    "event.panToStove.disabled",
                    false
                ],
                "action": "set",
                "args": [
                    "item.stove.visual.description",
                    "The gas stove is hot and ready to cook."
                ],
                "action": "set",
                "args": [
                    "item.stove.aural.description",
                    "sound://speech/stoveOnDesc"
                ]
            }
        ],
        "report": [
            {
                "description": "You light the stove.",
                "narration": "sound://speech/lightStove"
            }
        ]
    }


.. code-block:: javascript

    {
        "type": "event",
        "id": "panToStove",
        "on": [
            "use",
            "pan",
            "stove"
        ],
        "exec": [
            {
                "action": "remove",
                "args": [
                    "player.items",
                    "pan"
                ]
            },
            {
                "action": "append",
                "args": [
                    "scene.kitchen.items",
                    "pan"
                ]
            }
        ],
        "report": [
            {
                "description": "You place the pan on the hot stove.",
                "narration": "sound://speech/panToStove"
            }
        ]
    }


.. _defaults:

Defaults
~~~~~~~~

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

.. _media-sounds:

Sounds
~~~~~~

Images
~~~~~~

Styles
~~~~~~