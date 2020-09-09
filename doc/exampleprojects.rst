Beispielprojekte
================

.. highlight:: python
    :linenothreshold: 5

Auto-Runner "Racing"
--------------------

In diesem Beispielprojekt wird eine Implementierung eines einfachen `Auto-Runners`__ gezeigt.

.. __: https://de.wikipedia.org/wiki/Auto-Runner

.. image:: _static/racing.JPG

Das `fertige Spiel`__ kann hier heruntergeladen werden.

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

Zusätzlich wollen wir eine Leitplanke auf der linken und rechten Seite realisieren. Dazu programmieren wir
eine eigene Methode, die das erledigt::

    # barriers
    barriers = []

    [...]

    # initialises the barrier of the race track
    def setupBarriers():
      for i in range(0, 6):
          barriers.append(Actor('barrier', (60, 64 + (i * 128))))
          barriers.append(Actor('barrier', (730, 64 + (i * 128))))

Diese Methode setzt auf beiden Seiten insgesamt sieben Stücke der Leitplanke. Die Methode muss in der Methode ``draw()`` aufgerufen werden.
Jedes Stück der Leitplanke wird der Liste ``barriers`` hinzugefügt. Diese wird in der Methode ``draw()`` benötigt, um den Zugriff auf alle Stücke
der Leitplanke zu vereinfachen und diese zu zeichnen::

    def draw():
        screen.blit('road',(0,0))
        for b in barriers:
          b.draw()

.. tip::

    Du findest viele kostenlose Sprites, einschließlich diesem auf `kenney.nl
    <https://kenney.nl/assets?q=2d>`_.
    Die Bilder für dieses Spiel stammen aus `Racing pack
    <https://kenney.nl/assets/racing-pack>`_.

Ein Auto (Sprite) erstellen und steuern
'''''''''''''''''''''''''''''''''''''''

Nachdem die Fahrbahn fertig ist, soll ein Auto (Sprite) realisiert werden.
Der folgende Code erstellt ein neues Auto und positioniert es mittig auf der Fahrbahn::

    # the car
    car = Actor('car_red')
    car.pos = WIDTH/2, HEIGHT/2

In der Methode ``draw()`` muss zudem ``car.draw()`` aufgerufen werden.
Um es so aussehen zu lassen, als ob das Auto sich bewegt, soll es in jedem Frame etwas nach unten verschoben werden::

    # updates inbetween frames
    def update():
      car.y += 1

Zur Steuerung des Autos mithilfe der Pfeiltasten der Tastatur definieren wir die Methode ``checkKeyboard()``, die ebenfalls in ``update()`` aufgerufen werden muss::

    # checks keyboard input
    def checkKeyboard():
        # check for clicks on keyboard to move the car
        if keyboard.left:
            car.angle = +5
            car.x -= SPEED_CAR
        elif keyboard.right:
            car.angle = -5
            car.x += SPEED_CAR
        elif keyboard.up:
            car.y -= SPEED_CAR
        elif keyboard.down:
            car.y += SPEED_CAR
        else:
          car.angle = 0

Die (globale) Variable ``SPEED_CAR`` definiert, wieviel Pixel das Auto bei Klick einer Pfeiltaste verschoben wird. Zusätzlich wird durch die Änderung des Neigungswinkels
des Autos (``car.angle``) das Auto in die entsprechende Richtung, in die es gesteuert wird, geneigt. Dieser Effekt kann auch vernachlässigt werden.
Nun können wir das Auto auf der Fahrbahn steuern.

Ein erstes Hindernis: Kegel
'''''''''''''''''''''''''''
Damit das Spiel etwas spannender wird, sollen dem Auto nun Hindernisse entgegen kommen, auf die unterschiedlich reagiert werden muss.
Wir beginnen dabei mit einem Kegel, der vom Auto entweder nach links (wenn das Auto von rechts kommt) oder nach rechts (wenn das Auto von links kommt) verschoben werden soll.

