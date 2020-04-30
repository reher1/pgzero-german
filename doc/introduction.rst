Einführung zu Pygame Zero
===========================

.. highlight:: python
    :linenothreshold: 5

Ein Fenster erzeugen
--------------------

Erzeuge als erstes eine leere Datei mit dem Namen ``intro.py``.

Stelle durch den Befehl ::

    pgzrun intro.py

sicher, dass es ausgeführt wird und ein leeres Fenster erzeugt.

Du kannst das Spiel beenden, indem du auf die Schließen-Schaltfläche des 
Fensters klickst oder durch drücken von 
``Ctrl-Q`` (``⌘-Q`` beim Mac). Wenn das Spiel aus nicht mehr reagiert, kann es sein, dass du es durch drücken von ``Ctrl-C`` im
Terminal-Fenster beenden musst.


Einen Hintergrund zeichnen
-----------------------

Als nächstes wollen wir die Funktion :func:`draw` hinzufügen und die
Fenstergröße setzen. Pygame Zero ruft diese Funktion immer dann auf, wenn der Bildschirm neu gezeichnet werden muss.
Füge folgenden Code in ``intro.py`` hinzu::

    WIDTH = 300
    HEIGHT = 300

    def draw():
        screen.fill((128, 0, 0))

Führe ``pgzrun intro.py`` erneut aus. Der Bildschirm sollte jetzt ein rotes
Quadrat zeigen!

Was tut dieser Code?

``WIDTH`` und ``HEIGHT`` bestimmen die Breite und Höhe deines Fensters. Der Code
setzt die Größe des Fensters auf 300 Pixel in jede Richtung.

``screen`` ist ein vorgegebenes Objekt, dass das angezeigte Fenster
representiert. Es hat eine 
:ref:`Reihe an Methoden zum malen von Sprites und Formen <screen>`. Die 
``screen.fill()`` Methode befüllt den Bildschirm einfarbig,
definiert mit dem Farbtupel ``(rot, grün, blau)``. ``(128, 0, 0)`` wird zu einem
mittleren Rot-Ton. Ändere die Werte mit Zahlen zwischen 0 und 255 und schaue,
welche Farben du erzeugen kannst.

Jetzt werden wir eine Figur (Sprite) aufsetzten, die wir animieren können.

Eine Figur (Sprite) zeichnen
----------------------------

Bevor wir etwas zeichnen können, müssen wir eine Alien-Figur
speichern. Du kannst hier mit der rechten Maustaste klicken und es speichern
("Bild speichern unter...").

.. image:: _static/alien.png

(Diese Figur hat einen Transparenz- (oder "alpha-") Kanal, der für Spiele
großartig ist!
Allerdings ist sie für einen dunklen Hintergrund gezeichnet worden. Es kann also
sein, dass du den Helm des Aliens erst siehst, wenn er im Spiel
dargestellt wird.)

.. Tipp::

    Du findest viele kostenlose Sprites, einschließlich diesem auf `kenney.nl
    <https://kenney.nl/assets?q=2d>`_. Das Alien-Bild stammt aus
    `Platformer Art Deluxe pack
    <https://kenney.nl/assets/platformer-art-deluxe>`_.

Du musst die Datei am richten Ort ablegen.
Erstelle ein Verzeichnis namens ``images`` und speichere das Bild dort unter
``alien.png``. (alles kleingeschrieben!)

Wenn du das erledigt hast, sollte dein Projektverzeichnis so aussehen:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    └── intro.py

``images/`` ist das Standardverzeichnis, in dem Pygame Zero Bilder sucht.

In Pygame Zero gibt es ein Klasse namens :class:`Actor` das benutzt werden kann,
um Bilder auf dem Bildschrimm zu zeichnen.

Ändere die Datei ``intro.py`` wie folgt ab::

    alien = Actor('alien')
    alien.pos = 100, 56

    WIDTH = 500
    HEIGHT = alien.height + 20

    def draw():
        screen.clear()
        alien.draw()

Auf dem Bildschrim sollte jetzt ein Alien erscheinen! Durch die Angabe des Strings ``'alien'``
in der ``Actor`` Klasse, lädt Pygame Zero automatisch das entsprechende Bild
(inkl. der Attribute wie Position, etc.). So können wir beispielsweise die Höhe des Fensters
entsprechend der Höhe des Alien-Sprites setzen.

Die Methode ``alien.draw()`` zeichnet das Alien an seiner aktuellen Position auf den Bildschirm.


Figuren bewegen
----------------

Wir wollen das Alien in die rechte obere Ecke des Bildschirrm setzen; dazu ändern wir den Code wie folgt::

    alien.topright = 0, 10

