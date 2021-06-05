import PySimpleGUI as sg
from random import randint
import pyperclip as clip
from PIL import Image
import os
import webbrowser as web


####################################################################################
def passagebinaire(message): #fonction qui transforme un message en binaire, par exemple avec la lettre a, la fonction transforme la lettre en sa valeur ascii puis transforme la valeur en binaire
    binaire = ""
    for i in message:
        binaire += (bin(ord(i))[2:].rjust(8, '0'))
    return binaire


def stegano(message, image, nomimage): #fonction qui cache un message dans une image avec en entré le message l'imaage et le nom de la nouvelle image
    jpgvalue = 0
    if image[-3:] == "jpg": #verification du format de l'image, si le format est jpg, il y a une conversion en pgn car la programme fonctionne uniquement avec les images png
        transfo = Image.open(r'' + image)
        transfo.save(r'' + image[:-4] + "png" + ".png")
        image = (image[:-4] + "png" + ".png")
        jpgvalue = 1
    lienimage = image
    image = Image.open(image) #decomposition de l'image en trois couche r,g,b
    w, h = image.size
    r, g, b = image.split()
    r = list(r.getdata()) #passage de la couche rouge en liste de valeurs
    message = passagebinaire(message) #passage du message en binaire
    if len(r) - 16 < len(message): # verifie que le message n'est pas trop long pour l'image
        return False
    for i in range(len(message) + 16):#passe chaque pixel a sa valeur paire inferieur pour avoir que des valeurs paires
        if r[i] % 2 == 1:
            r[i] += 1
        if r[i] == 256:
            r[i] -= 2
    longueur = bin(len(message))[2:].rjust(16, "0")
    longueur = list(map(int, longueur))

    for j in range(16):#codage de la longueur du message dans les 16 premiers pixels
        r[j] += longueur[j]
    for k in range(len(message)):#codage du message dans les pixels suivants
        entier = int(message[k])
        r[k + 16] += entier
    nr = Image.new("L", (w, h))#création de la nouvelle image
    nr.putdata(r)
    imgnew = Image.merge('RGB', (nr, g, b))

    for i in range(len(lienimage)):#modification du nom de l'image
        if lienimage[i] == "/":
            valeurslash = i
    nomsave = lienimage[:(valeurslash + 1)] + nomimage + "crypt.png"
    nomfinal = nomimage + "crypt"
    imgnew.save(nomsave)#sauvergarde de la nouvelle image
    if jpgvalue == 1:
        os.remove(lienimage)

    return nomfinal, # retourne le nom de l'image modifié ainsi que la valeur 1 pour prouver que la fonction est arrivé a son terme


def decryptstegano(image):#fonction de decryptage avec seulement une image en entrée
    im = Image.open(image)#decomposition de l'image en trois couche r,g,b
    r, g, b = im.split()
    r = list(r.getdata())

    longueur = ""
    for i in range(16):#recuperation de la longueur du message dans les 16 premiers pixels
        longueur += (str(r[i] % 2))
    longueur = int(longueur, 2)

    message = ""
    for i in range(longueur):#recuperation du message
        message += (str(r[i + 16] % 2))

    messagedecode = ""
    for i in range(int(longueur / 8)):#transformation du message binaire en message normal
        messagedecode += chr(int(message[0:8], 2))
        message = message[8:]
    messagedecode = messagedecode[:-1]

    return messagedecode,1#retourne le message decodé ainsi que la valeurs 1 en verification


#############################################################################################
# police pre definit pour gagner en clareté, éviter les répétitions
police_modif = {"font": ("Arial Greek", 10)}
police_defi = {"font": ("Arial Greek", 17)}
police_title = {"font": ("Arial Greek", 20)}
police_aide = {"font": ("Arial Greek", 11)}
police_aide_titre = {"font": ("Arial Greek", 12, "bold")}

color_back = {"background_color": "#565268"}

sg.theme("DarkTeal4")  # rajoute une couleur par défaut? #494460 par défault en background

BlocBandeau = [[sg.Button("Aide", key="HelpCode"), sg.Button("Retour", key="RetourCode")]]

