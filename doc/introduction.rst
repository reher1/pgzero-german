Einführung zu Pygame Zero
===========================

.. highlight:: python
    :linenothreshold: 5

Ein Fenster erzeugen
--------------------

Erzeuge als erstes eine leere Datei mit dem Namen ``intro.py``.

Stelle durch den Befehl ::

    pgzrun intro.py

sicher, dass das Programm ausgeführt wird und ein leeres Fenster erzeugt. Alternativ kannst du das auch in deiner IDE testen.

Du kannst das Spiel beenden, indem du auf die Schließen-Schaltfläche des Fensters oder die Taste
``Ctrl-Q`` (``⌘-Q`` beim Mac) drückst. Wenn das Spiel nicht mehr reagiert, kann es sein, dass du es durch Drücken von ``Ctrl-C`` im Terminal-Fenster beenden musst.


Einen Hintergrund zeichnen
--------------------------

Als nächstes wollen wir die Funktion :func:`draw` hinzufügen und die
Fenstergröße setzen. Pygame Zero ruft diese Funktion immer dann auf, wenn der Bildschirm neu gezeichnet werden muss.
Füge folgenden Code in ``intro.py`` hinzu::

    WIDTH = 300
    HEIGHT = 300

    def draw():
        screen.fill((128, 0, 0))

Führe ``pgzrun intro.py`` erneut aus. Der Bildschirm sollte jetzt ein rotes
Quadrat zeigen!

Was tut dieser Code genau?

``WIDTH`` und ``HEIGHT`` bestimmen die Breite und Höhe deines Fensters. Der Code
setzt die Größe des Fensters auf 300 Pixel für beide Dimensionen.

``screen`` ist ein vorgegebenes Objekt, dass das angezeigte Fenster
repräsentiert. Es hat eine
:ref:`Reihe an Methoden zum Malen von Sprites und Formen <screen>`. Die Methode
``screen.fill()`` befüllt den Bildschirm einfarbig,
in der Farbe mit dem Farbtupel ``(rot, grün, blau)``. ``(128, 0, 0)`` wird zu einem
mittleren Rot-Ton. Ändere die Werte mit Zahlen zwischen 0 und 255 und schaue,
welche Farben du erzeugen kannst.

Jetzt werden wir eine Figur (Sprite) hinzufügen, die wir animieren können.

Eine Figur (Sprite) zeichnen
----------------------------

Bevor wir eine Figur (ein Alien) zeichnen können, müssen wir diese Alien-Figur
speichern. Du kannst hier mit der rechten Maustaste klicken und das Bild speichern
("Bild speichern unter...").

.. image:: _static/alien.png

(Diese Figur hat einen Transparenz- (oder "alpha-") Kanal, der für Spiele
großartig ist!
Allerdings ist sie für einen dunklen Hintergrund gezeichnet worden. Es kann also
sein, dass du den Helm des Aliens erst siehst, wenn er im Spiel
dargestellt wird.)

.. tip::

    Du findest viele kostenlose Sprites, einschließlich diesem auf `kenney.nl
    <https://kenney.nl/assets?q=2d>`_.
    Das Alien-Bild stammt aus `Platformer Art Deluxe pack
    <https://kenney.nl/assets/platformer-art-deluxe>`_.

Du musst die Datei am richten Ort speichern.
Erstelle ein Verzeichnis namens ``images`` und speichere das Bild dort unter
``alien.png`` (alles kleingeschrieben!).

Wenn du das erledigt hast, sollte dein Projektverzeichnis so aussehen:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    └── intro.py

``images/`` ist das Standardverzeichnis, in dem Pygame Zero Bilder sucht.

In Pygame Zero gibt es ein Klasse namens :class:`Actor`, die benutzt werden kann,
um Sprites auf dem Bildschirm zu zeichnen.

Ändere die Datei ``intro.py`` wie folgt ab::

    alien = Actor('alien')
    alien.pos = 100, 56

    WIDTH = 500
    HEIGHT = alien.height + 20

    def draw():
        screen.clear()
        alien.draw()

Auf dem Bildschirm sollte jetzt ein Alien erscheinen! Durch die Angabe des Strings ``'alien'``
in der ``Actor`` Klasse, lädt Pygame Zero automatisch das entsprechende Bild
(inkl. der Attribute wie Position, etc.). So können wir beispielsweise die Höhe des Fensters
entsprechend der Höhe des Alien-Sprites setzen.

Die Funktion ``alien.draw()`` zeichnet das Alien an seiner aktuellen Position auf den Bildschirm.

Figuren bewegen
---------------

