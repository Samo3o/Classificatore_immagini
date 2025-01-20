import cv2
import tkinter as tk
from tkinter import Button, Toplevel
from PIL import Image, ImageTk

# Variabile globale per la foto scattata
foto_scattata = None

# Funzione per scattare la foto
def scatta_foto():
    global foto_scattata
    ret, frame = cap.read()

    if ret:
        # Memorizza il frame scattato
        foto_scattata = frame
        print("Foto scattata!")

        # Nasconde la finestra principale e mostra la finestra di opzioni
        window.withdraw()
        mostra_foto(foto_scattata)


# Funzione per mostrare la foto scattata in una finestra separata
def mostra_foto(frame):
    # Crea una nuova finestra per visualizzare la foto
    top = Toplevel(window)
    top.title("Foto Scattata")

    # Converti il frame in un formato che Tkinter può mostrare
    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    img = Image.fromarray(frame_rgb)
    imgtk = ImageTk.PhotoImage(image=img)

    # Mostra l'immagine nella finestra
    label = tk.Label(top, image=imgtk)
    label.pack()

    # Memorizza l'oggetto imgtk per evitare che venga distrutto dal garbage collector
    label.imgtk = imgtk

    # Pulsante per salvare la foto
    salva_button = Button(top, text="Salva Foto", command=lambda: salva_foto(frame, top))
    salva_button.pack(side=tk.LEFT)

    # Pulsante per rifare la foto
    rifai_button = Button(top, text="Rifai Foto", command=lambda: rifai_foto(top))
    rifai_button.pack(side=tk.LEFT)


# Funzione per salvare la foto
def salva_foto(frame, top):
    cv2.imwrite("foto_scattata.jpg", frame)
    print("Foto salvata!")
    top.destroy()
    window.quit()  # Chiude la finestra principale


# Funzione per rifare la foto
def rifai_foto(top):
    top.destroy()  # Chiude la finestra della foto scattata
    window.deiconify()  # Mostra di nuovo la finestra principale


# Funzione per aggiornare la finestra con l'immagine della telecamera
def aggiorna_finestra():
    ret, frame = cap.read()
    if ret:
        # Converti il frame in formato RGB
        frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        img = Image.fromarray(frame_rgb)
        imgtk = ImageTk.PhotoImage(image=img)

        # Mostra l'immagine nella finestra Tkinter
        label.config(image=imgtk)
        label.imgtk = imgtk

    # Riprova ad aggiornare la finestra ogni 10ms
    window.after(10, aggiorna_finestra)


# Crea la finestra principale
window = tk.Tk()
window.title("Finestra Telecamera")

# Crea un'etichetta per mostrare il video
label = tk.Label(window)
label.pack()

# Pulsante per scattare la foto
scatta_button = Button(window, text="Scatta Foto", command=scatta_foto)
scatta_button.pack()

# Avvia la videocamera (0 è l'indice predefinito della videocamera)
cap = cv2.VideoCapture(0)

# Avvia l'aggiornamento della finestra
aggiorna_finestra()

# Avvia la finestra
window.mainloop()

# Rilascia la videocamera quando la finestra viene chiusa
cap.release()
cv2.destroyAllWindows()
cv2.destroyAllWindows()
-----
import tkinter as tk
from tkinter import messagebox
import os
from pathlib import Path


# Funzione che verrà chiamata quando si clicca su "Ok"
def on_ok():
    nr_classifiche = entry_nr_classifiche.get()  # Otteniamo il numero di classifiche
    project_name = entry_project_name.get()  # Otteniamo il nome del progetto

    if nr_classifiche.isdigit():  # Verifica se è un numero
        nr_classifiche = int(nr_classifiche)

        if not project_name:  # Se non è stato inserito un nome del progetto, mettiamo un valore predefinito
            project_name = "Lavoro"  # Cambiato "Progetto Adecco" in "Lavoro"

        open_classifiche_window(nr_classifiche, project_name)
        root.withdraw()  # Nasconde la finestra principale senza chiuderla
    else:
        messagebox.showerror("Errore", "Per favore, inserisci un numero valido.")