BlocCode = [[sg.Text("Entrez votre texte :", **police_title, **color_back)],
            [sg.Multiline(**police_modif, size=(35, 5), key="InputMessage")],
            [sg.Text("Choisissez les paramètres\nde codage :", **police_defi, justification="c", **color_back,
                     pad=((0, 0), (20, 10)))],
            [sg.OptionMenu(("Codage", "Decodage"), default_value="Codage", key="SelectModeCode"),
             sg.OptionMenu(("Alphanumérique", "Tout caractère"), default_value="Alphanumérique",
                           key="SelectAlphaCode")],
            ]

BlocClef = [[sg.Text("Choisissez votre clef :", **police_title, **color_back)],
            [sg.Slider(range=(1, 64), default_value=1, resolution=1, orientation="h", key="TailleClef", **color_back)],
            [sg.OptionMenu(("Alphanumérique", "Tout caractère"), key="SelectAlphaClef", default_value="Alphanumérique",
                           pad=((0, 5), (8, 25))),
             sg.Button("Générer", pad=((5, 0), (8, 25)), key="BoutonGenererClef")],
            [sg.Multiline(**police_modif, size=(35, 3), key="OutInputClef")]
            ]

BlocResult = [[sg.Text("Résultat :", **police_title, **color_back)],
              [sg.Button("OK", font=("Arial Greek", 30, "bold"), size=(22, 1), border_width=20,
                         key="ButtonOkGenerateCode", focus=True, pad=((0, 0), (5, 10)))],
              [sg.Button("Copier", key="CopierResult"),
               sg.Multiline(**police_modif, size=(73, 4), key="OutPutMessage")],
              # [sg.Input(key="lienCodeTxt"),sg.FileBrowse(),sg.Button("Modifier un txt")]
              ]

assemblageCrytpage = [[sg.Column(BlocBandeau, element_justification="right", **color_back, pad=((470, 0), (0, 0)))],
                      [sg.Column(BlocCode, element_justification="c", pad=((0, 5), (0, 0)), **color_back),
                       sg.Column(BlocClef, element_justification="c", pad=((5, 0), (28, 0)), **color_back)],
                      [sg.ProgressBar(1, orientation='h', size=(45, 30), key='FakeProgress', visible=True)],
                      [sg.Column(BlocResult, element_justification="c", pad=((5, 5), (0, 0)), **color_back, )]
                      ]


def fct_helpcodecryptage():  # affiche une fenêtre d'aide
    helpcodelayout = [
        [sg.Text("Bienvenue dans le menu d'aide au cryptgae", pad=((0, 0), (0, 20)), **police_aide_titre)],
        [sg.Text("CHOIX DU TEXTE :", pad=((0, 0), (0, 0)), **police_aide_titre)],
        [sg.Text("Tapez simplement votre texte dans le case sous 'Entrez votre texte :'", **police_aide)],
        [sg.Text("CHOIX DE LA CLEF :", pad=((0, 0), (15, 0)), **police_aide_titre)],
        [sg.Text("Vous avez deux possibilités :", **police_aide)],
        [sg.Text(
            "- Soit vous taper la clef manuellement, dans la case sous les options de 'Choisissez votre clef :',\n  mais cela rend le cryptage moins sûr",
            **police_aide)],
        [sg.Text(
            "- Soit vous la générez en choisissant : la longueur avec la barre glissante (de 1 à 64),\n  puis sélectionnez avec le menu déroulant en alpha (de a à z), ou en tout caractère (plus sûr) qui prend toute la table ASCII\n  Puis cliquer sur 'Générer'",
            **police_aide)],
        [sg.Text("LANCEMENT DU CODAGE", pad=((0, 0), (15, 0)), **police_aide_titre)],
        [sg.Text("Pour lancer le cryptage il faut :", **police_aide)],
        [sg.Text("- S'assurer que les cases de texte et de clef soient rempli", **police_aide)],
        [sg.Text("- Choisir dans le menu déroulant du côté 'texte' alpha ou tout caractère", **police_aide)],
        [sg.Text("- Choisir 'codage' ou 'decodage', dans le menu déroulant du côté 'texte'")],
        [sg.Text("Enfin, cliquez sur 'OK' pour lancer le cryptage / decryptage")],
        [sg.Button("J'ai compris", key="OkTerm", pad=((0, 0), (10, 0)))]
        ]
    aide_de_code = sg.Window("Helpcode", helpcodelayout)

    while True:
        event_wait, values_wait = aide_de_code.read()
        if event_wait == sg.WIN_CLOSED or event_wait == "OkTerm":
            break
    aide_de_code.close()


