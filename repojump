#!/bin/bash

CONFIG_FILE="$HOME/.repojump"

function set_config() {
    echo "Enter the full path where your GitHub repos are stored:"
    read -r REPOPATH

    # Remove trailing slash if present
    REPOPATH="${REPOPATH%/}"

    if [[ ! -d "$REPOPATH" ]]; then
        echo "Error: '$REPOPATH' is not a valid directory."
        exit 1
    fi

    echo "REPOPATH=$REPOPATH" > "$CONFIG_FILE"
    echo "RepoJump configured successfully."
}

function load_config() {
    if [[ ! -f "$CONFIG_FILE" ]]; then
        echo "Config not found. Run 'repojump config' first."
        exit 1
    fi
    source "$CONFIG_FILE"
}

function find_repo_path() {
    local target="$1"
    local found_path=""

    # Look for a direct match in root
    for dir in "$REPOPATH"/*; do
        if [[ -d "$dir" && "$(basename "$dir")" == "$target" ]]; then
            if [[ -d "$dir/.git" ]]; then
                found_path="$dir"
                break
            fi
        fi
    done

    # Look for a match in subdirectories (organization-style)
    if [[ -z "$found_path" ]]; then
        for org in "$REPOPATH"/*; do
            [[ -d "$org" ]] || continue
            for sub in "$org"/*; do
                if [[ -d "$sub" && "$(basename "$sub")" == "$target" && -d "$sub/.git" ]]; then
                    found_path="$sub"
                    break 2
                fi
            done
        done
    fi

    echo "$found_path"
}

function jump_to_repo() {
    load_config
    local repo_name="$1"
    local target_path
    target_path=$(find_repo_path "$repo_name")

    if [[ -z "$target_path" ]]; then
        echo "Error: Repository '$repo_name' not found or doesn't contain a .git folder."
        exit 1
    fi

    echo "Jumping to: $target_path"
    cd "$target_path" || exit 1
    $SHELL
}

# --- Main Execution ---

if [[ "$1" == "config" ]]; then
    set_config
elif [[ -z "$1" ]]; then
    echo "Usage: repojump <repo-name>"
    exit 1
else
    jump_to_repo "$1"
fi