# Funzione per aprire una nuova finestra con i campi per le classifiche
def open_classifiche_window(nr_classifiche, project_name):
    # Nuova finestra popup
    classifiche_window = tk.Toplevel(root)
    classifiche_window.title(f"Inserisci Classifiche per {project_name}")  # Titolo aggiornato

    # Lista per raccogliere gli entry delle classifiche
    entry_classifiche_list = []

    # Creare i campi di input per le classifiche
    for i in range(nr_classifiche):
        label = tk.Label(classifiche_window, text=f"Classifica {i + 1}:")
        label.pack(pady=5)
        entry_classifica = tk.Entry(classifiche_window)
        entry_classifica.pack(pady=5)
        entry_classifiche_list.append(entry_classifica)

    # Bottone "Conferma" per creare le cartelle e chiudere la finestra
    confirm_button = tk.Button(classifiche_window, text="Conferma",
                               command=lambda: create_folders(entry_classifiche_list, classifiche_window, project_name))
    confirm_button.pack(pady=10)


# Funzione per creare le cartelle sul desktop
def create_folders(entry_classifiche_list, classifiche_window, project_name):
    # Ottieni il percorso del Desktop dell'utente
    desktop_path = str(Path.home() / "Desktop")
    project_folder_path = os.path.join(desktop_path, project_name)

    # Creare la cartella del progetto se non esiste
    if not os.path.exists(project_folder_path):
        os.makedirs(project_folder_path)

    # Creare una cartella per ogni classifica
    for entry in entry_classifiche_list:
        classifica_name = entry.get()
        if classifica_name:  # Se c'è un nome inserito per la classifica
            classifica_folder = os.path.join(project_folder_path, classifica_name)
            if not os.path.exists(classifica_folder):
                os.makedirs(classifica_folder)

    # Messaggio di conferma
    messagebox.showinfo("Successo", f"Le cartelle per il progetto '{project_name}' sono state create con successo!")

    # Chiudi la finestra delle classifiche
    classifiche_window.destroy()
# Creazione della finestra principale
root = tk.Tk()
root.title("Lavoro")  # Titolo aggiornato

# Impostare la dimensione della finestra (larghezza x altezza)
window_width = 400
window_height = 200

# Otteniamo la risoluzione del monitor
screen_width = root.winfo_screenwidth()
screen_height = root.winfo_screenheight()

# Calcoliamo la posizione per centrare la finestra
position_top = int(screen_height / 2 - window_height / 2)
position_left = int(screen_width / 2 - window_width / 2)

# Impostiamo la geometria della finestra con la posizione calcolata
root.geometry(f"{window_width}x{window_height}+{position_left}+{position_top}")

# Etichetta con il testo "Nome del Progetto"
label_project_name = tk.Label(root, text="Nome del Progetto:")
label_project_name.pack(pady=10)

# Campo di input per scrivere il nome del progetto
entry_project_name = tk.Entry(root)
entry_project_name.pack(pady=5)

# Etichetta con il testo "Nr. Classifiche"
label = tk.Label(root, text="Nr. Classifiche:")
label.pack(pady=10)

# Campo di input per scrivere il numero di classifiche
entry_nr_classifiche = tk.Entry(root)
entry_nr_classifiche.pack(pady=5)

# Bottone "Ok" per confermare l'inserimento
ok_button = tk.Button(root, text="Ok", command=on_ok)
ok_button.pack(pady=10)

# Avvio del loop principale dell'interfaccia grafica
root.mainloop()
-----
classifica 2
import tkinter as tk
from tkinter import messagebox
import subprocess
import sys

# Funzione che verrà eseguita quando si clicca sul pulsante "Invia" nella prima finestra
def submit_first_window():
    nome_progetto = entry_nome_progetto.get()  # Ottieni il valore della casella "Nome progetto"
    numero_classifiche = entry_numero_classifiche.get()  # Ottieni il valore della casella "Numero classifiche"

    # Verifica che i campi non siano vuoti
    if nome_progetto == "" or numero_classifiche == "":
        messagebox.showerror("Errore", "Entrambi i campi devono essere compilati!")
    else:
        try:
            # Verifica che il numero di classifiche sia un numero intero positivo
            numero_classifiche = int(numero_classifiche)
            if numero_classifiche <= 0:
                raise ValueError
        except ValueError:
            messagebox.showerror("Errore", "Il numero delle classifiche deve essere un numero intero positivo.")
            return

        # Chiudi la finestra iniziale
        root_first_window.withdraw()

        # Crea la seconda finestra per inserire i nomi delle classifiche
        open_second_window(nome_progetto, numero_classifiche)


