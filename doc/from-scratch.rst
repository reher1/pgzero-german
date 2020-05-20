Von Scratch zu Pygame Zero
======================

In diesem Tutorial wird eine Implementierung des Spiels Flappy Bird in Scratch und Pygame Zero gegenübergestellt. So soll gezeigt werden, dass die Implmentierung in Pygame Zero sehr ähnlich zu der in Scratch ist und der Übergang von Scratch zu Pygame Zero daher sehr einfach ist.

Die `Pygame Zero Version`__ kann hier heruntergeladen werden.

.. __: https://github.com/lordmauve/pgzero/blob/master/examples/flappybird/flappybird.py
Auch die `Scratch Version`__ gibt es zum Download.

.. __: https://github.com/lordmauve/pgzero/raw/master/examples/flappybird/Flappy%20Bird.sb

Der Code der Pygame Zero Version wurde an einigen Stellen etwas abgeändert, um die beiden Versionen besser gegenüberstellen zu können.

Die Bühne
---------

So sieht die Bühne des Spiels in der Scratch Version aus:

.. image:: _static/scratch/flappybird-stage.png

In der Bühne finden sich lediglich drei Objekte: Der Hintergrund, der Vogel und das obere und untere Rohr.

In Pygame Zero erstellen wir zuerst diese drei Objekte::

   bird = Actor('bird1', (75, 200))
   pipe_top = Actor('top', anchor=('left', 'bottom'))
   pipe_bottom = Actor('bottom', anchor=('left', 'top'))

Danach zeichnen wir diese in unserem Fenster::

   def draw():
       screen.blit('background', (0, 0))
       pipe_top.draw()
       pipe_bottom.draw()
       bird.draw()


Bewegung der Rohre
-------------

Die Rohre bewegen sich in konstanter Geschwindigkeit zufällig nach oben und unten.
Wenn sie links aus dem Fenster verschwinden, erscheinen sie rechts neu.

In der Scratch Version werden dazu zwei Skripte (je eines pro Rohr) erzeugt:

.. image:: _static/scratch/flappybird-top-start.png

.. image:: _static/scratch/flappybird-bottom-start.png

Diese erledigen folgende Aktionen:

* Die Bedingung ``x position < -240`` ist erfüllt, wenn das Rohr das Fenster links verlassen hat.
  Dann werden sie rechts neu erzeugt.
* Die Variable ``pipe_height`` koordiniert die beiden Rohe, die immer gleich weit 
  voneinander entfernt sein sollen. Daher kann nicht für beide Rohre eine zufällige Höhe bestimmt werden.
* Der Befehl ``set y position to pipe height +/- 230`` sorgt dafür, dass immer der gleiche Abstand zwischen 
  den Rohren gegeben ist. 

