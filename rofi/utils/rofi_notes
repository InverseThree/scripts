#!/bin/bash

AUTHOR="Invers∃"
NOTES_FOLDER="$HOME/Documents/notes"
placeholder=$(date +%F_%H-%M-%S)

while getopts ":a:e:o:" opt; do
  case $opt in
    a) AUTHOR="$OPTARG"
    ;;
    e) EDITOR="$OPTARG"
    ;;
    o) NOTES_FOLDER="$OPTARG"
    ;;
    \?) echo "Invalid option -$OPTARG" >&2
    ;;
  esac
done

if [[ ! -d "${NOTES_FOLDER}" ]]; then
    mkdir -p "$NOTES_FOLDER"
fi

get_notes() {
    ls "${NOTES_FOLDER}"
}

edit_note() {
    note_location=$1
    setsid -f "$TERMINAL" -e "$EDITOR" "$note_location"
}

delete_note() {
    local note=$1
    local action=$(echo -e "Yes\nNo" | rofi -dmenu -p "Are you sure you want to delete $note? ")

    case $action in
        "Yes")
            rm "$NOTES_FOLDER/$note"
            main
            ;;
        "No")
            main
    esac
}

note_context() {
    local note=$1
    local note_location="$NOTES_FOLDER/$note"
    local action=$(echo -e "Edit\nDelete" | rofi -dmenu -p "$note > ")
    case $action in
        "Edit")
            edit_note "$note_location"
            ;;
        "Delete")
            delete_note "$note"

    esac
}

new_note() {
    local title=$(echo -e "New\nCancel" | rofi -dmenu -p "Input title: ")

    case "$title" in
        "Cancel")
            main
            ;;
        "New")
            local file=$(echo $placeholder | sed 's/ /_/g;s/\(.*\)/\L\1/g')
            title=$placeholder
            new_file
            ;;
        *)
            local file=$(echo "$title" | sed 's/ /_/g;s/\(.*\)/\L\1/g')
            new_file
    esac
}

new_file() {
local template=$(cat <<- END

---
title: $title
date: $(date --rfc-3339=seconds)
author: $AUTHOR
---

# $title
END
            )

            note_location="$NOTES_FOLDER/$file.md"
            if [ "$title" != "" ]; then
                echo "$template" > "$note_location" | edit_note "$note_location"
            fi

}

main()
{
    local all_notes="$(get_notes)"
    local first_menu="New note"

    if [ "$all_notes" ];then
        first_menu="New note\n\n${all_notes}"
    fi

    local note=$(echo -e "$first_menu"  | rofi -dmenu -p "Note: ")

    case $note in
        "New note")
            new_note
            ;;
        "")
            ;;
        *)
            note_context "$note"
    esac
}


main