##################################GUI decryptage steganographie#######################################################
choix_du_fichierdecrypt = [[sg.Text("Choisissez votre image à décoder : ", font=("Arial Greek", 12), border_width=2)],
                           [sg.Input(key="liendecrypt"), sg.FileBrowse()]]

demarrerdecrypt = [
    [sg.Button('Demarrer', key="demarrerdecrypt", font=("Arial Greek", 14), size=(10, 1), border_width=10)]]

decryptage = [[sg.Text("Message decrypté :", font=("Arial Greek", 12), pad=((0, 0), (0, 8)))],
              [sg.Button(button_text="Copier", key="copierdecrypt"),
               sg.Multiline(auto_size_text=True, key="outputdecrypt")]]
layoutdecryptsize = [[sg.Column(choix_du_fichierdecrypt, pad=((10, 10), (0, 0)))],
                     [sg.Column(demarrerdecrypt, pad=((130, 0), (8, 8)))],
                     [sg.Column(decryptage, pad=((10, 10), (0, 8)))]]
layout_decryptage = [[sg.Column(layoutdecryptsize, pad=((85, 86), (0, 0)))]]
####################################GUI cryptage steganographie####################################################################

choix_imagecrypt = [[sg.Text("Choix de l'image a crypter :", font=("Arial Greek", 12))],
                    [sg.Input(key="lienchoiximagecrypt"), sg.FileBrowse()]]

choix_nomimagecrypt = [[sg.Text("Choix du nom de la nouvelle image :", font=("Arial Greek", 12))],
                       [sg.Input(key="nomimagecrypt")]]

choix_messagecrypt = [[sg.Text("Choix du message : ", font=("Arial Greek", 12))],
                      [sg.Multiline(auto_size_text=True, key="zonemessagecrypt"),
                       sg.Button(button_text="Demarrer", border_width=3, size=(0, 3), key="departcrypt")]]

image_modifiecrypt = [[sg.Text('Image modifié :', font=("Arial Greek", 12)), sg.Input(key="lienimagefinalcrypt")]]

layoutcryptsize = [[sg.Column(choix_imagecrypt)],
                   [sg.Column(choix_messagecrypt)],
                   [sg.Column(choix_nomimagecrypt, pad=((5, 0), (0, 8)))],
                   [sg.Column(image_modifiecrypt, pad=((0, 10), (0, 8)))]]
layout_cryptage = [[sg.Column(layoutcryptsize, pad=((77, 57), (0, 0)))]]
####################################GUI page d'acceuil#########################################################

AccueilText = [[sg.Text("ACCUEIL", **police_title, pad=((230, 0), (0, 5))),
                sg.Button("Fermer", key="CloseButton", pad=((180, 8), (0, 5)))]]

CryptageAccueil = [[sg.Text("A propos du cryptage :", **police_defi)],
                   [sg.Multiline(
                       "Lorsque l'on parle de cryptage on peut entendre parler de plusieurs termes différent : 'codage', 'chiffrement', où bien sûr 'cryptage'. Ces différents termes veulent souvent signifer la même chose : modifier un message à l'aide d'une clef de sorte a ce que ce message soit indéchiffrable sans cette même clef (que dans le cas du cryptage dit 'symétrique' (il faut la même clef pour chiffrer et pour déchiffrer)).\nPour en savoir plus sur son fonctionnement et son histoire, vous pouvez cliquer sur le bouton ci-dessous."
                       , **police_aide, disabled=True, autoscroll=True, size=(30, 15))],
                   [sg.Button("En savoir plus", key="espcrypt")]]

SteganoAccueil = [[sg.Text("A propos de la stégano :", **police_defi)],
                  [sg.Multiline(
                      "La stéganographie est l'art de la dissimulation, elle consiste à cacher des messages dans des fichiers audio, vidéo, ou autres. Dans notre cas, le logiciel va cacher le message dans une image en modifiant les couleurs de manière infime.\nPour en savoir plus sur son fonctionnement et son histoire, vous pouez cliquer sur le bouton ci-dessous.",
                      disabled=True, autoscroll=True, size=(30, 15), **police_aide)],
                  [sg.Button("En savoir plus", key="espstega")]
                  ]

################################GUI final de la page de steganographie###########################################################