Dieser Code kann in Pygame Zero deutlich einfacher realisiert werden::

   import random

   WIDTH = 400
   HEIGHT = 708
   GAP = 130
   SPEED = 3

   def reset_pipes():
       pipe_gap_y = random.randint(200, HEIGHT - 200)
       pipe_top.pos = (WIDTH, pipe_gap_y - GAP // 2)
       pipe_bottom.pos = (WIDTH, pipe_gap_y + GAP // 2)

   def update_pipes():
       pipe_top.left -= SPEED
       pipe_bottom.left -= SPEED
       if pipe_top.right < 0:
           reset_pipes()

Ein Vorteil von Pygame Zero ist die Verwendung von Konstanten (z.B. GAP (in Großbuchstaben)). Wenn man 
den Abstand zwischen den Rohren verringern möchte (z.B. um das Spiel schwieriger zu machen),
muss der Code lediglich an einer Stelle geändert werden.

Der größte Unterschied der beiden Versionen ist die (fehlende) Benutzung einer Endlosschleife.
Dies geschieht in Pygame Zero automatisch mithilfe der Funktion ``update()``, 
die regelmäßig automatisch aufgerufen wird::

   def update():
      update_pipes()

Der Vogel
--------

Ähnlich wie bei der Bewegung der Rohre funktioniert auch die Bewegung des Vogels.

Um die Bewegung des Vogels zu aktualisieren, wird eine neue Funktion ``update_bird()`` benutzt.
Dabei wird als realisiert, dass sich der Vogel gemäß der Schwerkraft bewegt (d.h. dass er nach unten fällt)::

   GRAVITY = 0.3

   # Initial state of the bird
   bird.dead = False
   bird.vy = 0

   def update_bird():
       uy = bird.vy
       bird.vy += GRAVITY
       bird.y += bird.vy
       bird.x = 75

Zur Realisierung der Schwerkraft benutzen wir folgende einfache Begriffe:

* Schwerkraft (GRAVITY) meint **Beschleunigung nach unten**.
* Beschleunigung meint die Veränderung der **Geschwindigkeit**.
* Geschwindigkeit meint die Veränderung der **Position**.

Die Variable ``bird.vy`` stellt die Geschwindigkeit des Vogels in ``y`` Richtung dar.
Diese Variable erstellen wir neu. Die Schwerkraft ist eine konstante Bewegung nach unten.
Die Beschleunigung wird durch die Schwerkraft verstärkt: ``GRAVITY`` wird zu ``bird.vy`` addiert. 
Die Geschwindigkeit wird durch die Positionveränderung ausgedrückt: ``bird.vy`` wird zu ``bird.y`` addiert.

Gleichzeit bewegt sich der Vogel nicht in ``x`` Richtung. Die Bewegung des Spiels wird durch die Bewegung 
der Rohre simuliert.

Als nächstes wollen wir die Flügelbewegung des Vogels implementieren. Dazu ändern wir sein Bild. 
Bewegt sich der Vogel nach oben, wird das Bild ``bird2`` angezeigt. Bei einer Bewegung nach unten das Bild ``bird1``::

       if not bird.dead:
           if bird.vy < -3:
               bird.image = 'bird2'
           else:
               bird.image = 'bird1'
              
Hinweis: Der Wert -3 wurde durch Ausprobieren gewählt.

Nun soll überprüft werden, ob der Vogel eines der Rohre berührt::

       if bird.colliderect(pipe_top) or bird.colliderect(pipe_bottom):
           bird.dead = True
           bird.image = 'birddead'
           
Ist dies der Fall, soll die Variable ``dead`` auf ``True`` gesetzt werden (d.h. der Vogel ist tot und das Spiel somit beendet) und das Bild geändert werden.

Am Ende soll noch überprüft werden, ob der Vogel auf den Boden gefallen ist. Falls ja, soll er wieder 
in die Startposition gebracht werden. Zudem sollen die Rohre neu gesetzt werden::

       if not 0 < bird.y < 720:
           bird.y = 200
           bird.dead = False
           bird.vy = 0
           reset_pipes()

Damit all diese Änderungen auch regelmäßig aufgerufen werden, rufen iese Funktionen in der Funktion ``update()`` auf::

   def update():
      update_walls()
      update_bird()

Um jetzt auf Tastatur- bzw. Mauseingaben zu reagieren, definieren wir noch die Funktion ``on_key_down()``.::

   FLAP_VELOCITY = -6.5

   def on_key_down():
       if not bird.dead:
           bird.vy = FLAP_VELOCITY

Bei Tastendruck soll ein lebender Vogel etwas nach oben fliegen (bevor er aufgrund der Schwerkraft wieder 
nach unten fällt). Dazu wird die Variable ``vy`` auf einen negativen Wert gesetzt.

Hinweis: Durch Veränderung der Variable ``vy`` fliegt der Vogel etwas höher bzw. weniger hoch nach oben.

Im Allgemeinen sollten beim Vergleich der beiden Implementierungen in Scratch und Pygame Zero einige Ähnlichkeiten auffallen:

.. image:: _static/scratch/flappybird-bird-start.png
.. image:: _static/scratch/flappybird-bird-space.png

Zusammenfassung
-------

Viele Schnippsel des Codes aus der Scratch Version lassen sich einfach in Pygame Zero überführen.

Hier eine paar Beispiele:

+----------------------------+--------------------------------------------+
| In Scratch                 | In Pygame Zero                             |
+============================+============================================+
| ``change y by 1`` (up)     | ``bird.y -= 1``                            |
+----------------------------+--------------------------------------------+
| ``change y by -1`` (down)  | ``bird.y += 1``                            |
+----------------------------+--------------------------------------------+
| ``set costume to <name>``  | ``bird.image = 'name'``                    |
+----------------------------+--------------------------------------------+
| ``if dead = 0``            | ``if not bird.dead:``                      |
+----------------------------+--------------------------------------------+
| ``set dead to 0``          | ``bird.dead = False``                      |
+----------------------------+--------------------------------------------+
| ``if touching Top?``       | ``if bird.colliderect(pipe_top)``          |
+----------------------------+--------------------------------------------+
| ``When Flag clicked``...   | Code in die Funktion ``update()``          |
| ``forever``                | schreiben.                                 |
+----------------------------+--------------------------------------------+
| ``When [any] key pressed`` | ``def on_key_down():``                     |
+----------------------------+--------------------------------------------+
| ``pick random a to b``     | ``import random`` um das Modul ``random``  |
|                            | zu laden, dann ``random.randint(a, b)``    |
+----------------------------+--------------------------------------------+
| (0, 0) is the centre of    | (0, 0) ist die linke obere Ecke des        |
| the stage                  | Fensters.                                  |
+----------------------------+--------------------------------------------+

In einigen Fällen ist der Code der Pygame Zero Version sogar deutlich 
einfach zu verstehen, als der der Scratch Version.
