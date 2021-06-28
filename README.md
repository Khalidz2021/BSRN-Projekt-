# BSRN-Projekt-
# Khalid Zoufal ( 1349537 ) , Fatima Irhzal ( 1352346 ) , Abed al muhsen Alkarim ( 1352650 )
# Alternative 7 Labyrinth (Maze)

import time
import random
import keyboard
from os import system

# Klassen Definieren
class Feld(object):
    """Stellt ein Feld dar"""

    def _init_(self):
        self.besucht = False
        self.walls = [True, True]  # [rechts, unten]


class Player(object):
    """Speichert alle wichtigen daten, die den Spieler betreffen"""

    def _init_(self, x, y):
        self.x = x
        self.y = y
        self.finished = False

    def checkgoal(self):
        """testet, ob der Spieler am Ziel ist"""
        if (self.x, self.y) == (size_x - 2, size_y - 2):
            self.finished = True

    def redraw(self, draw_tiles):
        """Updated das Labyrinth auf den aktuellen stand"""
        for x in range(size_x):
            for y in range(size_y):
                if felder[x][y].besucht:
                    draw_tiles[x * 2 + 1][y * 2 + 1] = " "
                if not felder[x][y].walls[0]:
                    draw_tiles[x * 2 + 1][y * 2 + 2] = " "
                if not felder[x][y].walls[1]:
                    draw_tiles[x * 2 + 2][y * 2 + 1] = " "

        draw_tiles[(size_x - 1) * 2 - 1][(size_y - 1) * 2 - 1] = "\u2573"
        draw_tiles[self.x * 2 + 1][self.y * 2 + 1] = "\u2596"
        draw(draw_tiles)

    def move(self):
        global moving
        # bewegung nach oben
        if keyboard.is_pressed("w") and not moving[0] and not felder[self.x - 1][self.y].walls[1]:  # Set
            moving[0] = True
            self.x -= 1
            self.redraw(tile_asciis)
        if not keyboard.is_pressed("w") and moving[0]:  # Reset
            moving[0] = False
        # bewegung nach unten
        if keyboard.is_pressed("s") and not moving[1] and not felder[self.x][self.y].walls[1]:
            moving[1] = True
            self.x += 1
            self.redraw(tile_asciis)
        if not keyboard.is_pressed("s") and moving[1]:
            moving[1] = False
        # bewegung nach links
        if keyboard.is_pressed("a") and not moving[2] and not felder[self.x][self.y - 1].walls[0]:
            moving[2] = True
            self.y -= 1
            self.redraw(tile_asciis)
        if not keyboard.is_pressed("a") and moving[2]:
            moving[2] = False
        # bewegung nach rechts
        if keyboard.is_pressed("d") and not moving[3] and not felder[self.x][self.y].walls[0]:
            moving[3] = True
            self.y += 1
            self.redraw(tile_asciis)
        if not keyboard.is_pressed("d") and moving[3]:
            moving[3] = False

# Funktionen Definieren

def formattedInput():
    size = input("Die groesse, die das Labyrinth hat: ")
    size = size.split(",")
    try:
        max_x, max_y = int(size[0]) * 2, int(size[1]) * 2
        return max_x, max_y
    except:
        return formattedInput()


def unbesuchteFelder(tile):
    """sucht nach noch nicht besuchten tiles"""
    global felder
    x, y = tile[0], tile[1]
    visitable_tiles = []
    if y + 1 < size_y - 1:
        if not felder[x][y + 1].besucht:
            visitable_tiles.append(1)
    if not felder[x][y - 1].besucht and y > 0:
        visitable_tiles.append(0)
    if x + 1 < size_x - 1:
        if not felder[x + 1][y].besucht:
            visitable_tiles.append(3)
    if not felder[x - 1][y].besucht and x > 0:
        visitable_tiles.append(2)

    return visitable_tiles


def visit_tile(tile):
    """stellt ein tile.visited auf True"""
    x, y = tile[0], tile[1]
    global felder
    felder[x][y].besucht = True


def draw(draw_list):
    print("\n"*100)
    draw_strings = []
    for column in draw_list:
        draw_string = ""
        for line in column:
            for char in line:
                draw_string += char * 2

        draw_strings.append(draw_string)
    for string in draw_strings:
        print(string)

# Variablen Definieren
felderstack = [(0, 0)]  # Stack(speichert die zuletzt besuchten Felder)
felder = []  # 2-Dimensionale Liste aller felder
size_x, size_y = formattedInput()  # groesse des Maze's
directions = [(0, -1), (0, 1), (-1, 0), (1, 0)]  # wird zur berechnung des nächsten feldes genutzt
tile_asciis = []  # 2-Dimesionale Liste aller der zu den Feldern gehörenden ASCII-Char's
moving = [False, False, False, False]  # ist für die jeweilige bewegungsrichtung True oder False
# damit sich der Spieler pro Knopfdruck nur ein Feld bewegt



# 2D-Liste mit allen Felder-Objekten erstellen
for x in range(size_x):
    felder.append([])
    for y in range(size_y):
        felder[x].append(Feld())
del x, y

visit_tile(felderstack[-1])

# Schleife in dem das Labyrinth generiert wird
while len(felderstack) > 0:

    if unbesuchteFelder(felderstack[-1]) != []:  # Test ob es noch nicht besuchte Nachbarfelder gibt
        # falls ja
        direction = random.choice(unbesuchteFelder(felderstack[-1]))  # auswahl eines noch nicht besuchten feldes
        # 0 hoch, 1 runter, 2 links und 3 rechts

        # print(getVisitableTiles(felderstack[-1]))

        nexttile = (felderstack[-1][0] + directions[direction][0], felderstack[-1][1] + directions[direction][1])  # berechnen der koordinaten des Nächsten feldes aus der Richtungsvariable

        felderstack.append(nexttile)  # hinzufügen zum Stack
        visit_tile(felderstack[-1])
        # print(felderstack)
        # wände entfernen
        if direction == 0:  # oben
            felder[felderstack[-1][0]][felderstack[-1][1]].walls[0] = False
        elif direction == 1:  # unten
            felder[felderstack[-2][0]][felderstack[-2][1]].walls[0] = False
        elif direction == 2:   # rechts
            felder[felderstack[-1][0]][felderstack[-1][1]].walls[1] = False
        elif direction == 3:  # links
            felder[felderstack[-2][0]][felderstack[-2][1]].walls[1] = False


    else:  # wenn um das Feld nur besuchte nachbarn sind dann entferne ein Item aus dem Stack
        felderstack.pop()

# Definieren, des Spielerobjektes

player = Player(1, 1)


# generiert die Liste mit allen Characters
for x in range(size_x * 2 - 1):
    tile_asciis.append([])
    for y in range(size_y * 2 - 1):
        tile_asciis[x].append("\u2591")


startTime = time.time()  # für den timer, des Labyrinthes

print("Generiere, das Labyrinth...")
time.sleep(1)
print("Fertig")
time.sleep(0.3)


player.redraw(tile_asciis)  # erstes mal zeichnen, da nur bei einer bewegung neugezeichnet wird


while not player.finished:
    time.sleep(1 / 30)

    player.move()
    player.checkgoal()

print("Du hast %s Sekunden gebraucht um zum Ziel zu gelagen" % (str(time.time() - startTime)[:4]))

print("Drücke [Enter] um zu verlassen")
keyboard.wait("ENTER")
#
