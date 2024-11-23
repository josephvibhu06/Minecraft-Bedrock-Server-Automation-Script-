#!/bin/bash

mcbe() {
    # Navigate to the bedrock-server directory
    cd ~/Downloads/bedrock-server || { echo "Directory not found!"; return; }

    # Check if the worlds directory exists
    if [ ! -d "worlds" ]; then
        echo "No 'worlds' directory found!"
        return
    fi

    # List available worlds by listing subdirectories inside the worlds directory
    echo "Listing available worlds in 'worlds' directory..."
    worlds=$(ls -d worlds/*/ | sed 's|^worlds/||' | sed 's|/||')  # List subdirectories (worlds) inside 'worlds'

    if [ -z "$worlds" ]; then
        echo "No worlds available in the 'worlds' directory!"
        return
    fi

    echo "Available worlds:"
    echo "$worlds"

    # Prompt the user to enter the world name
    read -p "Enter the name of the world: " world_name

    # Trim any leading or trailing whitespace from the user input
    world_name=$(echo "$world_name" | xargs)

    # Check if the selected world exists
    if ! echo "$worlds" | grep -qw "$world_name"; then
        echo "World '$world_name' does not exist!"
        return
    fi

    # Check if the server.properties file exists
    server_properties="server.properties"
    if [ ! -f "$server_properties" ]; then
        echo "server.properties not found!"
        return
    fi

    # Check if the server.properties file is writable
    if [ ! -w "$server_properties" ]; then
        echo "server.properties is not writable!"
        return
    fi

    # Use sed to replace the level-name in the server.properties file
    sed -i "s/^level-name=.*/level-name=$world_name/" "$server_properties"
    if [ $? -eq 0 ]; then
        echo "Updated level-name to '$world_name' in server.properties"
    else
        echo "Failed to update server.properties"
        return
    fi

    # Set the library path and start the server
    echo "Starting the Minecraft server with world '$world_name'..."
    LD_LIBRARY_PATH=. ./bedrock_server

    # After the server stops, back up the world to Google Drive with the current timestamp
    backup_world_to_drive "$world_name"
}

backup_world_to_drive() {
    world_name=$1

    # Define the source world directory and the destination Google Drive directory
    world_directory="worlds/$world_name"
    
    # Generate the timestamp for backup folder name
    timestamp=$(date +"%Y-%m-%d_%H-%M-%S")
    
    # Create a backup directory with the timestamp
    backup_directory="mcbe:/Minecraft_Backups/$world_name-$timestamp/"

    # Check if the world directory exists
    if [ ! -d "$world_directory" ]; then
        echo "World '$world_name' directory does not exist for backup!"
        return
    fi

    echo "Backing up world '$world_name' to Google Drive..."

    # Use rclone to copy the world directory to Google Drive
    rclone copy "$world_directory" "$backup_directory" --progress
    if [ $? -eq 0 ]; then
        echo "Backup of world '$world_name' completed successfully to '$backup_directory'!"
    else
        echo "Failed to backup world '$world_name'."
    fi
}

# Run the function when the script is executed
mcbe