layoutfinalstegano = [[sg.Button('Aide', key="aidestegano", pad=((512, 8), (0, 0))),
                       sg.Button('Retour', key="retourstegano", pad=((0, 5), (0, 0)))],
                      [sg.Frame("Cryptage", layout_cryptage, font=12)],
                      [sg.Frame("Decryptage", layout_decryptage, font=12)]]

tabAccueil = [[sg.Column(AccueilText)],
              [sg.Frame("Cryptage", CryptageAccueil, **police_title),
               sg.Frame("Steganographie", SteganoAccueil, **police_title)]]

assemblageTabs = [[sg.Tab("Accueil", tabAccueil, key="tabAccueil", element_justification="center")],
                  [sg.Tab("Cryptage", assemblageCrytpage, key="tabCrytpage", element_justification="center")],
                  [sg.Tab("Stegano", layoutfinalstegano, key="tabStegano")],
                  ]

doubleTabs = [[sg.TabGroup(assemblageTabs, key="allTabs")]]

tabkey = ("tabAccueil", "tabCryptage", "tabStegano")


#########################################################################################


# codage=True : code, codage=False : decode ;
# si alpha=True alors alpha pour le code ET la clef sinon ASCII 32 de 255
def fct_codage(str_a_changer, clef, codage, alpha):
    # verifie que les paramêtres sont cohérents
    if not str_a_changer or not clef or type(codage) != bool and type(alpha) != bool:
        return [None, -1]
    elif type(clef) != str or type(str_a_changer) != str:
        return [None, -1]

    if alpha == False:
        moinsSiAlpha = 0
        tableauValeur = [10] + [a for a in range(32, 127)] + [129, 141, 143, 144, 157] + [a for a in
                                                                                          range(161, 173)] + [a for a in
                                                                                                              range(174,
                                                                                                                    256)]

    else:  # change la limite (normal) mais aussi : met tout en alpha, et enleve un bug MoinsSiAlpha, pour permettre le passage du cryptage par l'alpha
        tableauValeur = []
        for a in range(48, 58):
            tableauValeur.append(a)
        for a in range(97, 123):
            tableauValeur.append(a)

        str_a_changer = fct_transformeAlpha(str_a_changer)
        if str_a_changer[1] != -1:
            str_a_changer = str(str_a_changer[0])
        else:
            print("Erreur dans la transformation en alphanumérique de la clef")

        clef = fct_transformeAlpha(clef)
        if clef[1] == 1:
            clef = str(clef[0])
        else:
            print("Erreur dans la transformation en alphanumérique de la clef")

        moinsSiAlpha = 97

    clefTableau = [0] * len(clef)
    nbrTourClef = 0

    for a in clef:
        clefTableau[nbrTourClef] = ord(a) - moinsSiAlpha
        nbrTourClef += 1
    nbrTourCodage = 0
    str_change = ""

    if codage == True:  # code

        for b in str_a_changer:  # crypte le message
            ord_b = ord(b)

            if nbrTourCodage >= len(clefTableau):  # si a fait le tour de la clef, reset au début
                nbrTourCodage = 0

            for c in range(clefTableau[nbrTourCodage]):

                if ord_b == tableauValeur[-1]:
                    ord_b = tableauValeur[0]
                else:
                    ord_b = tableauValeur[tableauValeur.index(ord_b) + 1]

            nouveauCaractere = ord_b

            str_change += chr(nouveauCaractere)
            nbrTourCodage += 1

        return [str_change, 1]


    elif codage == False:  # decrypte , voir crypte c'est pareil

        for b in str_a_changer:  # decrypte le message
            ord_b = ord(b)

            if nbrTourCodage >= len(clefTableau):  # si a fait le tour de la clef, reset au début
                nbrTourCodage = 0

            for c in range(clefTableau[nbrTourCodage]):

                if ord_b == tableauValeur[0]:
                    ord_b = tableauValeur[-1]
                else:
                    ord_b = tableauValeur[tableauValeur.index(ord_b) - 1]

            nouveauCaractere = ord_b

            str_change += chr(nouveauCaractere)
            nbrTourCodage += 1

        return [str_change, 1]