# Funzione per aprire la seconda finestra dove inserire i nomi delle classifiche
def open_second_window(nome_progetto, numero_classifiche):
    # Crea la finestra per le classifiche
    second_window = tk.Toplevel(root_first_window)
    second_window.title("Inserisci i nomi delle classifiche")

    # Lista per memorizzare i nomi delle classifiche
    classifiche_entries = []

    # Etichetta e caselle per i nomi delle classifiche
    for i in range(numero_classifiche):
        label = tk.Label(second_window, text=f"Nome classifica {i + 1}:")
        label.pack(pady=5)

        entry = tk.Entry(second_window, width=30)
        entry.pack(pady=5)

        classifiche_entries.append(entry)

    # Funzione per gestire il pulsante "Invia" nella seconda finestra
    def submit_second_window():
        classifiche_nomi = [entry.get() for entry in classifiche_entries]

        if "" in classifiche_nomi:
            messagebox.showerror("Errore", "Tutti i campi devono essere compilati!")
        else:
            # Mostra i dati raccolti e chiedi la scelta della fotocamera o vibrazioni
            messagebox.showinfo("Dati inviati", f"Nome progetto: {nome_progetto}\n"
                                                f"Numero classifiche: {len(classifiche_nomi)}\n"
                                                f"Nomi classifiche: {', '.join(classifiche_nomi)}")

            # Chiudi la seconda finestra
            second_window.destroy()

            # Chiedi cosa fare dopo
            ask_next_action()

    # Pulsante per inviare i dati delle classifiche
    submit_button_second = tk.Button(second_window, text="Invia", command=submit_second_window)
    submit_button_second.pack(pady=10)


# Funzione per chiedere cosa fare dopo
def ask_next_action():
    # Crea una finestra di selezione per scegliere l'azione
    action_window = tk.Toplevel(root_first_window)
    action_window.title("Scegli un'azione")

    # Etichetta per l'istruzione
    label_action = tk.Label(action_window, text="Vuoi scegliere la webcam o visualizzare i dati vibrazione?")
    label_action.pack(pady=10)

    # Variabile per la scelta
    action_var = tk.StringVar(action_window)
    action_var.set("Scegli...")  # Imposta un valore di default

    # Opzioni per l'Action Menu (webcam o vibrazioni)
    action_menu = tk.OptionMenu(action_window, action_var, "Webcam", "Dati Vibrazione")
    action_menu.pack(pady=10)

    # Funzione per gestire la scelta dell'utente
    def handle_action_selection():
        choice = action_var.get()
        if choice == "Webcam":
            print("Azione webcam selezionata")  # Sostituire con il codice per la webcam
            subprocess.Popen([sys.executable, 'fotocamera_vibrazioni.py'])

            # Qui puoi aggiungere il codice per aprire la webcam
        elif choice == "Dati Vibrazione":
            print("Visualizzazione dei dati vibrazione selezionata")  # Sostituire con il codice per visualizzare i dati vibrazione
            # Qui puoi aggiungere il codice per visualizzare i dati vibrazione
        else:
            messagebox.showerror("Errore", "Devi selezionare un'opzione!")

        action_window.destroy()  # Chiudi la finestra di selezione

    # Pulsante per confermare la selezione
    submit_button_action = tk.Button(action_window, text="Conferma", command=handle_action_selection)
    submit_button_action.pack(pady=10)


# Crea la finestra principale (prima finestra)
root_first_window = tk.Tk()
root_first_window.title("Form di inserimento progetto")

# Etichetta e casella di testo per "Nome progetto"
label_nome_progetto = tk.Label(root_first_window, text="Nome progetto:")
label_nome_progetto.pack(pady=5)

entry_nome_progetto = tk.Entry(root_first_window, width=30)
entry_nome_progetto.pack(pady=5)

# Etichetta e casella di testo per "Numero classifiche"
label_numero_classifiche = tk.Label(root_first_window, text="Numero classifiche:")
label_numero_classifiche.pack(pady=5)

