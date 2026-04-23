# zoo-python-
Application de simulation de zoo avec Python et SQLite
import sqlite3
import time

# -------------------------------
# Création de la base de données
# -------------------------------
def creer_base():
    connexion = sqlite3.connect("zoo.db")
    curseur = connexion.cursor()

    curseur.execute("""
    CREATE TABLE IF NOT EXISTS animaux (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nom TEXT,
        espece TEXT,
        faim INTEGER,
        energie INTEGER
    )
    """)

    connexion.commit()
    connexion.close()


# -------------------------------
# Ajouter un animal
# -------------------------------
def ajouter_animal(nom, espece):
    connexion = sqlite3.connect("zoo.db")
    curseur = connexion.cursor()

    curseur.execute(
        "INSERT INTO animaux (nom, espece, faim, energie) VALUES (?, ?, ?, ?)",
        (nom, espece, 50, 100)
    )

    connexion.commit()
    connexion.close()
    print("Animal ajouté !")


# -------------------------------
# Afficher les animaux
# -------------------------------
def afficher_animaux():
    connexion = sqlite3.connect("zoo.db")
    curseur = connexion.cursor()

    curseur.execute("SELECT * FROM animaux")
    animaux = curseur.fetchall()

    if len(animaux) == 0:
        print("Aucun animal dans le zoo.")
    else:
        for a in animaux:
            print(f"ID:{a[0]} | Nom:{a[1]} | Espèce:{a[2]} | Faim:{a[3]} | Energie:{a[4]}")

    connexion.close()


# -------------------------------
# Nourrir un animal
# -------------------------------
def nourrir_animal(id):
    connexion = sqlite3.connect("zoo.db")
    curseur = connexion.cursor()

    curseur.execute(
        "UPDATE animaux SET faim = faim - 20 WHERE id = ?",
        (id,)
    )

    connexion.commit()
    connexion.close()
    print("Animal nourri !")


# -------------------------------
# Simulation automatique
# -------------------------------
def simulation():
    print("Simulation en cours... (CTRL+C pour arrêter)")
    try:
        while True:
            connexion = sqlite3.connect("zoo.db")
            curseur = connexion.cursor()

            curseur.execute("""
            UPDATE animaux 
            SET faim = faim + 5, energie = energie - 2
            """)

            connexion.commit()
            connexion.close()

            print("Le temps passe dans le zoo...")
            time.sleep(5)

    except KeyboardInterrupt:
        print("\nSimulation arrêtée.")


# -------------------------------
# Menu principal
# -------------------------------
def menu():
    creer_base()

    while True:
        print("\n----- ZOO SIMULATION -----")
        print("1. Ajouter un animal")
        print("2. Voir les animaux")
        print("3. Nourrir un animal")
        print("4. Lancer simulation")
        print("5. Quitter")

        choix = input("Choix : ")

        if choix == "1":
            nom = input("Nom : ")
            espece = input("Espèce : ")
            ajouter_animal(nom, espece)

        elif choix == "2":
            afficher_animaux()

        elif choix == "3":
            id = int(input("ID de l'animal : "))
            nourrir_animal(id)

        elif choix == "4":
            simulation()

        elif choix == "5":
            print("Au revoir !")
            break

        else:
            print("Choix invalide.")


# Lancer le programme
menu()
