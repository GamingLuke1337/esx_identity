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

# ESX Identity with translation system

This module allows you to create a character with identity information.

## Example of using translations

### In the server or client script

```lua
-- Example of sending translations from client or server side
-- You can call this when the resource loads or when the interface is displayed

local translations = {
    -- General titles and texts
    title = "CHARACTER",
    subtitle = "CREATION",
    description = "Create your character to start your adventure in our city.",
    error_title = "Error",
    submit_button = "CREATE MY CHARACTER",
    
    -- Field labels
    firstname_label = "FIRST NAME",
    lastname_label = "LAST NAME",
    dob_label = "DATE OF BIRTH",
    gender_label = "GENDER",
    age_label = "AGE",
    height_label = "HEIGHT",
    
    -- Placeholders
    firstname_placeholder = "John",
    lastname_placeholder = "Doe",
    dob_placeholder = "DD/MM/YYYY",
    
    -- Gender
    male = "Male",
    female = "Female",
    
    -- Police texts
    police_title = "Police Department",
    police_description = "Join law enforcement to ensure safety",
    
    -- Error messages
    firstname_required = "First name is required",
    firstname_min = "First name must have at least 3 characters",
    lastname_required = "Last name is required",
    lastname_min = "Last name must have at least 3 characters",
    dob_required = "Date of birth is required",
    dob_invalid = "Invalid date format",
    dob_too_old = "You're too old for this adventure",
    dob_too_young = "You must be at least 18 years old",
    gender_required = "You must select a gender",
    height_required = "Height is required",
    height_min = "Minimum height is 120 cm",
    height_max = "Maximum height is 220 cm",
    height_invalid = "Height must be a number"
}

-- Send translations to the web interface
SendNUIMessage({
    type = "sendtranslations",
    translations = translations
})
```

## How it works

1. The system uses Vue.js to create the user interface
2. Translations are injected via the `sendtranslations` event
3. Each text in the interface can be customized
4. If a translation is not provided, a default value in English is used