entry_numero_classifiche = tk.Entry(root_first_window, width=30)
entry_numero_classifiche.pack(pady=5)

# Pulsante per inviare i dati della prima finestra
submit_button_first = tk.Button(root_first_window, text="Invia", command=submit_first_window)
submit_button_first.pack(pady=10)

# Avvia la finestra principale
root_first_window.mainloop()
-----
foto classifica
import tkinter as tk
from tkinter import messagebox, Button, Toplevel
import os
from pathlib import Path
import cv2
from PIL import Image, ImageTk

# Variabili globali
foto_index = 0
project_folder_path = ""
classifiche_names = []
cap = None
classifiche_window = None  # Per tener traccia della finestra delle classifiche

# Funzione che verrà chiamata quando si clicca su "Ok"
def on_ok():
    global classifiche_names

    nr_classifiche = entry_nr_classifiche.get()  # Otteniamo il numero di classifiche
    project_name = entry_project_name.get()  # Otteniamo il nome del progetto

    if nr_classifiche.isdigit():  # Verifica se è un numero
        nr_classifiche = int(nr_classifiche)

        if not project_name:  # Se non è stato inserito un nome del progetto, mettiamo un valore predefinito
            project_name = "Lavoro"

        # Ottieni il percorso del Desktop dell'utente
        desktop_path = str(Path.home() / "Desktop")
        global project_folder_path
        project_folder_path = os.path.join(desktop_path, project_name)

        # Crea la cartella del progetto se non esiste
        if not os.path.exists(project_folder_path):
            os.makedirs(project_folder_path)

        open_classifiche_window(nr_classifiche, project_name)
        root.withdraw()  # Nasconde la finestra principale senza chiuderla
    else:
        messagebox.showerror("Errore", "Per favore, inserisci un numero valido.")

# Funzione per aprire una nuova finestra con i campi per le classifiche
def open_classifiche_window(nr_classifiche, project_name):
    global classifiche_names, classifiche_window

    # Nuova finestra popup per inserire le classifiche
    classifiche_window = tk.Toplevel(root)
    classifiche_window.title(f"Inserisci Classifiche per {project_name}")

    # Lista per raccogliere gli entry delle classifiche
    entry_classifiche_list = []

    # Creare i campi di input per le classifiche
    for i in range(nr_classifiche):
        label = tk.Label(classifiche_window, text=f"Classifica {i + 1}:")
        label.pack(pady=5)
        entry_classifica = tk.Entry(classifiche_window)
        entry_classifica.pack(pady=5)
        entry_classifiche_list.append(entry_classifica)

    # Bottone "Conferma" per avviare la fotocamera e scattare le foto
    confirm_button = tk.Button(classifiche_window, text="Conferma",
                               command=lambda: start_camera(entry_classifiche_list, classifiche_window))
    confirm_button.pack(pady=10)

# Funzione per avviare la fotocamera e scattare le foto per ogni classifica
def start_camera(entry_classifiche_list, classifiche_window):
    global classifiche_names, cap, foto_index

    # Lista di classifiche con i relativi nomi
    classifiche_names = [entry.get() for entry in entry_classifiche_list]

    # Chiudi la finestra delle classifiche
    classifiche_window.destroy()

    # Crea la finestra per la videocamera
    window = tk.Toplevel(root)
    window.title(f"Scatta Foto - Foto: {foto_index}")

    # Crea un'etichetta per mostrare il video
    label = tk.Label(window)
    label.pack()

    # Pulsante per scattare la foto
    scatta_button = Button(window, text="Scatta Foto", command=lambda: scatta_foto(window, label))
    scatta_button.pack()

    # Pulsante per chiudere la finestra
    close_button = Button(window, text="Termina", command=lambda: terminate(classifiche_window, window))
    close_button.pack()

    # Avvia la videocamera
    cap = cv2.VideoCapture(0)

    # Funzione per aggiornare la finestra con l'immagine della telecamera
    def aggiorna_finestra():
        ret, frame = cap.read()
        if ret:
            # Converti il frame in formato RGB
            frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
            img = Image.fromarray(frame_rgb)
            imgtk = ImageTk.PhotoImage(image=img)

            # Mostra l'immagine nella finestra Tkinter
            label.config(image=imgtk)
            label.imgtk = imgtk

        # Riprova ad aggiornare la finestra ogni 10ms
        window.after(10, aggiorna_finestra)

    # Avvia l'aggiornamento della finestra
    aggiorna_finestra()

    # Avvia il loop per la finestra della fotocamera
    window.mainloop()