Wir wollen das Alien in die linke obere Ecke des Bildschirms setzen; dazu ändern wir den Code wie folgt::

    alien.x = 10
    alien.y = 10

Mithilfe der x- und y-Koordinate können wir das Alien an eine beliebige Position setzen.
Hier setzen wir die Figur in die linke obere Ecke.
Jetzt wollen wir das Alien bewegen; dazu ändern wir den Code wie folgt::

    def update():
        alien.left += 2
        if alien.left > WIDTH:
            alien.right = 0

Pygame Zero ruft die Funktion :func:`update` automatisch in jedem Frame auf. Indem wir das Alien
in jedem Frame etwas verschieben, sieht es so aus, als ob sich das Alien bewegt. Wenn es links aus dem Bildschirm läuft,
erscheint es wieder von rechts.

Die Funktionen ``draw()`` und ``update()`` arbeiten in ähnlicher Art und Weise, sind aber für unterschiedliche Zwecke gedacht.
DIe ``draw()`` Funktion zeichnet das Alien an seiner aktuellen Position. Die Funktion ``update()`` wird benutzt, um das Alien zu bewegen.


Tastatur- und Mauseingaben verarbeiten
--------------------------------------
Nun wollen wir auf Tastatur- und Mauseingaben reagieren. Dazu müssen wir die Funktion :func:`on_mouse_down` implementieren. Füge folgenden Code hinzu::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            print("Eek!")
        else:
            print("You missed me!")

Starte das Spiel und versuche auf das Alien (mit der linken Maustaste) zu klicken.

Musik und Bilder hinzufügen
---------------------------

Wenn wir auf das Alien klicken, soll es verletzt sein. Dazu wollen wir das Bild des Alien-Sprites ändern. Speichere die folgenden Dateien:

* `alien_hurt.png <_static/alien_hurt.png>`_ - speichere die Datei ``alien_hurt.png``
  in das Verzeichnis ``images`` .
* `eep.wav <_static/eep.wav>`_ -erstelle ein Verzeichnis namens ``sounds`` und speichere die Datei ``eep.wav`` in dem Verzeichnis

Wenn du das erledigt hast, sollte dein Projektverzeichnis so aussehen:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    │   └── alien_hurt.png
    ├── sounds/
    │   └── eep.wav
    └── intro.py

``sounds/`` ist das Standardverzeichnis, in dem Pygame Zero Musik sucht.

Wir wollen die Funktion ``on_mouse_down`` so anpassen, damit wir die neuen Dateien verwenden::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            alien.image = 'alien_hurt'
            sounds.eep.play()

Wenn du jetzt auf das Alien klickst, erscheint das neue Bild ``alien_hurt`` und es ertönt ein Ton.

In unserem Spiel gibt es allerdings noch einen kleinen Fehler...
Das Alien wechselt nicht mehr zurück zu seinem alten Bild. Das wollen wir als nächsten ändern!


Die Uhr benutzen
----------------

Pygame Zero hat eine eingebaute Klasse :class:`Clock`, die dafür sorgt, dass Funktionen später ausgeführt werden.

Zuerst wollen wir ein kurzes ``Refactoring`` (d.h. wir reorganisieren unseren Code, um ihn lesbarer zu machen). Wir können Funktionen schreiben, um das Alien verletzt bzw. normal zu setzen::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            set_alien_hurt()


    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()


    def set_alien_normal():
        alien.image = 'alien'

Das ändert an unserem Programm noch nichts, da ``set_alien_normal()`` noch nicht aufgerufen wird. Wir wollen die beiden Funktionen nutzen, damit das Alien nach einem kurzen Augenblick wieder in den normalen Zustand zurück gesetzt wird.::

    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()
        clock.schedule_unique(set_alien_normal, 0.5)

``clock.schedule_unique()`` ruft ``set_alien_normal()`` ca. ``0.5`` Sekunden später auf. ``schedule_unique()`` sorgt auch dafür, dass das nicht mehrfach geschieht, z.B. wenn du mehrfach auf das Alien klickst.

Probier es aus!


Zusammenfassung
---------------

Wir haben jetzt gelernt, wie man Sprites lädt und zeichnet, Musik abspielt, Tastatur- und Mauseingaben verarbeitet und die eingebaute Uhr benutzt. Damit beherrschen wir die ersten Schritte mit Pygame Zero! War doch gar nicht so schwierig, oder?

Ihr wollt euer Spiel noch weiterentwickeln?

Es gibt noch eine Vielzahl an weiteren Funktionen in Pygame Zero, die sehr einfach zu benutzen sind. Lies dazu :doc:`built in objects <builtins>` (Achtung, auf Englisch!), um den Rest von Pygame Zero zu entdecken.
