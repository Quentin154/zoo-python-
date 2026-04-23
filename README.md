# zoo-python-
Application de simulation de zoo avec Python et SQLite
import sqlite3
import time

# =========================
# BASE DE DONNÉES
# =========================
class Database:
    def __init__(self, db_name="zoo.db"):
        self.db_name = db_name
        self.creer_table()

    def connect(self):
        return sqlite3.connect(self.db_name)

    def creer_table(self):
        conn = self.connect()
        cur = conn.cursor()

        cur.execute("""
        CREATE TABLE IF NOT EXISTS animaux (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nom TEXT,
            espece TEXT,
            faim INTEGER,
            energie INTEGER
        )
        """)

        conn.commit()
        conn.close()

    def ajouter_animal(self, animal):
        conn = self.connect()
        cur = conn.cursor()

        cur.execute("""
        INSERT INTO animaux (nom, espece, faim, energie)
        VALUES (?, ?, ?, ?)
        """, (animal.nom, animal.espece, animal.faim, animal.energie))

        conn.commit()
        conn.close()

    def obtenir_animaux(self):
        conn = self.connect()
        cur = conn.cursor()

        cur.execute("SELECT * FROM animaux")
        data = cur.fetchall()

        conn.close()
        return data

    def update_simulation(self):
        conn = self.connect()
        cur = conn.cursor()

        cur.execute("""
        UPDATE animaux
        SET faim = faim + 5,
            energie = energie - 2
        """)

        conn.commit()
        conn.close()

    def nourrir(self, animal_id):
        conn = self.connect()
        cur = conn.cursor()

        cur.execute("""
        UPDATE animaux
        SET faim = faim - 20
        WHERE id = ?
        """, (animal_id,))

        conn.commit()
        conn.close()


# =========================
# CLASSE ANIMAL (POO)
# =========================
class Animal:
    def __init__(self, nom, espece, faim=50, energie=100):
        self.nom = nom
        self.espece = espece
        self.faim = faim
        self.energie = energie

    def afficher(self):
        return f"{self.nom} ({self.espece}) | Faim:{self.faim} | Energie:{self.energie}"


# =========================
# ZOO (LOGIQUE PRINCIPALE)
# =========================
class Zoo:
    def __init__(self):
        self.db = Database()

    def ajouter_animal(self):
        nom = input("Nom : ")
        espece = input("Espèce : ")

        animal = Animal(nom, espece)
        self.db.ajouter_animal(animal)

        print("✔ Animal ajouté au zoo.")

    def afficher_animaux(self):
        animaux = self.db.obtenir_animaux()

        if not animaux:
            print("Aucun animal.")
            return

        print("\n--- ZOO ---")
        for a in animaux:
            print(f"[ID:{a[0]}] {a[1]} ({a[2]}) | Faim:{a[3]} | Energie:{a[4]}")

    def nourrir_animal(self):
        try:
            animal_id = int(input("ID de l'animal : "))
            self.db.nourrir(animal_id)
            print("🍖 Animal nourri.")
        except ValueError:
            print("❌ ID invalide.")

    def simulation(self):
        print("⏳ Simulation en cours (CTRL+C pour arrêter)")

        try:
            while True:
                self.db.update_simulation()
                print("Le temps passe dans le zoo...")
                time.sleep(3)

        except KeyboardInterrupt:
            print("\nSimulation arrêtée.")


# =========================
# MENU
# =========================
def menu():
    zoo = Zoo()

    while True:
        print("\n===== ZOO SIMULATION =====")
        print("1. Ajouter un animal")
        print("2. Afficher les animaux")
        print("3. Nourrir un animal")
        print("4. Simulation")
        print("5. Quitter")

        choix = input("Choix : ")

        if choix == "1":
            zoo.ajouter_animal()

        elif choix == "2":
            zoo.afficher_animaux()

        elif choix == "3":
            zoo.nourrir_animal()

        elif choix == "4":
            zoo.simulation()

        elif choix == "5":
            print("Fin du programme.")
            break

        else:
            print("Choix invalide.")


# =========================
# LANCEMENT
# =========================
if __name__ == "__main__":
    menu()