.. image:: _static/cone.PNG

Ähnlich wie der Leitplanke erstellen wir zuerst eine Liste der Hindernisse (hier: ``cones``), die wir zur Speicherung der Kegel benötigen. Diese hilft uns später,
alle Kegel zu aktualisieren (in der Methode ``draw``)::

    # obstacles
    cones = [] # when toching cones, they are pushed aside

    # speeds and chances of obstacles
    # speeds
    SPEED_CONES = 3

    # chances
    CHANCE_CONES = 1

Das (zufällige) Erstellen sowie das Aktualisieren der Kegel bei Kollision mit dem Auto erledigt die Methode ``createAndUpdateCones()``, die in der Methode ``update()`` aufgerufen werden muss::

    # create a update cones
    def createAndUpdateCones():
        # create new cone
        if(random.randint(0, 100) < CHANCE_CONES): # using a 1% chance
            x = random.randint(0, 400)
            cones.append(Actor('cone', (200 + x, 0)))
        # move the cones
        for c in cones:
            c.y += SPEED_CONES
            # check for collision of the car and cones
            if(car.colliderect(c)):
                if(car.x > c.x):
                    c.x -= 100
                else:
                    c.x += 100

Um den Befehl ``random.randint()`` verwenden zu können, muss zu Beginn des Programms der Befehl ``import random`` gesetzt werden.
Das zufällige Erzeugen neuer Kegel wird von ``random.randint(0, 100) < CHANCE_CONES`` erledigt. Dieser Befehl gibt zurück, ob
die erzeugte Zufallszahl kleiner als eine vorgegebene Wahrscheinlichkeit ist (``CHANCE_CONES``, hier: 1). Damit werden neue Kegel
mit einer Wahrscheinlichkeit von 1 % pro Frame erstellt.
Ähnlich wie das Auto, wird auch ein Kegel pro Frame um den Wert von ``SPEED_CONES`` nach unten verschoben.
Die Kollision eines Kegels mit dem Auto wird mithilfe des Befehls ``car.colliderect(c)`` überprüft. Je nachdem ob das Auto von links oder rechts kommt,
wird der Kegel nach rechts bzw. links verschoben. Somit ist das erste Hindernis, dem das Auto ausweichen muss, erstellt.

Weitere Hindernisse: Ölpfützen, Steine und Pfeile
'''''''''''''''''''''''''''''''''''''''''''''''''

Weitere Hindernisse, die dem Auto entgegen kommen und denen es ausweichen (bzw. angemessen reagieren) muss, können beispielsweise Ölpfützen, Steine und Pfeile sein.

.. image:: _static/oil.PNG

.. image:: _static/rock.PNG

.. image:: _static/arrow.PNG

Der grundlegende Aufbau zur Programmierung von Hindernissen wurde bereits im vorigen Abschnitt bei den Kegeln gezeigt. Wir definieren für alle Hindernisse
eine eigene Methode ``createAndUpdate<newObstacle>()``, die das zufällige Erstellen und die Kollisionserkennung verarbeitet. Diese rufen wir in der Methode ``update()`` auf.
Zudem müssen wir eine Liste, die wir für neue Hindernisse zur Speicherung der einzelnen Objekte verwenden, implementieren und diese in der Methode ``draw()`` benutzen.
Die Unterschiede bei den Hindernissen liegt also großteils lediglich darin, wie sie auf Kollision mit dem Auto reagieren. Wir schlagen folgende Reaktionen vor:

- Bei Kollision mit einer Ölpfütze dreht sich das Auto zufällig::

    [...]
    # check for collision of the car and oil
    if(car.colliderect(o)):
        spin()

    # makes the car spin
    def spin():
      car.y -= 10
      car.angle -= random.randint(-180, 180)