def fct_creationClef(longueur, alpha):  # si alpha = True que abc.. sinon ASCII de 0 à 255
    clefCode = ""
    tableauValeurClef = []

    if not longueur or type(alpha) != bool:  # verifie que tout est nickel
        return [None, -1]
    elif type(longueur) != int:
        return [None, -1]

    elif alpha == True:
        for a in range(48, 58):
            tableauValeurClef.append(a)
        for a in range(97, 123):
            tableauValeurClef.append(a)

    else:
        tableauValeurClef = [a for a in range(32, 127)] + [129, 141, 143, 144, 157] + [a for a in range(161, 173)] + [a
                                                                                                                      for
                                                                                                                      a
                                                                                                                      in
                                                                                                                      range(
                                                                                                                          174,
                                                                                                                          256)]

    for a in range(0, longueur - 1):
        temp = tableauValeurClef[randint(0, (len(tableauValeurClef) - 1))]
        if temp == 151:
            clefCode = "_"
        else:
            clefCode += str(chr(temp))
    return [clefCode, 1]


def fct_transformeAlpha(message):  # rend ce qui est envoyé alphanumérique
    if message and type(message) == str:

        message = message.lower()  # mise en minuscule

        message = message.replace("é", "e")  # remplace des lettre avec des accents, en lettre sans accents
        message = message.replace("è", "e")
        message = message.replace("ë", "e")
        message = message.replace("ê", "e")
        message = message.replace("à", "a")
        message = message.replace("â", "a")
        message = message.replace("ä", "a")
        message = message.replace("ï", "i")
        message = message.replace("î", "i")
        message = message.replace("ö", "o")
        message = message.replace("ô", "o")
        message = message.replace("ü", "u")
        message = message.replace("û", "u")
        message = message.replace("ÿ", "y")
        message = message.replace("ç", "c")

        nouveauMessage = ""

        for d in message:
            if (ord(d) >= 97 and ord(d) <= 122) or (ord(d) >= 48 and ord(d) <= 57):
                nouveauMessage += d

        return [nouveauMessage, 1]
    else:
        return [None, -1]


###########################################################################################

# fonction d'appel qui évite les erreurs d'oubli
def fct_appel_code():
    if values["InputMessage"] == "\n":
        sg.popup("Erreur texte", "Vous avez oublié de rentrer un texte !")
    elif values["OutInputClef"] == "\n":
        sg.popup("Erreur clef", "Vous avez oublié de générer ou entrer une clef !")
    else:
        window["FakeProgress"].update(visible=True, current_count=1)
        progress_bar = window.FindElement('FakeProgress')
        for i in range(100):
            wait_event, wait_values = window.Read(timeout=1)
            if wait_event == sg.WIN_CLOSED:
                break
            progress_bar.UpdateBar(i + 1, 100)
        window["FakeProgress"].update(visible=False, current_count=1)
        window["OutPutMessage"].update()

        if values["SelectModeCode"] == "Codage":
            sens_codage_apl = True
        else:
            sens_codage_apl = False
        if values["SelectAlphaCode"] == "Alphanumérique":
            alpha_apl = True
        else:
            alpha_apl = False

        message_a_modif_apl = values["InputMessage"][:(len(values["InputMessage"]) - 1)]
        clef_apl = values["OutInputClef"][:(len(values["OutInputClef"]) - 1)]

        codage_apl = fct_codage(message_a_modif_apl, clef_apl, sens_codage_apl, alpha_apl)

        if codage_apl[1] != -1:
            window["OutPutMessage"].update(value=codage_apl[0][:len(str(codage_apl[0]))])
        else:
            print("Erreur lors du cryptage")


def fct_appel_clef():
    if values["SelectAlphaClef"] == "Alphanumérique":
        alpha_apl_clef = True
    else:
        alpha_apl_clef = False
    nv_clef = fct_creationClef(int(values["TailleClef"] + 1), alpha_apl_clef)

    if nv_clef[1] != -1:
        window["OutInputClef"].Update(value=nv_clef[0])
    else:
        print("Erreur lors de la création de la clef")

# fonction qui execute les fonctions precedentes quand des boutons sont préssés (partie cryptage steganographie)
def fct_decrypt():
    if event == "demarrerdecrypt":
        verification = values["liendecrypt"][-3:]
        if verification=="":
            sg.popup("Vous devez choisir une image", title="Erreur")
            return
        if verification == "png":
            message = decryptstegano(values["liendecrypt"])
            window["outputdecrypt"].update(value=message[0])
        if message[1]==1:
            sg.popup("Decryptage réalisé avec succès !")
        else:
            sg.popup("Erreur", title="Erreur")

    elif event == "copierdecrypt":
        clip.copy(values["outputdecrypt"])

    elif event == "aidedecrypt":
        sg.popup("Bienvenue dans le menu d'aide", "Commencez par choisir votre image a décoder (format png)",
                 "Appuyer sur le bouton demarrer et attendez",
                 "Une fois l'opération finis, vous pouvez appuyer sur copier ou simplement recopier le message",
                 title="Aide")


