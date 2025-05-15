<h1 align='center'>[ESX] Identity</a></h1><p align='center'><b><a href='https://discord.gg/rnWH528S9W'>Discord</a> - <a href='https://docs.esx-legacy.com/legacy/installation'>Documentation</a></b></h5>

A Core Resource that Allows the player to Pick their characters, Name, Gender, Height and Date-of-birth.

![Preview](![image](https://github.com/user-attachments/assets/80a33a2f-19d9-48c5-b0f1-6f3fad95a851))

# Infomation

## Requirements

[esx_skin](./../esx_skin/README.md)

## Commands

```
/char
/chardel
```

## Legal

esx_identity - Make your Character a Person!

Copyright (C) 2015-2024 Jérémie N'gadi

This program Is free software: you can redistribute it And/Or modify it under the terms Of the GNU General Public License As published by the Free Software Foundation, either version 3 Of the License, Or (at your option) any later version.

This program Is distributed In the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty Of MERCHANTABILITY Or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License For more details.

You should have received a copy Of the GNU General Public License along with this program. If Not, see <http://www.gnu.org/licenses/>.

# ESX Identity avec système de traduction

Ce module permet de créer un personnage avec ses informations d'identité.

## Exemple d'utilisation des traductions

### Dans le script côté serveur ou client

```lua
-- Exemple d'envoi des traductions depuis le côté client ou serveur
-- Vous pouvez appeler ceci au chargement de la ressource ou lorsque l'interface est affichée

local translations = {
    -- Titres et textes généraux
    title = "CRÉATION DE",
    subtitle = "PERSONNAGE",
    description = "Créez votre personnage pour commencer votre aventure dans notre ville.",
    error_title = "Erreur",
    submit_button = "CRÉER MON PERSONNAGE",
    
    -- Labels des champs
    firstname_label = "PRÉNOM",
    lastname_label = "NOM",
    dob_label = "DATE DE NAISSANCE",
    gender_label = "SEXE",
    age_label = "ÂGE",
    height_label = "TAILLE",
    
    -- Placeholders
    firstname_placeholder = "Jean",
    lastname_placeholder = "Dupont",
    dob_placeholder = "JJ/MM/AAAA",
    
    -- Genre
    male = "Homme",
    female = "Femme",
    
    -- Textes police
    police_title = "Département de Police",
    police_description = "Rejoignez les forces de l'ordre pour assurer la sécurité",
    
    -- Messages d'erreur
    firstname_required = "Le prénom est obligatoire",
    firstname_min = "Le prénom doit avoir au moins 3 caractères",
    lastname_required = "Le nom est obligatoire",
    lastname_min = "Le nom doit avoir au moins 3 caractères",
    dob_required = "La date de naissance est obligatoire",
    dob_invalid = "Format de date invalide",
    dob_too_old = "Vous êtes trop vieux pour cette aventure",
    dob_too_young = "Vous devez avoir au moins 18 ans",
    gender_required = "Vous devez sélectionner un genre",
    height_required = "La taille est obligatoire",
    height_min = "La taille minimum est de 120 cm",
    height_max = "La taille maximum est de 220 cm",
    height_invalid = "La taille doit être un nombre"
}

-- Envoi des traductions à l'interface web
SendNUIMessage({
    type = "sendtranslations",
    translations = translations
})
```

## Comment ça fonctionne

1. Le système utilise Vue.js pour créer l'interface utilisateur
2. Les traductions sont injectées via l'event `sendtranslations`
3. Chaque texte dans l'interface peut être personnalisé
4. Si une traduction n'est pas fournie, une valeur par défaut en français est utilisée
