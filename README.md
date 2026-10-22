# Hitster DIY

Version maison de Hitster sur le thème films, séries et jeux vidéo.
Chaque carte a un QR code qui ouvre une page web jouant le morceau via YouTube, sans rien afficher (ni titre, ni miniature).

## Structure

```
data/cartes.csv          liste de toutes les cartes
docs/index.html          page de lecture, servie par GitHub Pages
docs/cartes.json         généré : ID de carte → ID YouTube + départ
scripts/find_youtube.py  remplit les IDs YouTube automatiquement
scripts/build_json.py    génère docs/cartes.json depuis les CSV
scripts/make_cards.py    génère le PDF des cartes à imprimer
fonts/                   polices DejaVu (licence libre) pour le PDF
```

Colonnes du CSV : `id, oeuvre, type, morceau, artiste, annee, youtube_id, debut_s`.
`id` sur 4 chiffres (0001, 0002...) : les nouvelles cartes continuent la numérotation. `oeuvre` en titre original (ou titre international anglais).
`debut_s` est le temps de départ en secondes (vide = début du morceau).

## Mise en place (une fois)

```bash
pip3 install yt-dlp reportlab
```

GitHub : Settings > Pages > Source « Deploy from a branch », branche `main`, dossier `/docs`.
La page est alors en ligne sur https://louisaldsr.github.io/hitster-diy/

## Cycle pour un nouveau lot de cartes

```bash
python3 scripts/find_youtube.py          # 1. cherche les vidéos (quelques minutes)
#                                          2. vérifie data/verif_youtube.csv, corrige les IDs douteux dans le CSV
python3 scripts/build_json.py            # 3. met à jour docs/cartes.json
git add -A && git commit -m "Cartes 0131-0260" && git push   # 4. publie
python3 scripts/make_cards.py --test     # 5. une planche test à imprimer
python3 scripts/make_cards.py --de 131 --a 260   # 6. le PDF du lot
```

Tester une carte sans l'imprimer : https://louisaldsr.github.io/hitster-diy/?c=0001

## Corriger une carte

Pas besoin de réimprimer : change `youtube_id` ou `debut_s` dans le CSV, relance `build_json.py`, push.
`find_youtube.py --check` repère les vidéos devenues illisibles.

## Impression

- Papier 250 à 350 g, recto/verso **bord long**.
- Le verso est inversé automatiquement pour tomber en face du recto.
- Imprime d'abord une planche test et vérifie l'alignement à contre-jour.
- Couper le long des traits gris dans les marges.
