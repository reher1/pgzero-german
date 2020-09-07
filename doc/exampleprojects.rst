Beispielprojekte
================

.. highlight:: python
    :linenothreshold: 5

Auto-Runner "Racing"
--------------------

In diesem Beispielprojekt wird eine Implementierung eines einfachen `Auto-Runners`__ gezeigt.

.. __: https://de.wikipedia.org/wiki/Auto-Runner

.. image:: _static/racing.JPG

Die `Pygame Zero Version`__ kann hier heruntergeladen werden.

.. __: https://github.com/nicolaipoehner/pgzero_exampleprojects/blob/master/Racing.py

Die Bühne
'''''''''

Zu Beginn wollen wir eine Fahrbahn als Bühne für das Spiel entwickeln.
Dazu setzen wir zuerst den Hintergrund::

    # width and height of the world
    WIDTH = 800
    HEIGHT = 768

    def draw():
        screen.blit('road',(0,0))

Zusätzlich wollen wir eine Leitplanke auf der linken und rechten Seite realisieren. Dazu realisieren wir
eine eigene Methode, die das erledigt::

    # initialises the barrier of the race track
    def setupBarriers():
      for i in range(0, 6):
          barriers.append(Actor('barrier', (60, 64 + (i * 128))))
          barriers.append(Actor('barrier', (730, 64 + (i * 128))))

Diese Methode setzt auf beiden Seiten insgesamt sieben Stücke der Leitplanke. Die Methode muss in der Methode ``draw()`` aufgerufen werden.

.. tip::

    Du findest viele kostenlose Sprites, einschließlich diesem auf `kenney.nl
    <https://kenney.nl/assets?q=2d>`_.
    Die Bilder für dieses Spiel stammen aus `Racing pack
    <https://kenney.nl/assets/racing-pack>`_.

Ein Auto (Sprite) erstellen und steuern
'''''''''''''''''''''''''''''''''''''''

Kegel im Weg
''''''''''''

Weitere Hindernisse: Ölpfützen und Steine
'''''''''''''''''''''''''''''''''''''''''

Countdown hinzufügen
''''''''''''''''''''

Mehr gefällig? Weitere Beispiele finden sich auf der englischen Seite von `Pygame Zero`__.

.. __: https://github.com/nicolaipoehner/pgzero/blob/master/doc/examples.rst
