# binpfrom sys import exit as sluiten
from random import choice
from string import ascii_lowercase
import time


def lijst_maken():
    """

    :return:
    """
    l_woorden = open('woorden.lst', 'r')
    l_woordenlijst = l_woorden.read().split()
    l_woorden.close()
    return l_woordenlijst


def woord_toevoegen(w_toegevoegde_woord, w_woordenlijst):
    l_woord = ''
    if w_toegevoegde_woord not in w_woordenlijst:
        for letter in w_toegevoegde_woord:
            if letter.isalpha():
                l_woord += letter
        if l_woord in w_woordenlijst:
            verkeerde_woord = 'Dit woord komt al voor in deze lijst.'
        elif len(l_woord) < 3 or len(w_toegevoegde_woord) > 38:
            verkeerde_woord = 'Woorden mogen niet korter zijn dan 3 ' \
                              'letters en niet langer zijn dan 38 letters.'
        else:
            w_woordenlijst.append(l_woord)
            w_woordenlijst.sort(key=len, reverse=False)
            verkeerde_woord = 'Het woord ' + l_woord + ' is toegevoegd.'
    else:
        verkeerde_woord = 'Dit woord komt al voor in deze lijst.'

    return w_woordenlijst, verkeerde_woord


def letter_raden(w_gis, w_gekozen_woord):
    geraden = True
    updated_woord = ''
    for letter in w_gekozen_woord:
        if letter not in w_gis:
            updated_woord += '*'
            geraden = False
        else:
            updated_woord += letter
    return updated_woord, geraden


def woord_raden(w_geraden_letter, w_gekozen_woord, w_beurten):
    if w_geraden_letter == w_gekozen_woord:
        return True, w_beurten
    else:
        w_beurten += 3
        if w_beurten > 9:
            w_beurten = 9
        return False, w_beurten


def beurten_tellen(b_letter, b_gekozen_woord, b_beurt, b_gebruikte_letters):
    if b_letter in b_gebruikte_letters:
        b_beurt = b_beurt
    elif b_letter not in b_gekozen_woord:
        b_beurt += 1
    return b_beurt


def galg_maken(g_beurten):
    g_figuuren = [
        '         ;         ;         ;         ;         ;         ',
        '         ;  |      ;  |      ;  |      ;  |      ; / \     ',
        '   ____  ;  |/     ;  |      ;  |      ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |      ;  |      ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |    O ;  |      ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |    O ;  |    | ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |   \O ;  |    | ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |   \O/;  |    | ;  |      ; / \     ',
        '   ____  ;  |/   | ;  |   \O/;  |    | ;  |   /  ; / \     ',
        '   ____  ;  |/   | ;  |   \O/;  |    | ;  |   / \; / \     '
    ]
    return g_figuuren[g_beurten].split(';')


def gebruikte_letters(g_gis):
    letters = []
    for letter in ascii_lowercase:
        if letter in g_gis:
            letters.append(letter)
        else:
            letters.append('* ')
    return letters


def tijd_tellen():
    t_tijd = time.time()
    return t_tijd


def punten_tellen(p_tijd, p_gekozen_woord, p_beurten):
    p_punten = 10000 * (len(p_gekozen_woord) / ((p_tijd * p_beurten) + 1))
    p_punten = int(p_punten)
    return p_punten


def rankinglijst_maken():
    ranking_file = open('ranking.txt', 'r')
    lijst_ranking = []
    inhoud_bestand = ranking_file.read().splitlines()
    for rij in inhoud_bestand:
        lijst_ranking.append(rij.split(';'))
    ranking_file.close()
    return lijst_ranking


def ranking_vullen(r_lijst, r_naam, r_gekozen_woord, r_beurten, r_tijd,
                   r_punten):
    """
    Functiebeschrijving: Deze funktie maakt een ranking.
    :param r_lijst: scores lijst
    :param r_naam: speler naam
    :param r_gekozen_woord: random woord van woordenlijst
    :param r_beurten: aantal fouten
    :param r_tijd: tijd van de spel
    :param r_punten: gehaalde punten
    :return: geeft een tupel terug, eerste is gevoelde ranking lijst, tweede
     een string met rankig.
    """
    r_lengte = len(r_gekozen_woord)
    r_lijst.append(['', str(r_naam), str(r_lengte), str(r_beurten),
                    str(r_tijd), str(r_punten)])
    r_lijst.sort(key=lambda item: int(item[5]), reverse=True)
    positie = 1
    for item, value in enumerate(r_lijst):
        r_lijst[item][0] = str(positie)
        positie += 1
    r_lijst = r_lijst[0:10]

    return r_lijst