# fonction qui execute les fonctions precedentes quand des boutons sont préssés (partie decryptage steganographie)
def fct_crypt():
    if event == "departcrypt":
        verification = values["lienchoiximagecrypt"][-3:]
        if values["lienchoiximagecrypt"]== "":
            sg.popup("Vous devez choisir une image", title="Erreur")
            return
        if (verification!="jpg") and (verification!="png"):
            sg.popup("L'image doit être au fromat jpg ou png", title="Erreur")
            return
        if values["zonemessagecrypt"] == '\n':
            sg.popup("Vous devez entrer un message", title="Erreur")
            return
        if (verification == "png") or (verification == "jpg"):
            image = stegano(values["zonemessagecrypt"], values["lienchoiximagecrypt"], values["nomimagecrypt"])
            window["lienimagefinalcrypt"].update(value=image[0])
        if image[1]==1:
            sg.popup("Cryptage réalisé avec succès !")
        else:
            sg.popup("Erreur", title="Erreur")


#popup de texte pour l'aide de la steganographie
def fct_aidestegano():
    sg.popup("Bienvenue dans le menu d'aide", "", "AIDE CRYPTAGE :",
             "Commencez par choisir votre image à coder (format png ou jpg)",
             "Selectionnez votre message"
             "Appuyer sur le bouton demarrer et attendez",
             "Une fois l'opération finis, l'image est disponible dans le fichier de l'image source sous le nom indiqué dans sur la page",
             "",
             "AIDE DECRYPTAGE :", "Commencez par choisir votre image a décoder (format png)",
             "Appuyer sur le bouton demarrer et attendez",
             "Une fois l'opération finis, vous pouvez appuyer sur copier ou simplement recopier le message", ""
             , title="Aide")


def fct_fermer():  # petite fenêtre
    fermer_layout = [[sg.Text("Voulez vous vraiment fermer ?", pad=((0, 0), (0, 20)), **police_aide)],
                     [sg.Button("Oui", key="valide_ferme"), sg.Button("Non", key="pas_valide_ferme")]]

    fermer_win = sg.Window("Fermer ? :(", fermer_layout, element_justification="center")

    while True:
        event_wait, values_wait = fermer_win.read()
        if event_wait == sg.WIN_CLOSED or event_wait == "pas_valide_ferme":
            var_fermer = False
            break
        elif event_wait == "valide_ferme":
            var_fermer = True
            break

    fermer_win.close()
    if var_fermer == True:
        return True


window = sg.Window('LicoCrypto', doubleTabs, resizable=True,
                   icon=r'C:\Users\joric\Documents\Cours\Terminal\NSI\Projetpalmier2000\icon.ico',
                   element_justification="center")  # creation de la fenêtre général, donne son nom, son contenu, la position par défault de ses éléments...

event, values = window.read(timeout=1)  # attend avant de faire disparaitre
window["FakeProgress"].update(visible=False,
                              current_count=1)  # fait disparaitre la barre de chargement (est apparu un fraction de seconde)

while True:
    event, values = window.read()
    if event == sg.WIN_CLOSED:
        break
    elif event == "ButtonOkGenerateCode":
        fct_appel_code()
    elif event == "BoutonGenererClef":
        fct_appel_clef()
    elif event == "CopierResult":
        clip.copy(values["OutPutMessage"][:-1])
    elif event == "HelpCode":
        fct_helpcodecryptage()
    elif (event == "demarrerdecrypt") or (event == "copierdecrypt") or (event == "aidedecrypt"):
        fct_decrypt()
    elif (event == "departcrypt") or (event == "aidecrypt"):
        fct_crypt()
    elif event == "aidestegano":
        fct_aidestegano()
    elif event == "retourstegano" or event == "RetourCode":
        window["tabAccueil"].select()
    elif event == "espcrypt":
        web.open("https://fr.wikipedia.org/wiki/Chiffrement")
    elif event == "espstega":
        web.open("https://fr.wikipedia.org/wiki/Stéganographie")
    elif event == "CloseButton":
        if fct_fermer() == True:
            break

# Close Window
window.close()