# Funzione per scattare la foto
def scatta_foto(window, label):
    global foto_index, classifiche_names

    ret, frame = cap.read()

    if ret:
        # Crea una finestra pop-up per selezionare la classifica (Menu a discesa)
        classifica_window = Toplevel(window)
        classifica_window.title("Seleziona Classifica")

        # Variabile per il menu a discesa
        selected_classifica = tk.StringVar()
        selected_classifica.set(classifiche_names[0])  # Impostiamo la prima classifica come default

        # Crea il menu a discesa con le classifiche disponibili
        classifica_menu = tk.OptionMenu(classifica_window, selected_classifica, *classifiche_names)
        classifica_menu.pack(pady=10)

        # Bottone per confermare la selezione della classifica
        confirm_button = tk.Button(classifica_window, text="Conferma",
                                   command=lambda: save_photo(frame, selected_classifica.get(), classifica_window, window))
        confirm_button.pack(pady=10)

    else:
        messagebox.showerror("Errore", "Impossibile scattare la foto. Riprova.")

# Funzione per salvare la foto dopo aver scelto la classifica
def save_photo(frame, selected_classifica, classifica_window, window):
    global foto_index, project_folder_path

    # Salva la foto nella cartella della classifica scelta
    classifica_folder = os.path.join(project_folder_path, selected_classifica)

    if not os.path.exists(classifica_folder):
        os.makedirs(classifica_folder)

    # Salva la foto come file .jpg
    photo_name = f"foto_{foto_index + 1}.jpg"
    photo_path = os.path.join(classifica_folder, photo_name)
    cv2.imwrite(photo_path, frame)
    print(f"Foto {foto_index + 1} salvata in {photo_path}")

    # Incrementa l'indice delle foto
    foto_index += 1

    # Aggiorna il titolo della finestra con il numero di foto scattate
    window.title(f"Foto: {foto_index}")

    # Chiudi la finestra di selezione della classifica
    classifica_window.destroy()

# Funzione per terminare il processo e chiudere tutte le finestre
def terminate(classifica_window, window):
    classifica_window.destroy()  # Chiudi la finestra delle classifiche
    window.destroy()  # Chiudi la finestra della fotocamera
    root.quit()  # Chiudi la finestra principale
    cap.release()  # Rilascia la videocamera
    cv2.destroyAllWindows()  # Chiudi tutte le finestre di OpenCV

# Crea la finestra principale
root = tk.Tk()
root.title("Lavoro")

# Impostare la dimensione della finestra (larghezza x altezza)
window_width = 400
window_height = 200

# Otteniamo la risoluzione del monitor
screen_width = root.winfo_screenwidth()
screen_height = root.winfo_screenheight()

# Calcoliamo la posizione per centrare la finestra
position_top = int(screen_height / 2 - window_height / 2)
position_left = int(screen_width / 2 - window_width / 2)

# Impostiamo la geometria della finestra con la posizione calcolata
root.geometry(f"{window_width}x{window_height}+{position_left}+{position_top}")

# Etichetta con il testo "Nome del Progetto"
label_project_name = tk.Label(root, text="Nome del Progetto:")
label_project_name.pack(pady=10)

# Campo di input per scrivere il nome del progetto
entry_project_name = tk.Entry(root)
entry_project_name.pack(pady=5)

# Etichetta con il testo "Nr. Classifiche"
label = tk.Label(root, text="Nr. Classifiche:")
label.pack(pady=10)

# Campo di input per scrivere il numero di classifiche
entry_nr_classifiche = tk.Entry(root)
entry_nr_classifiche.pack(pady=5)

# Bottone "Ok" per confermare l'inserimento
ok_button = tk.Button(root, text="Ok", command=on_ok)
ok_button.pack(pady=10)

# Avvia il loop principale dell'interfaccia grafica
root.mainloop()




# Rilascia la videocamera quando la finestra viene chiusa
cap.release()
cv2.destroyAllWindows()
# ciao
