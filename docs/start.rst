Get Started
===========

Understand the Capabilities
---------------------------

The dim-boilerplate project provides the following features out-of-the-box:

#. Declarative JSON markup for defining game world scenes, items, and events.
#. Exploration of the game world via arrow key or gesture-driven menus.
#. Game world interactions including examining, moving, taking, and using.
#. Game world puzzles involving sequencing and timed reactions.
#. Visual backgrounds and text descriptions.
#. Aural text-to-speech synthesis, voice actor narration, sound effects, music, and ambiance.

Keep in mind that the project is a template for writing adventure games, not a strict framework or engine. You should drop, replace, extend, or otherwise hack any of the included components to suit your needs.

Clone the Boilerplate
---------------------

The boilerplate project is available on GitHub. Clone it with the following command:

.. code-block:: bash

    git clone git://github.com/parente/dim-boilerplate.git

Understand the Contents
-----------------------

The boilerplate project has the following general layout (not all files are shown)::

    .
    ├── docs/                        # source for this documentation
    ├── dev/                         # development and deployment scripts
    └── webapp/
        ├── css/                     # game visual stylesheets
        ├── data/                    # game world assets
        │   └── world.jso n          # game world definition
        ├── favicon.ico              # game browser bar icon
        ├── img/                     # game iOS startup and touch icons
        ├── index.html
        ├── js/                      # game engine
        │   ├── dim/
        │   │   ├── aural/
        │   │   ├── controllers/     # game interaction controllers
        │   │   ├── events.js
        │   │   ├── input.js
        │   │   ├── main.js
        │   │   ├── pump.js
        │   │   ├── topic.js
        │   │   ├── visual.js
        │   │   └── world.js
        │   └── vendor/              # dependencies
        └── robots.txt


Setup for Development
---------------------

Push the boilerplate into a new git repository where you will build your game. Alternatively, copy at least the dev and webapp folders to a new directory in the version control system of your choice.

The boilerplate has no web server-side requirements. You can simply put the webapp folder in a web accessible directory (e.g., ~/Sites on OS X, ~/public_html on Linux) and visit the index.html page in a browser. As you make build-out your game, you can simply refresh your browser to immediately see the impact of your changes.

Currently, the boilerplate supports the following browsers:

#. Chrome on any platform
#. OS X Safari 6.x

.. note:: Ultimately, the boilerplate should be compatible with any browser that supports the HTML5 `Web Audio API`_.

Build Your Game
---------------

Draft the storyline of your adventure game. Then try to answer these questions.

#. What is the goal of the game?
#. What locations will the player visit?
#. What other people and things exist in the world?
#. What interactions will the player have?
#. What challenges will the player face?

Now modify the boilerplate to implement your game ideas. Work through the following levels of modification in the order given, only continuing to higher levels if you cannot accomplish what you need in a lower level.

* :doc:`Level 1 <declare>` - Define your game scenes, items, and events in the game world JSON file using text, sounds, speech, and images.
* :doc:`Level 2 <code>` - Create new JavaScript controllers to enable new user actions within the game world.
* :doc:`Level 3 <engine>` - Modify the input capture, aural rendering, and visual rendering JavaScript code to support new means of interaction with the user.

.. _Descent Into Madness: http://github.com/parente/dim-game
.. _Web Audio API: http://caniuse.com/#feat=audio-api