Note how you can assign to ``topright`` to move the alien actor by its
top-right corner. If the right-hand edge of the alien is at ``0``, the the
alien is just offscreen to the left.  Now let's make it move. Add the following
code to the bottom of the file::

    def update():
        alien.left += 2
        if alien.left > WIDTH:
            alien.right = 0

Pygame Zero ruft die Funktion :func:`update` automatisch in jedem Frame auf. Indem wir das Alien
in jedem Frame etwas verschieben, sieht es so aus, als ob sich das Alien bewegt. Wenn es links aus dem Bildschirm läuft,
erscheint es wieder von rechts.

Die Funktionen ``draw()`` und ``update()`` arbeiten in ähnlicher Art und Weise, sind aber für unterschiedliche Zwecke gedacht.
DIe ``draw()`` Funktion zeichnet das Alien an seiner aktuellen Position. Die Funktion``update()`` wird benutzt, um das Alien zu bewegen.


Handling clicks
---------------

Let's make the game do something when you click on the alien. To do this we
need to define a function called :func:`on_mouse_down`. Add this to the source
code::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            print("Eek!")
        else:
            print("You missed me!")

You should run the game and try clicking on and off the alien.

Pygame Zero is smart about how it calls your functions. If you don't define
your function to take a ``pos`` parameter, Pygame Zero will call it without
a position. There's also a ``button`` parameter for ``on_mouse_down``. So we
could have written::

    def on_mouse_down():
        print("You clicked!")

or::

    def on_mouse_down(pos, button):
        if button == mouse.LEFT and alien.collidepoint(pos):
            print("Eek!")



Sounds and images
-----------------

Now let's make the alien appear hurt. Save these files:

* `alien_hurt.png <_static/alien_hurt.png>`_ - save this as ``alien_hurt.png``
  in the ``images`` directory.
* `eep.wav <_static/eep.wav>`_ - create a directory called ``sounds`` and save
  this as ``eep.wav`` in that directory.

Your project should now look like this:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    │   └── alien_hurt.png
    ├── sounds/
    │   └── eep.wav
    └── intro.py

``sounds/`` is the standard directory that Pygame Zero will look in to find
your sound files.

Now let's change the ``on_mouse_down`` function to use these new resources::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            alien.image = 'alien_hurt'
            sounds.eep.play()

Now when you click on the alien, you should hear a sound, and the sprite will
change to an unhappy alien.

There's a bug in this game though; the alien doesn't ever change back to a
happy alien (but the sound will play on each click). Let's fix this next.


Clock
-----

If you're familiar with Python outside of games programming, you might know the
``time.sleep()`` method that inserts a delay. You might be tempted to write
code like this::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            alien.image = 'alien_hurt'
            sounds.eep.play()
            time.sleep(1)
            alien.image = 'alien'

Unfortunately, this is not at all suitable for use in a game. ``time.sleep()``
blocks all activity; we want the game to go on running and animating. In fact
we need to return from ``on_mouse_down``, and let the game work out when to
reset the alien as part of its normal processing, all the while running your
``draw()`` and ``update()`` methods.

This is not difficult with Pygame Zero, because it has a built-in
:class:`Clock` that can schedule functions to be called later.

First, let's "refactor" (ie. re-organise the code). We can create functions to
set the alien as hurt and also to change it back to normal::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            set_alien_hurt()


    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()


    def set_alien_normal():
        alien.image = 'alien'

This is not going to do anything different yet. ``set_alien_normal()`` won't be
called. But let's change ``set_alien_hurt()`` to use the clock, so that the
``set_alien_normal()`` will be called a little while after. ::

    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()
        clock.schedule_unique(set_alien_normal, 0.5)

``clock.schedule_unique()`` will cause ``set_alien_normal()`` to be called
after ``0.5`` second. ``schedule_unique()`` also prevents the same function
being scheduled more than once, such as if you click very rapidly.

Try it, and you'll see the alien revert to normal after 0.5 second. Try clicking
rapidly and verify that the alien doesn't revert until 0.5 second after the last
click.

``clock.schedule_unique()`` accepts both integers and float numbers for the time interval. in the tutorial we are using
a float number to show this but feel free to use both to see the difference and effects the different values have.


Summary
-------

We've seen how to load and draw sprites, play sounds, handle input events, and
use the built-in clock.

You might like to expand the game to keep score, or make the alien move more
erratically.

There are lots more features built in to make Pygame Zero easy to use. Check
out the :doc:`built in objects <builtins>` to learn how to use the rest of the
API.