- Bei Kollision mit einem Stein wird das Spiel beendet::

    # check for collision of the car and rocks
    if(car.colliderect(r)):
        # stop game
        gameover = "Game over! \n You hit a rock!"

  Der Text, der in der (globalen) Variable ``gameover`` gespeichert wird, soll also bei Kollision des Autos mit einem Stein angezeigt werden.
  Dieser wird mithilfe des folgenden Befehls in der Methode ``draw()`` gesetzt::

      screen.draw.text(gameover, (WIDTH/2 - 150, HEIGHT/2), color="white", fontsize=50, align="center")

  Zu Beginn des Spiels ist der Wert der Variable ein leerer String (``gameover = ""``), sodass dieser Befehl keine ersichtliche Wirkung hat und
  bei Kollision des Autos mit einem Stein auf einen nicht-leeren String gesetzt wird. Das Spiel wird hierbei jedoch nicht richtig beendet,
  sondern lediglich der Gameover-Text angezeigt. Das richtige Beenden des Spiels wird im folgenden Abschnitt umgesetzt.

- Die Kollisionserkennung des Autos mit einem Pfeil ist hier als offene Aufgabe definiert. Beispielsweise könnte das Auto durch den Pfeil beschleunigt werden...

Countdown hinzufügen
''''''''''''''''''''

Um das Ziel des Spiels (und somit auch das Spielende) sauber zu definieren, wird ein Countdown eingeführt. Der Spieler des Spiels muss den Hindernissen solange ausweichen,
bis der Countdown abgelaufen ist (bzw. das Spiel durch Kollision mit einem Stein vorzeitig beendet wurde).
Dazu werden die beiden neuen (globalen) Variablen ``running`` und ``countdown`` eingeführt. Die Variable ``gameover`` kennen wir bereits::

    # running variable and game over and countdown text
    running = True
    gameover = ""
    countdown = 60.0 (in seconds)

Der Countdown wird mittels des folgenden Befehls in der Methode ``draw()`` auf dem Bildschirm dargestellt::

    screen.draw.text(str(countdown), (WIDTH/2 - 50, 25), color="white", fontsize=50, align="center")

Die folgende Methode aktualisert den Countdown (und stoppt das Spiel, falls er abgelaufen ist)::

    # reset the countdown each second
    def resetCountdown():
        global countdown, gameover, running
        if(countdown > 0.0):
            countdown -= 1.0
        else:
            countdown = 0.0
            gameover = "Game over! \n You won!"
            running = False

Der Befehl ``running = False`` muss  ebenfalls am Ende der Methode ``checkAndUpdateRocks()`` eingefügt werden, um bei der Kollisionserkennung entsprechend zu reagieren.
Um die Methode ``resetCountdown()`` jede Sekunde (und nicht jedem Frame) aufzurufen, nutzen wir die `Uhr`__ von Pygame Zero.

.. __: introduction.html#die-uhr-benutzen

Der folgende Code, der außerhalb der Methode ``resetCountdown()`` aufgerufen werden muss, ruft diese jede Sekunde auf::

    # use the pgzero clock to call the resetCountdown() regulary each second
    clock.schedule_interval(resetCountdown, 1.0)

Damit das Spiel nun nur läuft, wenn der Countdown noch aktiv ist und das Auto mit keinem Stein kollidiert ist, muss die Methode ``update()`` angepasst werden::

    # updates inbetween frames
    def update():
        if (running):
            # check for updates of the actor and obstacles
        else:
            clock.unschedule(resetCountdown)

So wird gewährleistet, dass das Spiel korrekt stoppt und der Countdown mittels des Befehls ``clock.unschedule(resetCountdown)`` nicht mehr aktualisiert wird.
Somit haben wir ein kleines Auto-Runner-Spiel "Racing" fertigstellt und können nur mit dem Spielen beginnen.

Mehr gefällig? Weitere Beispiele finden sich auf der englischen Seite von `Pygame Zero`__.

.. __: https://github.com/nicolaipoehner/pgzero/blob/master/doc/examples.rst