def ranking_wegschrijven(r_ranking, r_kopje):
    file = open('ranking.txt', 'w')

    r_schrijfbaar = []

    for item in r_ranking:
        r_schrijfbaar.append(';'.join(item))

    r_schrijfbaar.insert(0, ';'.join(r_kopje))

    file.write('\n'.join(r_schrijfbaar))
    file.close()


def strip(s_naam):
    s_return = ''
    for letter in s_naam:
        if letter.isalpha():
            s_return += letter
    return s_return


def main():
    ranking_lijst = rankinglijst_maken()
    kopje = ranking_lijst[0]
    scores = ranking_lijst[1:]
    woorden_lijst = lijst_maken()

    speler_naam = ''

    while len(speler_naam) < 1:
        speler_naam = strip(input('Wat is je naam? '))

    opnieuw_vragen = 'doorgaan'
    while opnieuw_vragen == 'doorgaan':
        k_keuze = input('\r\nWat wil je doen?\r\n1. Een woord toevogen'
                        '\r\n2. Het spel spelen\r\n3. De ranking bekijken'
                        '\r\n4. Stoppen\r\n\nMaak je keuze: ')
        if k_keuze.isdigit():

            if int(k_keuze) not in range(1, 5):
                print('Je had verkeerde keuze gemaakt. Probeer het nog eens')
            elif int(k_keuze) == 1:
                toegevoegde_woord = input('Voer een woord in: ').lower()
                woorden_lijst, verkeerde_woord = \
                    woord_toevoegen(toegevoegde_woord, woorden_lijst)
                print(verkeerde_woord)
            elif int(k_keuze) == 2:
                tijd = tijd_tellen()
                woordenlijst = lijst_maken()
                gekozen_woord = choice(woordenlijst)
                gis = []
                woord_geraden = False
                beurt = 0
                geraden_letter = ''
                updated_woord = ''
                letters_gebruikt = [['* * * * * *'], ['* * * * * * * *'],
                                    ['  * * * * * * * * * * * ']]

                print(gekozen_woord)
                letters_vakje = gebruikte_letters(gis)
                updated_woord, woord_geraden = letter_raden \
                    (gis, gekozen_woord)
                galg = galg_maken(beurt)
                '{:>5}{:>41}\n' \
                '{:>5}{:^60}\n' \
                '{:>5}\n' \
                '{:>5}{:^60}\n' \
                '{:>5}\n' \
                '{:>5}{:^60}\n' \
                '{:>5}\n' \
                '\n' \
                '{:^40}\n' \
                '{!s:^40}'.format(
                        'Galgje:', 'Gebruikte letters',
                        galg[0], letters_vakje[0],
                        galg[1],
                        galg, letters_vakje[1],
                        galg[3],
                        galg[4], letters_vakje[2],
                        galg[5],
                        'Het te raden woord:',
                        updated_woord)

                while not woord_geraden and beurt < 10:
                    if beurt < 9:
                        geraden_letter = input(
                                'Type een letter of een word: ')
                        if len(geraden_letter) > 1:
                            woord_geraden, beurt = woord_raden(geraden_letter,
                                                               gekozen_woord,
                                                               beurt)
                        else:
                            gis.append(geraden_letter)
                            updated_woord, woord_geraden = \
                                letter_raden(gis, gekozen_woord)
                            beurt = beurten_tellen(geraden_letter,
                                                   gekozen_woord, beurt,
                                                   letters_gebruikt)
                            letters_gebruikt = gebruikte_letters(gis)

                        print(updated_woord)
                        galg = galg_maken(beurt)
                        for rij in galg:
                            print(rij)
                        print(letters_gebruikt)
                    else:
                        print('test')
                        woord_geraden = True

                tijd2 = tijd_tellen()
                totaale_tijd = round(tijd2 - tijd)
                punten = punten_tellen(totaale_tijd, gekozen_woord, beurt)
                minuten, seconden = divmod(totaale_tijd, 60)
                eind_tijd = "{}m{}s".format(minuten, seconden)
                lijst_ranking = ranking_vullen(scores,
                                               speler_naam, gekozen_woord,
                                               beurt, eind_tijd, punten)
                ranking_wegschrijven(lijst_ranking, kopje)
                print(lijst_ranking)
            elif int(k_keuze) == 3:
                opnieuw_vragen = 'stop'
            elif int(k_keuze) == 4:
                print('Het speel is gestopt')
                sluiten()
                opnieuw_vragen = 'stop'
        else:
            print('Je heb verkeerde keuze gemaakt. Probeer het nog eens.')


main()
